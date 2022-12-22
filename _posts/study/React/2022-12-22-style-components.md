---
layout: post
title: "[Styled Comonents] 리액트는 HTML과 JS를 한 파일에 같이 쓸 수 있..어? CSS는요?"
subtitle: "사실 나도 잘 모르지만...!"
category: Study
tags: react, study
image:
  path: /assets/img/2022-12-21/algorithm_thumnail.gif
---
* this unordered seed list will be replaced by the toc
{:toc}

<!--more-->

```JavaScript
import styled from "styled-components";
import { createGlobalStyle } from "styled-components"

const GlobalStyle = createGlobalStyle`
    button{
      margin: 0.5rem;
    }
`
const Practice = styled.button`
  padding: 1rem;
  font-size: 2rem;
  background: powderblue;
  border-radius: 1rem;
  transition: 0.5s;
  &:hover{  
    background-color : yellow;
    color : black
  }
`;

export default function App() {
  return (
    <>
      <GlobalStyle />
      <Practice>Practice!</Practice>;
    </>
  )
}

```
![결과물은](/assets/img/2022-12-22/practice.gif)