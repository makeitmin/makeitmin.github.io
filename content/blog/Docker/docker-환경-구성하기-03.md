---
title: Docker 환경 구성하기 03
date: 2021-01-29 14:08:14
category: Docker
thumbnail: { thumbnailSrc }
draft: false
---

## Docker 이미지 받아오기

이전 글에서 생성했던 Docker 이미지, 그러니까 프로젝트 팀원들에게 배포하기 위해 생성한 Docker 이미지는 Docker Hub의 [개인 레포지토리](https://hub.docker.com/repository/docker/seongminha/masl)에 있다. 이 이미지를 끌어와서 간단하게 동일한 개발환경을 세팅할 수 있다.

```shell
docker pull seongminha/masl:2.0
```

다음 명령어로 이미지가 잘 가져와졌는지 확인한다.

```shell
docker images
```

## Docker 컨테이너 생성 및 실행

이미지를 잘 가져왔다면, 이 이미지를 쓸 수 있도록 컨테이너로 만들어줘야한다. `coffee`는 그냥 컨테이너를 부를 이름이다. 마음에 드는 이름으로 바꾸어도 된다.

```shell
sudo docker run -it --name coffee seongminha/masl:2.0
```

Ubuntu 컨테이너로 접속이 되는 것을 확인하였다면, 여러 명령어를 사용하여 프로그램들이 잘 설치되어 있는지 확인한다.

```shell
git
node -v
npm -v
...
```

다음 명령어로 Ubuntu 컨테이너를 종료한다.

```shell
exit
```

컨테이너를 다시 시작할 때는 꼭 다음 명령어를 사용한다. `docker run -` 명령어를 사용하면 컨테이너를 리셋하고 새로 시작하는 것이기 때문에 내부에 있던 작업물이 전부 날아가니 주의한다.

```shell
docker start coffee
```

컨테이너를 시작한 후 Ubuntu shell에 접속할 때는 다음 명령어를 사용한다.

```shell
docker attach coffee
```

## Visual Studio Code에서 Docker 컨테이너 사용하기

아무래도 계속 shell에서 개발을 하는 것은 무리가 있다. Visual Studio Code에 Docker 컨테이너를 붙여서 개발환경을 로컬처럼 만들어보자.

1. Visual Studio Code에서 **Remote Container Extension**과 **Docker Extension**, **Docker Explorer Extension**을 각각 설치해준다.

2. F1을 눌러 `Remote-Containers: Attach to Running Container...` 을 클릭해준다. 이 때 Docker 컨테이너는 start 되어 있는 상태여야 한다.

3. Docker 컨테이너와 Visual Studio Code가 연결되면 왼쪽 하단에 `Container 이미지명 (/컨테이너명)` 이 Remote로 연결되었다고 뜬다.

4. Visual Studio Code에서 Terminal을 열어 workspace 폴더에 프로젝트를 git clone 해준다.

5. 좌측의 Explorer에서 Open Folder로 clone 된 프로젝트 폴더를 열어주면 개발을 진행할 수 있는 환경이 된다.

## 참고자료

- 초보를 위한 도커 안내서 ([블로그](https://subicura.com/2017/01/19/docker-guide-for-beginners-1.html))

- 도커(Docker) 입문편: 컨테이너 기초부터 서버 배포까지 ([블로그](https://www.44bits.io/ko/post/easy-deploy-with-docker#%EB%8F%84%EC%BB%A4-%ED%97%88%EB%B8%8C%EC%97%90-%EC%9D%B4%EB%AF%B8%EC%A7%80-%EC%98%AC%EB%A6%AC%EA%B8%B0))

- VS Code에 Docker 연동하기 ([블로그](https://89douner.tistory.com/m/123))