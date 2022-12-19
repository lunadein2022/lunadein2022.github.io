---
layout: post
title: "[Spring] 사이드 프로젝트 기술 스택 결정하기"
description: >
  Spring Data Redis에는 Object를 Json으로 변환할 수 있는 Serializer를 제공합니다. Redis를 캐시 저장소로 사용할 때에도 이 Serializer를 이용하는데요. 이 Serializer를 이용해 List 타입을 반환하는 메소드에 캐시를 적용하면 예외가 발생합니다.
tags: [backend, redis, cache, spring, kotlin-serialization, jackson, gson]
sitemap: true
---

## API Gateway
### Application APIGateway
응용 프로그램의 요청을 APIGateway로 전달

### Admin APIGateway
Admin 작업과 관련된 요청을 APIGateway로 전달

### API Gateway
Application APIGateway와 Admin APIGateway의 요청을 서비스로 전달

## Discovery, Config
### Consul
서비스 디스커버리, 클라우드 Config 저장소로 사용

## Messaging
### Kafka
Event-Driven을 위한 메세지 서비스

## Logging
### FluentD
New Relic의 리전이 미국에 존재하므로 서비스가 갑자기 종료되면 로그가 소실될 가능성이 있다. 따라서 FluentD로 로그를 모은 뒤 New Relic으로 전송하는 방식으로 사용

## Service
### User
사용자를 관리하는 서비스.

### Auth
인증을 처리하는 서비스

### Studio
스튜디오를 관리하는 서비스

### API
API 서비스

### File
파일을 저장
파일의 권한 관리(웹 애플리케이션을 개발할 때 추가)

### Admin
서비스를 관리

### Product
상품 관리

### Crawlling
크롤링 배치 시스템을 관리/실행

### Mail
메일 전송 및 템플릿 관리

### SMS
SMS 전송 및 템플릿 관리

### Push
푸시 전송 및 템플릿 관리

