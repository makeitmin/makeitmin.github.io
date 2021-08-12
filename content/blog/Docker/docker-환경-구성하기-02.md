---
title: Docker 환경 구성하기 02
date: 2021-01-28 13:08:27
category: Docker
thumbnail: { thumbnailSrc }
draft: false
---

Docker 환경을 구성하는 것에는 2가지 방법이 있다.

1. 직접 환경(이미지)을 생성하는 방법
2. 다른 사람이 만들어 배포한 환경(이미지)를 받아 사용하는 방법

여기에서는 1번을 먼저 설명한다. 2번은 다음 글에서 설명할 예정이다.

## Dockerfile 작성

> Dockerfile 이란?

Dockerfile은 Docker 개발환경을 만들 때 작성하는 명세 같은 파일이다. 우리가 **"Ubuntu OS 위에 Python과 Git이 설치된 환경 필요해!"**라고 Docker에게 말을 해야 Docker가 이미지를 만들어 줄 수 있다. Docker에게 무엇을 설치해야 하는지 말해주는 명령파일이라고 생각하면 된다. Dockerfile을 실행(빌드)하면 다른 사람에게 배포할 수 있는 Docker 이미지가 만들어지는데, 이 안에는 개발환경이 들어있다.

Dockerfile의 이름은 Dockerfile로 정해져 있고, 임의로 변경할 수 없다. 확장자도 없다.

> Dockerfile의 경로 및 준비사항

Dockerfile이 있어야 하는 경로는 따로 정해진 바 없으나, 이후에 Docker 이미지를 만드는 명령어는 반드시 Dockerfile과 같은 경로에서 실행해야 한다. 따라서 WSL2 Ubuntu에서 ```/home``` 밑에 Docker라는 폴더를 새로 만들어주고, 그 안에서 이 작업들을 진행했다. 

> Dockerfile에 사용하는 명령어

이 부분이 가장 어려워서 많이 헤맸다. Dockerfile의 내용 자체가 어렵다기보다는 전체적인 개념 차원에서 바라보는게 어려웠다. shell이나 bash 형태로 명령어를 쓰는 것에 약간의 공포감이 있는 것 같은데, 터미널에 익숙해지도록 노력해야겠다는 생각이 들었다.  
아래 명령은 Docker 폴더에서 Dockerfile을 생성하고 Nano 에디터로 파일을 여는 명령이다.

```bash
touch Dockerfile
nano Dockerfile
```

이번 프로젝트에 최종적으로 배포했던 Dockerfile의 내용이다.

```bash
#1 베이스 이미지
FROM ubuntu:18.04
MAINTAINER Seongmin "seongminha.dev@gmail.com"

#2 환경변수 설정
ENV LANG C.UTF-8
ENV DEBIAN_FRONTEND noninteractive

#3 필요한 프로그램 설치

#3-1 apt 패키지 업데이트
RUN apt-get update && apt-get -y upgrade

#3-2 기본 프로그램 설치
RUN apt-get install -y sudo net-tools wget nano lsof curl gnupg gnupg2 gnupg1

#3-3 Git 설치
RUN apt-get install -y git

#3-4 Python3 및 관련 프로그램 설치
RUN apt-get install -y python3 python3-pip python3-dev
RUN apt-get install -y python3-pandas

#3-5 Node.js 버전 세팅 및 설치
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -y nodejs
RUN apt-get install build-essential

##3-6 MongoDB 세팅
RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv 9DA31620334BD75D9DCB49F368818C72E52529D4
RUN echo "deb [ arch=amd64 ] https://repo.mongodb.org/apt/ubuntu bionic/mongodb-org/4.4 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-4.4.list
RUN apt-get update
RUN apt-get install -y mongodb-org
```

**#1**:  
말 그대로 개발 환경의 베이스가 되는 컨테이너이다. Ubuntu 환경을 쓰기로 했으니 Ubuntu OS 컨테이너를 베이스로 올려주었다.

**#2**:  
Ubuntu 내 기본 유저를 root 유저로 설정했다.

**#3**:  
한글이 깨질 수도 있어서, 언어 설정을 UTF-8로 설정했고, 사용자의 동의를 묻는 질문 때문에 설치가 중간에 끊기지 않도록 shell을 non-interactive로 설정했다.

**#4**:  
/home 디렉토리로 이동한다. cd 명령어와 비슷하다.

**#5**:  
필요한 디렉토리를 생성한다. 프로젝트를 수행할 디렉토리를 만들었다.

**#6**:  
필요한 프로그램을 설치한다. 이 프로그램들은 Ubuntu, 즉 전체에 설치되는 프로그램이다.

**#7**:  
Docker 환경에 처음 들어갈 때 이 디렉토리에서 시작하게 된다.

## Dockerfile 빌드

Dockerfile을 저장하고, 같은 경로에서 아래의 빌드 명령어를 실행하면 Docker 이미지가 생성된다. `masldev/env_masl:2.0` 은 추후에 Docker Hub로 배포하기 위한 Docker 이미지명이다. `[Docker Hub 아이디]/[이미지명]:[버전]` 의 형태로 작성하면 된다. 버전을 작성하지 않으면 `latest`로 자동 지정된다.

```bash
docker build --tag masldev/env_masl:2.0 .
```

다음 명령어로 이미지가 잘 생성되었는지 확인한다.

```bash
docker images
```

## Docker 컨테이너 생성 및 실행

이미지가 잘 생성되었다면, 이 이미지를 쓸 수 있도록 컨테이너로 만들어줘야 한다. `coffee`는 그냥 컨테이너를 부를 이름이다. 마음에 드는 이름으로 바꾸어도 된다.

```bash
sudo docker run -it --name coffee masldev/env_masl:2.0
```

Ubuntu 컨테이너로 접속이 되는 것을 확인하였다면, 여러 명령어를 사용하여 Dockerfile에 적었던 프로그램들이 잘 설치되어 있는지 확인한다.

```bash
git
node -v
npm -v
...
```

다음 명령어로 Ubuntu 컨테이너를 종료한다. MongoDB는 서비스를 확실하게 종료시키지 않고 시스템을 꺼버리면 다음에 켤 때 오류가 발생하니 주의한다.

```bash
exit
```

컨테이너를 다시 시작할 때는 꼭 다음 명령어를 사용한다. `docker start -` 가 아닌 `docker run -` 명령어를 사용하면 컨테이너를 리셋하고 새로 시작하는 것이기 때문에 내부에 작업하고 있던 것들이 전부 날아가니 주의한다.

```bash
docker start coffee
```

컨테이너를 시작한 후 Ubuntu shell에 접속할 때는 다음 명령어를 사용한다.

```bash
docker attach coffee
```

## Docker 이미지 배포

컨테이너까지 잘 실행되는 것을 확인하였다면, 이미지를 모두가 쓸 수 있도록 배포하는 작업을 한다. 다음 명령어로 Docker Hub에 만들어 둔 계정에 로그인한다. Username과 Password를 입력한다.

```bash
docker login
```

로그인에 성공하였다면 다음 명령어로 이미지를 Docker Hub에 배포한다.

```bash
docker push masldev/masl_env:2.0
```

해당 과정이 끝나면 Docker Hub에 이미지가 잘 올라가 있는 것을 확인할 수 있다. 이제 다른 사람들도 이 이미지를 받아서 동일한 개발환경을 사용할 수 있게 된다.  

모든 팀원이 이렇게 만들어 쓰려면 소모되는 노력과 시간이 너무 크다. 다른 사람이 배포한 이미지를 받아 간단하게 개발환경을 구성하는 방법은 다음 글에서 설명한다.

## 참고자료

- 초보를 위한 도커 안내서 ([블로그](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html))

- 도커(Docker) 입문편: 컨테이너 기초부터 서버 배포까지 ([블로그](https://www.44bits.io/ko/post/easy-deploy-with-docker#%EB%8F%84%EC%BB%A4-%ED%97%88%EB%B8%8C%EC%97%90-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%98%AC%EB%A6%AC%EA%B8%B0))

- Docker 이미지 만들기 & 배포하기 ([블로그](https://jgtonys.github.io/blog/2019/08/13/docker-image-upload/))