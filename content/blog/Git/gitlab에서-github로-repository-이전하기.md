---
title: GitLab에서 GitHub로 Repository 이전하기
date: 2021-03-27 07:07:12
category: Git
thumbnail: { thumbnailSrc }
draft: false
---

1. Code 이전하기 (간단)
2. Issues, Milestones, Merge Request 이전하기 (복잡)  

## 시작하기 전에

Merge Request를 이전하는 것은 **open** 되어 있는 것들만 가능하다. GitHub API 정책상 Merge Request를 Pull Request로 옮기는 것은 **Target 브랜치와 Source 브랜치 간의 커밋 차이**가 있어야 가능하다고 한다. 이미 merged 상태인 것은 두 브랜치 간의 차이를 추적할 수 없으므로 Merge Request의 형태로 옮기기 어렵다.  

해당 부분에 대해 [직접 질문을 남긴 내용](https://github.com/piceaTech/node-gitlab-2-github/issues/91#issuecomment-884719601)이다.  

질문 당시 merged 상태인 Merge Request를 이전할 경우 오류가 나고 있는 상황이었고, 이에 따라 이미 merged 상태인 Merge Request는 Issue의 형태로 이전되도록 어떤 친절한 개발자분의 코드 개선으로 [오류가 핸들링된 내용](https://github.com/piceaTech/node-gitlab-2-github/pull/93)이다.


## 방법 1 : Code 이전하기


1. GitHub에 옮길 빈 Repository를 미리 생성한다.

2. 로컬에서 다음을 수행한다.

```bash
# GitLab에서 bare 상태로 자신의 Repository clone 해오기
git clone --mirror https://kdt-gitlab.elice.io/001_part2_project-portfolio/team2/skidmarker.git

# 프로젝트 폴더로 이동하기
cd skidmarker.git

# 새 GitHub Repository에 remote 연결
git remote set-url --push origin https://github.com/makeitmin/skidmarker.git

# 새 GitHub Repository로 push 하기
git push --no-verify --mirror https://github.com/makeitmin/skidmarker.git
```

## 방법 2 :  Issues, Milestones, Merge Requests 이전하기


### 폴더 생성하기

CMD를 열고 다음을 수행한다.

```bash
cd C:/Users/사용자/.ssh
mkdir github
mkdir gitlab
```

### GitLab과 GitHub에서 각각 SSH Key 발급받기

1. GitLab 공개키

    **1.1.1** SSH Key 발급받기

    ```bash
    # Git Bash에서 작업
    ssh-keygen
    Generating public/private rsa key pair.
    **Enter file in which to save the key (/c/Users/사용자/.ssh/id_rsa): /c/Users/사용자/.ssh/github/id_rsa # Key 파일 저장할 경로 입력하기**
    Created directory '/기본저장경로 또는 입력경로'.
    **Enter passphrase (empty for no passphrase):      # 암호 입력하기**
    **Enter same passphrase again:                     # 암호 재입력하기**
    Your identification has been saved in /입력한 경로.
    Your public key has been saved in /입력한 경로.
    The key fingerprint is: 
    ```

    **1.1.2** Key 파일이 저장된 경로의 id_rsa.pub 내용 복사

    ```bash
    # ssh-rsa 뒤부터 사용자 이메일까지 복사
    cat C://Users/사용자/.ssh/github/id_rsa.pub ssh-rsa
    ```

    **1.1.3** `GitLab 프로필` → `Settings` → `SSH Keys`에 접근

    **1.1.4** 복사한 id_rsa.pub 내용 붙여넣고 등록

    **1.1.5** 발급된 token 복사하기(다시 조회할 수 없으니 꼭 다른 곳에 복사해두기)

2. GitHub 공개키

    **2.1** SSH Key 발급받기

    ```bash
    # Git Bash
    ssh-keygen
    Generating public/private rsa key pair.
    **Enter file in which to save the key (/c/Users/사용자/.ssh/id_rsa): /c/Users/사용자/.ssh/gitlab/id_rsa # Key 파일 저장할 경로 입력하기**
    Created directory '/기본저장경로 또는 입력경로'.
    **Enter passphrase (empty for no passphrase):      # 암호 입력하기**
    **Enter same passphrase again:                     # 암호 재입력하기**
    Your identification has been saved in /입력한 경로.
    Your public key has been saved in /입력한 경로.
    The key fingerprint is: 
    ```

    **2.2** Key 파일이 저장된 경로의 id_rsa.pub 내용 복사

    ```bash
    # ssh-rsa 뒤부터 사용자 이메일까지 복사
    cat C://Users/사용자/.ssh/gitlab/id_rsa.pub ssh-rsa
    ```

    **2.3** `GitHub 프로필` → `Settings` → `SSH and GPG Key` → `New SSH Key`에 접근

    **2.4** 복사한 id_rsa.pub 내용 붙여넣고 등록

    **2.5** 발급된 token 복사하기(다시 조회할 수 없으니 꼭 다른 곳에 복사해두기)

### node-gitlab-2-github 사용하기

1. node-gitlab-2-github 가져오기

    ```bash
    git clone https://github.com/piceaTech/node-gitlab-2-github.git
    cd node-gitlab-2-github
    ```

2. node_modules 설치하기

    ```bash
    npm i
    ```

3. settings.ts 생성하기

    ```bash
    cp sample_settings.ts settings.ts
    ```

4. settings.ts 작성하고 저장하기

    ```bash
    ...

    gitlab: {
        url: 'https://kdt-gitlab.elice.io',
        token: '복사해둔 GitLab token',
        projectId: 'GitLab Repository의 Project ID' # ex) 551 (Repository 타이틀 아래에 있음)
      },
      github: {
        baseUrl: 'https://api.github.com',
        owner: 'GitHub 유저명',
        token: '복사해둔 GitHub token',
        repo: 'GitHub Repository 이름',
      },

    ...

    transfer: {
        milestones: true,
        labels: true,
        issues: true,
        mergeRequests: true,
      },

    ...
    ```

5. 실행하기

    ```bash
    npm run start
    ```

## 참고자료

- node-gitlab-2-github ([레포지토리](https://github.com/piceaTech/node-gitlab-2-github))

- SSH 공개키 생성 ([문서](https://git-scm.com/book/ko/v2/Git-%EC%84%9C%EB%B2%84-SSH-%EA%B3%B5%EA%B0%9C%ED%82%A4-%EB%A7%8C%EB%93%A4%EA%B8%B0))

- SSH 공개키 등록 ([블로그](https://brunch.co.kr/@anonymdevoo/10))