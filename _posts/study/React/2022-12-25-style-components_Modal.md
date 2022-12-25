---
layout: post
title: "React로 Modal창을 띄워보자"
subtitle: "사실 나도 잘 모르지만...!"
category: Study
tags: study, react
image:
  path: /assets/img/2022-12-25/1225react.jpg
---

* this unordered seed list will be replaced by the toc
{:toc}

<!--more-->

모달창을 만들면서 잘 몰랐던 부분들을 코드 안에 주석을 넣으며 작성해보았다.  
머릿 속에 구멍난 개념들을 보충하는데 도움이 되기를 바란다.

```JavaScript

export const Modal = () => {
  const [isOpen, setIsOpen] = useState(false);

  const openModalHandler = () => {
    //isOpen의 상태를 변경하는 메소드를 구현합니다.
    //함수 setIsOpen -> isOpen의 기본값은 false => 고로 !초기값을 하면 닫혀있던게 열리겠지!(!isOpen)
    setIsOpen(!isOpen)
  };
```

## useState?
```JavaScript
const[상태값 저장 변수, 상태값 갱신 함수] = useState(상태초기값);

const[a, b] = useState(default);
```
a는 ***상태***일 뿐. (상태를 저장했습니다.)  
a의 맨 처음 ***기본값***이 default임. (현재 a의 상태는 default입니다.)  
b는 a(상태)를 바꿔주는 ***함수***입니다. (a의 default값을 바꿔주는 함수를 사용할 수 있습니다.)




```JavaScript
  return (
    <>
      <ModalContainer className='ModalContainer'>
        <ModalBtn 
        className='ModalBtn'
        onClick={openModalHandler}
        // 클릭하면 Modal이 열린 상태(isOpen)를 boolean 타입으로 변경하는 메소드가 실행될 것이다
        // 기본 = !isOpen(은 false), 클릭하면 = isOpen(은 true)
        >
          Open Modal!!!!
          {/*조건부 렌더링을 활용해서 Modal이 열린 상태(isOpen이 true인 상태)일 때는 
          ModalBtn의 내부 텍스트가 'Opened!' 로 Modal이 닫힌 상태(isOpen이 false인 상태)일 때는 
          ModalBtn 의 내부 텍스트가 'Open Modal'이 되도록 구현해보자*/}
        </ModalBtn>
        {/*조건부 렌더링을 활용해서 Modal이 열린 상태(isOpen이 true인 상태)일 때만 모달창과 배경이 뜨게 구현해보자 
        isOpen ->
         ModalBackdrop을 누르면 상태가 바뀌어 창이 닫힌다 + 
         모달창은 영역에서 제외한다 +
        x버튼을 누르면 상태가 바뀌어 창이 닫힌다. (모달창에 텍스트도 넣어주자)*/}
        {isOpen?(
          <ModalBackdrop on Click={openModalHandler}>
            <ModalView onClick={(event)=>{event.stopPropagation()}}>
              <button onClick={openModalHandler}>X</button>
              <div>Hello42s</div>
            </ModalView>
          </ModalBackdrop>
        ):null}
      </ModalContainer>
    </>
  );
};
```

## e.stopPropagation()
1. 배경을 누르면 창이 닫혀야 함
2. 모달창은 배경 안에 존재함
3. 배경을 누르면 && 모달창을 누르면 창이 닫힌다  (이벤트 버블링 발생!)

### 이벤트 버블링?
이벤트는 이벤트 캡처링과 이벤트 버블링으로 나타난다.  
클릭이 발생할 경우 해당 위치에서 이벤트가 발생하고 겹처진 요소를 올라가면서 해당 엘리먼트의 이벤트를 다시 발생시키는 현상  
(ex. 이벤트 범위 클릭 >   
모달창에 겹쳐진 엘리먼트인 백드롭 엘리먼트의 이벤트 발생 >   
모달창의 영역도 백드롭 엘리먼트에 겹쳐지므로 모달창 백드롭 이벤트 발생!  
 => 해당 이벤트 버블링을 막기위해 e.stopPropagation사용하자)

## 결과는 여기에!
![result](/assets/img/2022-12-25/Dec-25-2022%2017-26-02.gif)