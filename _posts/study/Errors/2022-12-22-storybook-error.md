---
layout: post
title: "[Storybook] Can't resolve '@mdx-js/react'"
subtitle: "사실 나도 잘 모르지만...!"
category: Study
tags: study, errors
image:
  path: /assets/img/2022-12-21/algorithm_thumnail.gif
---
* this unordered seed list will be replaced by the toc
{:toc}

## Error!

리액트, 스토리북을 <span style='color: darkblue; background-color: yellow' >처음</span> 사용하다가

**Module not found: Can't resolve '@mdx-js/react' in '/Users/codestates/CodeStates_Project/section3/1222/storybook-practice/src/stories' src/stories/Introduction.stories.mdx**

라는 에러가 났다.

<!--more-->

무언가.. 버전에러같은데 자세히 알 수는 없었다..
(나의 이해력이 아직 따라주지 않는 것 같다..!)
하지만 일단은 해결했다.


## How?

```
npm i @mdx-js/react@^1.6.22 -D --legacy-peer-deps
```
깃허브 현자분들의 가르침을 받아 위의 코드를 터미널에 (당연히 파일이 위치한 곳에서)입력했더니.. 된다!!






---
참고 : [MDX2: Can't resolve '@mdx-js/react' #18094](https://github.com/storybookjs/storybook/issues/18094)