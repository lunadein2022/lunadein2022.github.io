---
layout: post
title: "[Drone] Gitlab과 연동하기"
image: /devops/images/2022-02-13-drone-cicd-gitlab-integration/title.png
description: >
  서비스를 하는데 개발도 중요하지만 배포하는 과정도 그에 못지 않게 중요합니다.
  개발부터 배포/운영까지의 과정을 편리하게 해주는 도구 중 하나인 Drone을 Gitlab과 연동하고 파이프라인을 실행해보겠습니다.
tags: [drone, gitlab, ci, cd]
sitemap: true
---

# [Drone] Gitlab과 연동하기

* toc
{:toc}
## 1. CI/CD가 왜 필요할까?
일반적으로 서비스를 개발할 때 IDE의 도움을 많이 받을겁니다. 예를 들어 `InteliJ`와 같은 고급 IDE들은 프로젝트를 열고 실행 버튼을 누르는것 만으로도 쉽게 서버를 실행할 수 있습니다.

그렇다면 운영 환경을 생각해볼게요. 운영 환경으로 배포를 하려면 `빌드` > `서버로 실행 파일 업로드` > `서버 접속` > `실행` 단계를 거쳐야 합니다. 또한 부하 분산이 적용된 경우에는 분산되어있는 서버의 개수 만큼 반복 작업이 필요합니다. 그리고 서비스의 업데이트를 자주 한다면 번거로울 수 밖에 없겠죠?

CI/CD는 이 과정을 자동화 할 수 있고, 이 과정을 자동화 한다면 개발자는 온전히 개발에만 집중할 수 있겠죠?

![그림 1. Pipeline](/devops/images/2022-02-13-drone-cicd-gitlab-integration/1.png)

그림 1. Pipeline

## 2. 다른 CI/CD 도구들

### Jenkins

 CI/CD에 대해서 아신다면 가장 많이 들어본 도구가 Jenkins일거라 생각합니다. 수 많은 플러그인을 통해 다양한 개발 환경에 대응할 수 있습니다. 파이프라인의 과정을 파라미터 셋팅을 통해 구성할 수도 있고, Groovy 언어를 이용해 좀 더 복잡하게 파이프라인을 구성할 수 있습니다. 서버에 설치하여 구성할 수 있습니다.

### Travis

 Jenkins와 달리 설치하여 사용하지 않습니다. Travis에 요금을 지불하면 Travis의 서버에서 파이프라인을 수행합니다. yaml 파일을 통해 파이프라인을 구성할 수 있습니다.

### Github Action

 Github을 사용한다면 쉽게 Github Action을 구성할 수 있습니다. Docker 기반으로 동작하며 각 Step은 Docker로 구현된 Action으로 실행됩니다. yaml으로 구성할 수 있습니다.

### Gitlab CI/CD

 Gitlab은 서버에 설치하여 구성할 수 있는 remote git repository를 제공합니다. Gitlab은 이벤트가 발생하면  CI/CD 파이프라인을 실행하는 환경을 제공하고 있습니다.

## 3. Drone을 선택한 이유

 현재 제가 다니고 있는 회사에서는 Jenkins와 Gitlab CI/CD를 함께 사용하고 있습니다. 그리고 제 개인적으로진행하는 프로젝트에서는 Github Action을 사용하고 있었습니다.

 Jenkins는 빌드와 배포 용도로 사용하고 있습니다. 저희 서비스가 Node.JS로 개발되었다면 jenkins가 설치된 서버에 Node.JS를 설치하고 jenkins에서 셋팅을 해주어야 합니다. 또한 파이프라인을 구성할 때 파라미터 항목이 많아 복잡했습니다.

 Gitlab CI/CD은 MergeRequest와 Release Note를 자동으로 생성하는 용도로 사용하고 있는데, Jenkins에 비해 플러그인이 부족해 파이프라인을 한땀 한땀 구성해야 했습니다.

 Github Action은 Docker 기반으로 만들어진 Action으로 실행되기 때문에 Node.JS 빌드가 필요하다면 yaml에 Node.JS 관련 액션을 실행 하겠다고 명시만 해주면 Action이 실행될 때 관련 이미지를 다운받아 실행됩니다. 따라서 별도의 환경 셋팅이 필요없습니다. 또한 Action별로 어떤 파라미터를 어떻게 사용하는지 문서화가 잘 되어있었습니다. 그리고 Github이라는 초 거대 공룡 오픈소스 플랫폼에서 제공하기 때문에 이미 많은 개발자들이 웬만한 Action을 다 만들어 두었습니다. 때문에 따로 Action을 개발하지 않더라도 yaml을 잘 구성하면 원하는 파이프라인 동작을 구성할 수 있습니다.

 Github Action이 개인적으로 저에게 잘 맞았습니다. 하지만 Github Action은 Github을 사용하지 않으면 사용할 수 없습니다. 제가 진행중인 사이드 프로젝트 소스가 상용화 가능성이 있기 때문에 Github에 push할 수 없었습니다. 그래서 Gitlab 서버를 구축하여 사용하고 있어 Github Action은 더 이상 사용할 수 없게 되었고, Jenkins과 Gitlab CI/CD는 마음에 들지 않아 사용하고 싶지 않았습니다.

 Gitlab을 설치하고 관리자 설정을 둘러보다가 `설정` > `연동` 에서 Drone을 발견했습니다. 저는 처음 들어보는 도구였기 때문에 Google에 어떤 것인지 찾아보고 **Github Action과 유사한 방식으로 파이프라인을 구성할 수 있다는 점**이 마음에 들어서 바로 설치하게 되었습니다.

![그림 2. Gitlab 관리자 영역의 연동 메뉴](/devops/images/2022-02-13-drone-cicd-gitlab-integration/2.png)

그림 2. Gitlab 관리자 영역의 연동 메뉴

## 4. Gitlab과 연동하여 설치하기

 이 글에서 Gitlab은 gitlab.com을 이용합니다. 그리고 **Linux를 기준으로 설명**하며 **Docker는 설치되어있다고 가정**합니다.

먼저 오른쪽 상단의 프로필을 클릭하고 `Preferences`를 선택합니다.

![그림 3. gitlab.com 대쉬보드](/devops/images/2022-02-13-drone-cicd-gitlab-integration/3.png)

그림 3. gitlab.com 대쉬보드

그리고 Applications 메뉴로 이동해서 Application Key를 발급받습니다.

Name은 알아볼 수 있게 지정해주시고 **Redirect URI**는 `droneurl/login` 과 같이 지정해줍니다. 아래 사진에서는 localhost:8000으로 지정하였지만 **외부에서 접근 가능한 URL을 입력해주시길 바랍니다.** 그리고 아래 항목들을 체크해주시고 `Save application` 버튼을 눌러 발급을 완료합니다. 

![그림 4. 사용자 설정의 Application 메뉴](/devops/images/2022-02-13-drone-cicd-gitlab-integration/4.png)

그림 4. 사용자 설정의 Application 메뉴

저장을 하면 `Application ID`와 `Secret`이 발급된 것을 볼 수 있습니다. 저에게 발급된 키는 아래와 같습니다. 저장해 두고, 안전한 곳에 보관하도록 하고 절대 유출되지 않도록 주의하시길 바랍니다. 저는 튜토리얼 작성을 완료하고 파기할 예정입니다.

Application ID: `342034f168fd9bca3bc21158a9fed6bdec37341eb38e7fef3de1e4f344df4e16`

Secret: `398a0fb55b4cb0200454971ff9eda353a247af92d95834914367d4a9a7c62753`

![그림 5. Application Key가 발급된 모습](/devops/images/2022-02-13-drone-cicd-gitlab-integration/5.png)

그림 5. Application Key가 발급된 모습

그리고 터미널에서 다음 명령어를 통해 secret key를 발급합니다..

```bash
openssl rand -hex 16
```

출력된 키는 저장해 두고, 안전한 곳에 보관하도록 합니다. 제게 발급된 키는 `a944a33a742ed7196fc1dba0fe11c611` 입니다. 마찬가지로 외부에 유출되지 않도록 주의합니다.

macOS에서 docker.sock 파일의 권한 문제를 해결하기 위해 아래 명령어를 입력해 docker.sock의 링크를 생성하고 소유자를 변경합니다.

```bash
# docker.sock의 링크 생성
sudo ln -s /var/run/docker.sock ~/.docker/docker.sock
# 심볼릭 링크의 소유자 변경
sudo chown -h [소유자]:[그룹] ~/.docker/docker.sock
```

아래와 같이 docker-compose.yml 파일을 생성합니다.

```yaml
version: "3"

services:
  drone_postgres:
    container_name: drone_postgres
    image: postgres:12.5-alpine
    environment: 
      POSTGRES_DB: postgres
      POSTGRES_USER: testuser
      POSTGRES_PASSWORD: testpwd

  drone:
    container_name: drone
    image: drone/drone:2.0.2
    depends_on: 
      - drone_postgres
    restart: always

    environment:
      DRONE_DATABASE_DRIVER: postgres
      DRONE_DATABASE_DATASOURCE: postgres://testuser:testpwd@drone_postgres:5432/postgres?sslmode=disable
      DRONE_SERVER_PROTO: http
      DRONE_SERVER_HOST: [HOST_URL]
      # 생성된 16자리 Secret Hash
      DRONE_RPC_SECRET: a944a33a742ed7196fc1dba0fe11c611
      DRONE_GITLAB_SERVER: https://gitlab.com
      # Gitlab Application ID
      DRONE_GITLAB_CLIENT_ID: 342034f168fd9bca3bc21158a9fed6bdec37341eb38e7fef3de1e4f344df4e16
      # Gitlab Application Secret
      DRONE_GITLAB_CLIENT_SECRET: 398a0fb55b4cb0200454971ff9eda353a247af92d95834914367d4a9a7c62753

    ports:
      - 8000:80

  drone-runner:
    container_name: drone-runner
    image: drone/drone-runner-docker:latest
    restart: always
    volumes:
      - /var/run:/var/run
      - ~/.docker/config.json:/root/.docker/config.json
    ports:
      - "3300:3000"
    environment:
      DRONE_TRACE: "false"
      DRONE_DEBUG: "false"
      DRONE_RPC_PROTO: http
      DRONE_RPC_HOST: drone
      # 생성된 16자리 Secret Hash
      DRONE_RPC_SECRET: a944a33a742ed7196fc1dba0fe11c611
      # 동시에 실행할 수 있는 파이프라인
      DRONE_RUNNER_CAPACITY: 2
      DRONE_RUNNER_NAME: developer_pc
      DRONE_DOCKER_CONFIG: /root/.docker/config.json
```

터미널에서 다음 명령어를 입력해 drone 실행을 완료합니다.

```bash
cd [docker-compose.yml이 있는 디렉토리]
docker compose up -d
```

Docker compose가 실행된 후 입력한 도메인으로 접속하면 아래와 같은 화면이 표시됩니다. CONTINUE를 눌러 진행합니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/6.png)

Gitlab에서 로그인 후 Authorize를 누릅니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/7.png)

Drone에 정보를 등록하여 Drone 초기 셋팅을 완료합니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/8.png)

## 5. 파이프라인 실행하기

Drone이 정상적으로 실행되는지 테스트를 하기 위해 다음과 같이 Gitlab에 빈 리포지토리를 생성하였습니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/9.png)

리포지토리 메뉴에서 `Settings` > `Integrations` 선택해주시고  `Drone`을 선택합니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/10.png)

**Token에는 명령어로 생성한 secret key**를 입력해주시고 Drone server URL에는 외부에서 접근 가능한 URL을 입력해주시고 Save changes를 눌러 저장합니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/11.png)

그리고 Drone 페이지로 돌아와서 Sync 버튼을 누르면 내 리포지토리가 드론에 동기화 된 것을 볼 수 있습니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/12.png)

Drone을 사용할 리포지토리를 선택한 뒤 ACTIVE REPOSITORY 버튼을 누르면 이 리포지토리는 드론이 활성화 됩니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/13.png)

이제 파이프라인을 구성해보겠습니다. 다음 명령어를 실행해 리포지토리를 클론받고 .drone.yml을 생성합니다.

```bash
# 리포지토리 클론
git clone https://gitlab.com/[사용자]/[리포지토리이름].git
# 리포지토리로 이동
cd [리포지토리이름]
# .drone.yml 생성
touch .drone.yml
```

그리고 아래 코드를 .drone.yml에 붙여넣고 저장, 커밋 gitlab에 푸시를 합니다. 아래 파이프라인은 golang을 image로 Hello World를 출력합니다.

```yaml
---
kind: pipeline
type: docker
name: drone-test

steps:
  - name: Test Run Go Step
    image: golang
    environment:
      GOS: linux
      GOARCH: amd64
      CGO_ENABLED: 0
    commands:
    - echo "Hello World"
    
trigger:
  branch:
    - master
  event:
    - push
```

Drone 대쉬보드로 이동하면 파이프라인이 정상적으로 실행된 것을 볼 수 있습니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/14.png)

그리고 실행된 파이프라인의 로그를 확인하면 `Hello World`가 정상적으로 출력된 것을 확인할 수 있습니다.

![](/devops/images/2022-02-13-drone-cicd-gitlab-integration/15.png)