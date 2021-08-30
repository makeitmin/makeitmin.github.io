---
title: Docker + Gatsby + GitHub Pages로 블로그 만들기
date: 2021-03-28 15:08:74
category: Project
thumbnail: { thumbnailSrc }
draft: false
---

사실 Docker는 쓸 생각이 없었다. 그냥 로컬에서 작업할 계획이었는데, 기존에 설치된 React를 비롯한 각종 모듈과 Gatsby가 버전 충돌을 일으키며 이틀 밤낮을 붙잡아도 해결될 기미가 보이지 않았다(..) 그래서 Docker를 이용해서 깨끗한 새로운 환경에서 블로그를 만들어 보기로 했다.

블로그를 만들 순서는 다음과 같다.

1. Docker 환경 세팅
2. Gatsby 환경 세팅
3. GitHub Pages 배포

## Docker 환경 세팅

Linux/Unix 환경이 아닌 Windows 환경이라면 WSL2 터미널에서 다음을 수행한다.

```bash
docker pull seongminha/blog:2.0
```

참고로 해당 이미지는 미리 생성해서 Docker Hub에 올려둔 것으로, 최초에 이미지를 생성하기 위해 빌드했던 Dockerfile은 다음과 같다.

```bash
#1 베이스 이미지
FROM ubuntu:18.04
MAINTAINER Seongmin "seongminha0910@gmail.com"

#2 환경변수 설정
ENV LANG C.UTF-8
ENV DEBIAN_FRONTEND noninteractive

#3 필요한 프로그램 설치

#3-1 apt 패키지 업데이트
RUN apt-get update && apt-get -y upgrade

#3-2 기본 프로그램 설치
RUN apt-get update
RUN apt-get install -y sudo net-tools wget nano lsof curl gnupg gnupg2 gnupg1

#3-3 Git 설치
RUN apt-get install -y git

#3-4 Node.js 버전 세팅 및 설치
RUN curl -sL https://deb.nodesource.com/setup_12.x | bash -
RUN apt-get install -y nodejs
RUN apt-get install -y gcc g++ make
```

## Gatsby 환경 세팅

1. 다음과 같이 Docker 이미지를 사용하여 Docker 컨테이너를 생성 및 실행한다.

    ```bash
    docker run -it -p 8000:8000 --name blog seongminha/blog:2.0
    ```

2. 다음 [블로그](https://89douner.tistory.com/123)를 따라 Visual Studio Code에 Docker 환경을 attach 한다.
3. Visual Studio Code의 Terminal에서 다음을 실행하여 Docker 환경이 제대로 세팅되었는지 확인한다.

    ```bash
    node -v # 12.X 버전
    npm -v # 6.X 버전
    ```

4. Yarn과 Gatsby를 설치한다.

    ```bash
    npm install -g yarn
    npm install -g gatsby-cli
    ```

5. Gatsby Starter Theme 중에 마음에 드는 것을 가져와서 다음을 실행한다.

    ```bash
    gatsby new blog https://~
    ```

6. 다음을 실행하면 [localhost:8000](http://localhost:8000) 에서 확인할 수 있다.

    ```bash
    cd blog
    npm install
    gatsby develop
    ```

    대부분 해당 템플릿의 GitHub 소스에 가면 포스트를 어느 폴더에 작성해야 하는지,  `gatsby-config.js` 같은 파일이 관리하는 메타 정보나 프로필 등 개인마다 어떤 사항을 커스터마이즈 할 수 있는지도 알려주고 있다. 해당되는 부분들을 수정해서 저장하고, `gatsby develop` 명령어를 통해 수정한 부분이 잘 반영되는지 웹으로 확인해보자.

## GitHub Pages 환경 세팅

1. GitHub 레포지토리 생성

    GitHub에 `GitHub계정명.github.io` 라는 이름의 레포지토리를 생성한다.

    생성한 레포지토리와 블로그 코드를 git remote로 연결해준다.

    ```bash
    git remote add origin 'https://github.com/GitHub계정명/GitHub계정명.github.io
    ```

2. 개발용 브랜치와 배포용 브랜치 분리

    포스트를 commit 하는 등 블로그를 수정하는 브랜치(개발용)와 최종적으로 빌드된 결과물을 HTML 파일로 배포하는 브랜치(배포용)를 분리하는 작업을 수행한다. 개발용 브랜치명은 `develop`으로 정했다.

    ```bash
    git branch -m develop
    ```

3. gh-pages 설치

    블로그를 GitHub pages에 배포하기 위한 deploy script에 들어가는 gh-pages 모듈을 설치한다.

    ```bash
    npm install gh-pages
    ```

4. deploy script 추가

    Package.json의 scripts 부분에 다음 사항을 추가한다. `gatsby build` 명령어와 `-d`, `-b` 옵션은 작성한 블로그 코드를 public 폴더에 배포하고 이를 master 브랜치에 push 하는 역할이다. `gh-pages`와  `-r` 옵션은 이전에 생성했던 GitHub 레포지토리에 지금의 코드를 remote 연결하는 역할이다. 따라서 해당 레포지토리의 HTTP URL을 넣어주면 된다.

    ```bash
    "scripts": {
        "deploy": "gatsby build && gh-pages -d public -b master -r 'https://github.com/GitHub계정명/GitHub계정명.github.io.git'"
    }
    ```

5. GitHub Pages 배포하기

    다음 명령어를 실행하여 배포를 진행한다.

    ```bash
    npm run deploy
    ```

    이후 [GitHub 사이트](https://www.github.com)에 들어가서 이전에 생성한 레포지토리의 Settings 탭에 들어가면 Pages 메뉴를 볼 수 있다. Source에 최종적으로 배포될 브랜치를 정하게 되어있는데, deploy script에서 master 브랜치에 배포하도록 정했으므로 `Branch: master` 로 지정하고, 폴더는 `/ (root)` 로 지정한 뒤 저장한다.

    제대로 진행이 되었다면, `Your site is published at https://Github계정명.github.io/` 라는 메시지가 뜨며 배포가 완료되었을 것이다. 해당 URL에 들어가면 블로그가 웹 사이트로 배포된 것을 알 수 있다. 

## 참고 자료

- Docker 가상환경 이미지 ([링크](https://hub.docker.com/repository/docker/seongminha/blog))
- VSCode에 Docker 연동하기 ([블로그](https://89douner.tistory.com/123))
- Gatsby로 Github page 만들기 ([블로그](https://velog.io/@_woogie/Gatsby%EB%A1%9C-Github-page-%EB%A7%8C%EB%93%A4%EA%B8%B0))