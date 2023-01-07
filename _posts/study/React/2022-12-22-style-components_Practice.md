---
layout: post
title: "|Storybook| 리액트에 CSS넣는 법은 많다. 하지만"
subtitle: "사실 나도 잘 모르지만...!"
category: Study
tags: study, react
image:
  path: /assets/img/thumbnail/react.png
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