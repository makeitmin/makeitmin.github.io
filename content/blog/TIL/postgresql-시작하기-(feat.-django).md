---
title: PostgreSQL 시작하기 (feat. Django)
date: 2021-08-03 12:08:36
category: TIL
thumbnail: { thumbnailSrc }
draft: false
---

기존에 사용한 경험이 있는 데이터베이스는 MySQL과 MongoDB가 있었다. 관계형 데이터베이스(RDB)로 범위를 줄이면 MySQL 정도였고, 엘리스 AI 트랙에서 했던 프로젝트 중에 백엔드를 Django와 PostgreSQL로 구성한 프로젝트가 있었다. 나는 그 프로젝트에서 프론트엔드를 맡았고 Django를 애초에 몰랐기에 백엔드 코드를 자세히 보지 않았는데, 당시 백엔드 개발을 맡은 팀원이 개발환경 구성을 굉장히 일찍 끝내서 인상깊었던 기억이 있다.

그렇게 백엔드는 캄캄하게 모른 채로 지나간 프로젝트였는데 드디어 그 프로젝트의 백엔드 코드와 데이터베이스 구성을 이해할 수 있게 될 것 같아 벌써 기분이 좋다. 

## PostgreSQL 조사하기

### PostgreSQL의 필요성

Django 프로젝트를 위해 관계형 데이터베이스인 PostgreSQL에 대해 찾아보면서 들었던 가장 큰 의문은 **관계형 데이터베이스를 사용한다면 왜 MySQL이나 Oracle이 아닌 PostgreSQL을 사용하는지**였다. 답은 **다른 데이터베이스에 비해 Django 입장에서 PostgreSQL만이 지원하는 기능이 많기 때문**이었다. Django 공식 문서에서는 다음과 같이 설명하고 있다.

> **PostgreSQL has a number of features which are not shared by the other databases Django supports. (생략) ... Django provides support for a number of data types which will only work with PostgreSQL.**

### PostgreSQL의 장점

- 무료 오픈 소스
- 관계형 데이터베이스지만 객체 지향 데이터베이스의 기능(테이블 상속 및 함수 오버로딩)도 가능
- ACID(트랜잭션의 원자성, 일관성, 격리성, 내구성)를 엄격하게 준수하면서 동시성을 보장
- 다양한 데이터 타입 지원

## PostgreSQL 사용하기

### PostgreSQL 설치

설치되는 환경은 Ubuntu 18.04 이다. [PostgreSQL 공식 문서](https://www.postgresql.org/download/linux/ubuntu/)를 통해 저장소를 확인하였다.

1. **postgresql과 postgresql-contrib을 설치한다.**

    ```bash
    # 저장소 설정
    sudo sh -c 'echo "deb http://apt.postgresql.org/pub/repos/apt $(lsb_release -cs)-pgdg main" > /etc/apt/sources.list.d/pgdg.list'
    wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -

    # apt 패키지 업데이트
    sudo apt-get update

    # postgresql, postgresql-contrib 설치
    sudo apt-get install postgresql postgresql-contrib
    ```

2. **PostgreSQL 서버를 연결한다.**

    ```bash
    service postgresql start
    ```

3. **서버가 잘 연결되었는지 확인한다.**

    ```bash
    # 13/main (port 5432): online 이 뜨면 성공
    service postgresql status
    ```

4. **root 유저 설정**
여기서 조금 헤맸는데, 이유는 바로 PostgreSQL을 시작하기 위해 `psql`을 입력했지만 다음과 같은 오류가 발생했기 때문이다.

    ```bash
    psql: error: FATAL:  role "root" does not exist
    ```

    이 오류는 PostgreSQL에 'root'라는 Role이 등록되지 않아 생긴 문제이다. Unix 시스템의 root 계정과는 다른 개념이다. 대신 PostgreSQL에 슈퍼유저로 기본 등록되어 있는 postgres 계정으로 접속하자.

    ```bash
    sudo -u postgres psql
    ```

    이렇게 하면 바로 Shell이 뜬다.

### PostgreSQL의 명령어

데이터베이스와 테이블을 생성 또는 삭제하고, 데이터를 삽입/조회/수정/삭제할 수 있다.

```bash
# 데이터베이스 생성
CREATE DATABASE testdb;

# 데이터베이스 삭제
DROP DATABASE testdb;

# 데이터베이스 접속(Linux Command)
sudo -u postgres psql -d testdb

# 데이터베이스 목록
\l

# 테이블 생성
CREATE TABLE "USER"
(
	USER_ID SERIAL PRIMARY KEY,
	USER_NAME VARCHAR(50) UNIQUE NOT NULL,
	PASSWORD VARCHAR(50) NOT NULL,
	EMAIL VARCHAR(355) UNIQUE NOT NULL
);

# 테이블 삭제
DROP TABLE user;

# 데이터 삽입
INSERT INTO "USER" VALUES (0, 'seongmin', '1234', 'seongminha0910@gmail.com');

# 데이터 조회
SELECT * FROM "USER";
SELECT EMAIL FROM "USER";

# 데이터 수정
UPDATE "USER" SET EMAIL='seongminha.dev@gmail.com' where USER_ID=0;

# 데이터 삭제
DELETE FROM "USER" where USER_ID=0;
```

## 참고자료

- SQLite vs MySQL vs PostgreSql: 관계형 DB 시스템의 비교 ([블로그](https://smoh.tistory.com/370?category=706280))
- PostgreSQL 공식문서 ([문서](https://www.postgresql.org/))
    - PostgreSQL 설치 ([문서](https://www.postgresql.org/download/linux/ubuntu/))
    - PostgreSQL 기본 쿼리 ([문서](https://www.postgresql.org/docs/9.2/sql-insert.html))
- Django 공식 문서 ([문서](https://docs.djangoproject.com/en/3.0/ref/contrib/postgres/))
- Django Girls 튜토리얼 ([문서](https://tutorial-extensions.djangogirls.org/ko/optional_postgresql_installation#undefined-6))