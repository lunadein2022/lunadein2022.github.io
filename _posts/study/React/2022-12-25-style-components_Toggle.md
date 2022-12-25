---
layout: post
title: "React로 Modal창을 띄워보자"
subtitle: "사실 나도 잘 모르지만...!"
category: Study
tags: study, react
image:
  path: /assets/img/2022-12-25/toggle.jpg
---

* this unordered seed list will be replaced by the toc
{:toc}

<!--more-->

모달창을 만들면서 잘 몰랐던 부분들을 코드 안에 주석을 넣으며 작성해보았다.  
머릿 속에 구멍난 개념들을 보충하는데 도움이 되기를 바란다.

## 기능 구현 목표 = 토글 버튼을 누르면 버튼이 위치를 이동하고 다시 클릭하면 원 위치로 복귀한다. + 아래 텍스트도 같이 바뀐다.


## 트리구조
![modaltree](/assets/img/2022-12-25/toggletree.png){: height="700"}


## CSS
```JavaScript
import { useState } from 'react';
import styled from 'styled-components';

const ToggleContainer = styled.div`
  position: relative;
  margin-top: 8rem;
  left: 47%;
  cursor: pointer;

  > .toggle-container {
    width: 50px;
    height: 24px;
    border-radius: 30px;
    background-color: #8b8b8b;
    transition: 0.5s ease-out; //부드럽게 움직이도록 애니메이션 적용
    // TODO : .toggle--checked 클래스가 활성화 되었을 경우의 CSS를 구현합니다.
    &.toggle--checked{
      background: lightcoral;
      transition: 0.5s;
    }
  }

  > .toggle-circle {
    position: absolute;
    top: 1px;
    left: 1px;
    width: 22px;
    height: 22px;
    border-radius: 50%;
    background-color: #ffffff;
    transition: 0.5s ease-out;
    // TODO : .toggle--checked 클래스가 활성화 되었을 경우의 CSS를 구현합니다.
    &.toggle--checked{
      left: 27px;
      transition: 0.5s;
    }
  }
`;

const Desc = styled.div`
  // TODO : 설명 부분의 CSS를 구현합니다.
  text-align: center;
  margin: 20px;
`;
```


1. Sass문법인 '&'는 부모선택자이며 중첩된 Sass문법 안에서 사용한다. + 가상클래스 및 가상요소에 사용된다.


```JavaScript
export const Toggle = () => {
  const [isOn, setisOn] = useState(false);

  const toggleHandler = () => {
    // TODO : isOn의 상태를 변경하는 메소드를 구현합니다.
    setisOn(!isOn)
  };
  ```

앞선 Modal 예제처럼 기본 값은 false이며 상태가 변경되면 true가 된다.  
=> 상태를 변경하는 함수는 true로 만드는 함수.  
=> 현재 isOn은 false 이므로 !isON은 true가 된다.


```JavaScript
  return (
    <>
      <ToggleContainer className='ToggleContainer'
      // TODO : 클릭하면 토글이 켜진 상태(isOn)를 boolean 타입으로 변경하는 메소드가 실행되어야 합니다.
      >
        {/* TODO : 아래에 div 엘리먼트 2개가 있습니다. 각각의 클래스를 'toggle-container', 'toggle-circle' 로 지정하세요. */}
        {/* TIP : Toggle Switch가 ON인 상태일 경우에만 toggle--checked 클래스를 div 엘리먼트 2개에 모두 추가합니다. 조건부 스타일링을 활용하세요. */}
        <div onClick={toggleHandler} className={`toggle-container${isOn ? " toggle--checked" : ""}`} />
        <div onClick={toggleHandler} className={`toggle-circle${isOn ? " toggle--checked" : ""}`} />
      </ToggleContainer>
      <Desc className='Desc'>
        {isOn ? 'Toggle Switch ON' : 'Toggle Switch OFF'}
      </Desc>
      {/* TODO : Desc 컴포넌트를 활용해야 합니다. */}
      {/* TIP:  Toggle Switch가 ON인 상태일 경우에 Desc 컴포넌트 내부의 텍스트를 'Toggle Switch ON'으로, 그렇지 않은 경우 'Toggle Switch OFF'가 됩니다. 조건부 렌더링을 활용하세요. */}
    </>
  );
};
```

## JSX

상태가 변할 수 있는 두 개의 컨테이너가 있다.  
1. toggle-container
2. toggle-circle

두 상태가 변했을 때 적용될 CSS는 작성되어 있다.
1. toggle-container = 색상변경
2. toggle-circle = 위치변경

해 주어야 할 것은
1. 각 컨테이너를 클릭했을 때 상태 변경 함수가 동작할 것.
2. 함수가 동작했을 때 상태변경이 눈에 보이도록 (CSS를 적용할 수 있도록) 조건별 className을 설정해줄 것.

### $?
클래스네임 작성시 적어주는 $는 템플릿 리터럴..내에서 표현식을 사용할 수 있게 한다.  
그래서 위의 작성에 따르면
```JavaScript
className={`toggle-container${isOn ? " toggle--checked" : ""}`} 

className = 'toggle-container toggle--checked'
```
가 되는 것이다.

## 결과는 여기에!
![result](/assets/img/2022-12-25/1225toggle.gif)