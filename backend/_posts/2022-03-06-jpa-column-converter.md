---
layout: post
title: "[Spring/JPA] JPA에서 암호화 칼럼 암/복호화 하기"
image: /backend/images/2022-03-06-jpa-column-converter/title.png
description: >
  제가 담당하고 있는 서비스는 원래 MyBatis를 사용하고 있었고, 일부 칼럼이 Database Function으로 암호화를 하고 있었습니다. JPA에서는 이러한 암호화가 적용된 칼럼의 값을 어떻게 가져오고 저장할 수 있을까요?
tags: [backend, spring, jpa, column-converter, column-transformer]
sitemap: true
---

# [Spring/JPA] JPA에서 암호화 칼럼 암/복호화 하기

* toc
{:toc}

마켓찐 서비스는 MyBatis를 사용하고 있습니다.

다른 회사가 개발하던 것을 제가 이어서 개발하게 되었는데, 새로운 기능을 추가할 때나 유지보수를 할 때 몇 십 줄이 넘어가는 쿼리를 보고 있자면 정신이 아득해져요 ㅎㅎ...

그래서 **새로 개발하는 API들은 JPA를 사용**하기로 결정을 했습니다.

새로 추가되는 테이블이 기존 테이블과 관계를 맺었기 때문에 해당 테이블도 `엔티티`로 만들어줄 필요가 있었습니다.

이 과정에서 발생한 문제를 해결하는 과정을 포스트로 남깁니다.

## JPA로 전환하는 과정의 문제
기존에 MyBatis를 사용하면서 사용자 비밀번호나 개인정보의 암호화를 `Database Function`으로 처리하고 있었습니다.

그래서 JPA Repository로 엔티티를 가져올 때 칼럼의 값을 복호화 하고, 저장할 때 칼럼의 값을 암호화 하는 방법이 필요했습니다.

JPA에서는 이를 처리할 수 있는 방법을 두 가지 제공합니다!!

1. ColumnTransformer
2. AttributeConverter

처음에는 간편한 `ColumnTransformer`를 이용해 처리를 했는데요. 테스트를 하던 중 큰 문제가 있어서 `AttributeConverter`로 변경했습니다.

**ColumnTransformer**와 **AttributeConverter**가 무엇인지 왜 ColumnTransformer를 사용하지 않고 AttributeConverter를 사용하게 되었는지 이야기 해드릴게요.

## ColumnTransformer
ColumnTransformer는 엔티티의 필드에 사용하는 **Annotation**이에요.

사용 방법은 아래와 같아요.
```java
@ColumnTransformer(
  read = "FUNCTION_DECRYPT(MOBILE_NUMBER)",     // 칼럼 값을 가져올 때
  write = "FUNCTION_ENCRYPT(?)"                 // 칼럼 값을 쓸 때
)
private String mobileNumber;
```

코드만 보셔도 감이 오지 않나요?

JPA에서 해당 칼럼을 **SELECT** 하는 쿼리에 read를 그대로 넣어줘요. 그리고 **INSERT**나 **UPDATE** 하는 쿼리에는 write 를 넣어주되 ?에 저장할 값을 채워줍니다!

굉장히 간단한 방법이지만 치명적인 문제가 있었습니다.

1. 특정 Database에 종속됩니다.
   - 다른 데이터베이스로 변경하기 어려워집니다.
   - 테스트 용으로 H2 Database를 사용하기 어려워집니다.
2.  두 테이블을 JOIN 하여 **SELECT** 할 때 칼럼 이름이 중복된다면 AMBIGUOUS 문제가 발생합니다. SELECT 문에 read의 내용을 그대로 사용하기 때문입니다.

위 두 가지 문제로 인해 ColumnTransformer는 쓸 수 없다는 판단을 내렸고, 좀 더 찾아본 결과 **AttributeConverter** 라는 것이 있다는걸 알게 되었습니다.

## AttributeConverter
ColumnTransformer는 Database 쿼리로 문제를 해결하지만 AttributeConverter는 Java Code에서 문제를 해결하는 방법입니다.

AttributeConverter는 다음과 같이 사용합니다.
1. AttributeConverter<X, Y>를 implement 하여 엔티티에 맵핑된 값을 어떻게 변환할 것인지 코드로 작성을 하고, `@Converter` Annotation을 사용합니다.
2. 변환이 필요한 엔티티 필드에 **@Convert** Annotation으로 Converter를 지정합니다.

아래 예제는 MySQL의 `AES_ENCRYPT`, `AES_DECRYPT` 함수를 사용해 암호화, 복호화 하는 칼럼에서 사용 할 수 있는 Converter 입니다.

```java
import org.apache.commons.codec.binary.Hex;
import org.springframework.beans.factory.annotation.Value;

import javax.annotation.PostConstruct;
import javax.crypto.Cipher;
import javax.crypto.spec.SecretKeySpec;
import javax.persistence.AttributeConverter;
import javax.persistence.Converter;
import java.io.UnsupportedEncodingException;
import java.nio.charset.StandardCharsets;

@Converter
public class ColumnEncryptor implements AttributeConverter<String, String> {

    // 암호화 키
    @Value("${spring.database.column.encrypt.key:}")
    private String key;

    private Cipher encryptCipher;
    private Cipher decryptCipher;

    @PostConstruct
    public void init() throws Exception{
        encryptCipher = Cipher.getInstance("AES");
        encryptCipher.init(Cipher.ENCRYPT_MODE, generateMySQLAESKey(key, "UTF-8"));
        decryptCipher = Cipher.getInstance("AES");
        decryptCipher.init(Cipher.DECRYPT_MODE, generateMySQLAESKey(key, "UTF-8"));
    }

    /**
     * 필드의 값을 데이터베이스 칼럼 값으로 변환하는 코드를 여기 작성합니다.
     */
    @Override
    public String convertToDatabaseColumn(String attribute) {
        try {
            return new String(Hex.encodeHex(encryptCipher.doFinal(attribute.getBytes(StandardCharsets.UTF_8)))).toUpperCase();
        } catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    /**
     * 칼럼 값을 필드의 값으로 변환하는 코드를 여기 작성합니다.
     */
    @Override
    public String convertToEntityAttribute(String dbData) {
        try {
            return new String(decryptCipher.doFinal(Hex.decodeHex(dbData.toCharArray())));
        }catch (Exception e) {
            e.printStackTrace();
        }
        return null;
    }

    public static SecretKeySpec generateMySQLAESKey(final String key, final String encoding) {
        try {
            final byte[] finalKey = new byte[16];
            int i = 0;
            for(byte b : key.getBytes(encoding))
                finalKey[i++%16] ^= b;
            return new SecretKeySpec(finalKey, "AES");
        } catch(UnsupportedEncodingException e) {
            throw new RuntimeException(e);
        }
    }
}
```

그리고 엔티티에서는 아래와 같이 사용하면 됩니다.

```java

public class Member {
  ...

  @Convert(converter = ColumnEncryptor.class)
  private String mobileNumber;

  ...
}
```

## 마치며
이번 포스트에서는 **ColumnTransformer**와 **AttributeConverter**에 대해서 알아보았습니다.

MyBatis에서 JPA로 전환화는 과정에서 칼럼 암호화 처리를 해결하는 방법이 있었습니다.

AttributeConverter는 위와 같이 칼럼 암호화가 아니더라도 **Boolean을 VARCHAR(1) 타입의 YN 으로 변환** 한다거나 **Enum을 코드**로 변환 할 때에도 활용 할 수 있습니다.

개인적인 생각으로는 SQL 코드는 특정 데이터베이스에만 있는 함수, 또는 문법이 있을 수 있고 디버깅 하기 어렵기 때문에 가능하면 Java, Kotlin 코드로 처리하는 것이 좋다고 생각합니다.

따라서 ColumnTransformer 보다는 AttributeConverter를 사용하는 것을 권장하고 싶네요.