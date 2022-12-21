---
layout: post
title: "알고리즘 문제 접근법"
subtitle: "사실 나도 잘 모르지만...!"
category: Study
tags: algorithm
image:
  path: /assets/img/2022-12-21/algorithm_thumnail.gif
---

* this unordered seed list will be replaced by the toc
{:toc}

아무리 문제들 들여다봐도,
심지어는 레퍼런스를 봐도!
이해되지 않는 알고리즘, 코딩테스트 문제들..!

접근방법부터 알아보고자 한다.

우선 머리에 새겨야 할 말은 하나다.

> 문제를 풀어라, 그게 어렵다면 작고 간단한 문제부터 풀어라!

<!--more-->

아무리 어려운 문제라도 쪼개고 쪼개고 쪼개서 들어가보면 작은 문제들의 얽히고 설킨 덩어리일 뿐이다. 그냥 그..얽히고 설킨게.. 복잡하게 꼬여있어서 그렇지.. 아무튼 작은 문제부터 시작한다면 해낼 수 있는 용기가 생길 것이다! 자 아래의 내용으로 그 용기에 용기를 더해나가자

<!--more-->

## STEP 1. 문제 이해하기
  1. 나의 언어로 그 문제를 다시 말할 수 있는가?
  2. 문제에 어떤 input을 넣어야 하는지, 어떤 결과를 기대할 수 있는지 파악하고 있는가?
  3. 문제를 해결할 충분한 정보를 갖고 있는가?

## STEP 2. 세분화한 예시
  1. 간단한 예제로 시작하기
  2. 좀 더 복잡한 예제 풀어보기
  3. 아무 것도 입력하지 않았을 땐 어떤 결과가 나오는지 알아보기
  4. 잘못된 입력을 했을 때 어떤 에러가 나는지 등 알아보기
  5. 그 외에 문자, 숫자 등 여러 데이터 타입으로 실험하기

## STEP 3. 세부 분석
  1. 어떻게 풀어야할 지 단계별로 의사코드 적기

## STEP 4. 해결 또는 단순화
  1. 문제를 해결하는데 걸림돌이 되는 부분 (풀이가 어려운 부분)을 과감히 잘라내기
  2. 문제를 단순화해서 풀어보기
  3. 그런 다음 어려운 부분을 다시 넣어 풀기

## STEP 5. 되돌아보고 리팩토링하기
  1. 코드가 작동하는가?
  2. 다른 접근방식은?
  3. 이 풀이를 다른 문제에 사용할 수 있는가?
  4. 풀이 성능 개선할 수 있는가? (시간복잡도 + 공간복잡도)
  5. 다른 리팩터링 방법이 있는가?
  6. 다른 사람들의 풀이는 어떠한가?


### 마지막으로

코드 작성과 리딩에 가장 중요한건 ***효율성***과 ***가독성***이다.