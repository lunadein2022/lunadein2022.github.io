---
layout: post
title: "[Spring] Redis를 DB Cache로 사용할 때 리스트 저장하기"
description: >
  Spring Data Redis에는 Object를 Json으로 변환할 수 있는 Serializer를 제공합니다. Redis를 캐시 저장소로 사용할 때에도 이 Serializer를 이용하는데요. 이 Serializer를 이용해 List 타입을 반환하는 메소드에 캐시를 적용하면 예외가 발생합니다.
tags: [backend, redis, cache, spring, kotlin-serialization, jackson, gson]
sitemap: true
---

# [Spring] Redis를 DB Cache로 사용할 때 리스트 저장하기

* toc
{:toc}

예전에 토이 프로젝트를 진행할 때 `EhCache`로 `Database Cache`를 구현한 적이 있습니다. 당시에는 서버를 이중화하여 사용할 일이 없어서 `Redis`를 캐시 저장소로 사용하지 않았지만, 최근에 Redis를 이용해 Database Cache를 구현 할 일이 생겼습니다. 문제는 EhCache를 이용할 때에는 이상 없이 동작하던 것이 Redis를 사용할 때에는 오류가 발생하는 것이었습니다.

이번 포스트에서 이 문제의 원인이 무엇인지 어떻게 해결하였는지를 써보려고 합니다.

참고로 이번 포스트는 Kotlin으로 테스트하였습니다.

## Redis, Srping 설정
### Redis 실행
우선 Redis를 테스트하려면 Redis가 설치되어 있어야겠죠?

저는 Docker compose를 이용해 Redis를 간단하게 설치했어요.

```yml
# docker-compose.yml
version: '3.7'
services:
  redis:
    image: redis:alpine
    command: redis-server --port 6379
    container_name: redis_boot
    hostname: redis_boot
    labels:
      - "name=redis"
      - "mode=standalone"
    ports:
      - 6379:6379

```

`docker-compose.yml` 파일을 만들고 위 내용을 붙여넣은 뒤 해당 파일이 있는 디렉토리에서 아래 명령어를 입력하면 Redis가 Docker 위에서 실행됩니다!

```shell
docker compose up -d
```

### Srping 설정

우선 테스트를 위해 `build.gradle.kts`에 라이브러리 의존성을 다음과 같이 설정하였습니다.

유닛 테스트를 이용할 예정이라 `spring-boot-starter-web`은 추가하지 않았어요.

```kotlin
// build.gradle.kts
dependencies {
    implementation("org.springframework.boot:spring-boot-starter-cache")
    implementation("org.springframework.boot:spring-boot-starter-data-redis")

    // Jackson
    implementation("com.fasterxml.jackson.module:jackson-module-kotlin")
    // Gson
    implementation("com.google.code.gson:gson:2.9.0")
    // Kotlin Serialization
    implementation("org.jetbrains.kotlinx:kotlinx-serialization-json:1.3.2")

    implementation("org.jetbrains.kotlin:kotlin-reflect")
    implementation("org.jetbrains.kotlin:kotlin-stdlib-jdk8")

    testImplementation("org.springframework.boot:spring-boot-starter-test")
}
```

그리고 Redis에 연결할 수 있도록 RedisConnectionFactory를 만들겠습니다.

```kotlin
// RedisConfig.kt

@Configuration
class RedisConfig {

    @Value("\${spring.redis.port}")
    var port = 0

    @Value("\${spring.redis.host}")
    var host: String? = null

    @Bean
    fun redisConnectionFactory(): RedisConnectionFactory {
        val redisStandaloneConfiguration = RedisStandaloneConfiguration()
        redisStandaloneConfiguration.hostName = host!!
        redisStandaloneConfiguration.port = port
        return LettuceConnectionFactory(redisStandaloneConfiguration)
    }

}
```

SampleDto를 만들겠습니다.

```kotlin
// SampleDto.kt

data class SampleDto (
  val name: String = "",
  val number: Int = 0,
)

```

마지막으로 캐상울 적용할 Service를 만들게요.

```kotlin
// TestService.kt

@Service
class TestService {
    
    val logger: Logger get() = LoggerFactory.getLogger(this.javaClass)

    @Cacheable(key = "#id", value = ["one"])
    fun getOne(id: Int): TestData {
        logger.info("호출")
        return TestData(
            str = "$id",
            number = id
        )
    }

    @Cacheable(value = ["multiple"])
    fun getMultiple(): List<TestData> {
        logger.info("호출")
        return (1..10).map {
            TestData(
                str = "$it",
                number = it,
            )
        }
    }
    
}

```


## Redis가 기본적으로 제공하는 Serializer 사용해보기
