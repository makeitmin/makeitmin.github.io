---
title: Django ORM에서 Func() 사용하기
date: 2021-08-31 12:08:51
category: TIL
thumbnail: { thumbnailSrc }
draft: false
---

오늘 Django ORM Query 코드를 보다가 중간중간 Func()이라는 함수를 발견했다. 모르고 있던 함수여서 검색을 했는데, 감이 잘 안 잡혀서 동료 분한테 다시 여쭤보고 정리할 수 있었다.

## Func()의 개념과 구성 요소

Func()는 ORM이 자체 함수로 지원하지 않는 데이터베이스 함수를 개발자가 직접 적용할 수 있도록 해주는 함수이다. Func()에는 3개의 속성이 있다.

- 실행할 함수에 적용할 값
- 실행할 함수 (function)
- 템플릿 (template)

3번째 요소인 템플릿은 function과 expressions을 결합하여 DB에 질의할 Query를 만든다. 기본값은 **%(function)s(%(expressions)s)**이다.

## Func()에 들어갈 수 있는 함수

Func()에 들어갈 수 있는 함수는 무엇이 있을까? PostgreSQL에서 유용하게 쓰이지만 ORM이 지원하지 않는 것이 대상이 될 것이다. 일단 코드에서 확인한 예시는 다음의 3가지였다.

- **TIMEZONE**  
시간대(KST, UTC 등)을 인자로 주어 시간(Timestamp)를 해당 시간대로 바꾸어 반환한다.

    ```python
    Func('KST', 시간, function="TIMEZONE")
    ```

- **NOW**  
현재 시간(Timestamp)를 반환한다.

    ```python
    Func(function="NOW")
    ```

- **DATE_TRUNC**  
시간 단위(Year, Month, Day)를 인자로 주어 해당 기준으로 시간(Timestamp)을 절사하여 반환한다.

    ```python
    Func(Value('day'), 시간, function="DATE_TRUNC")
    ```

## 참고자료

- Django가 지원하지 않는 데이터베이스 함수를 사용할 수 있나요? ([문서](https://django-orm-cookbook-ko.readthedocs.io/en/latest/func_expressions.html))

- ORM에서 Func()를 사용해 커스텀 DB 함수 구현하기 ([블로그](https://brownbears.tistory.com/496))

- ORM Cookbook, 정보를 조회하고 필요한 항목을 선별하는 방법(3) ([블로그](https://ssungkang.tistory.com/entry/Django-ORM-Cookbook-%EC%A0%95%EB%B3%B4%EB%A5%BC-%EC%A1%B0%ED%9A%8C%ED%95%98%EA%B3%A0-%ED%95%84%EC%9A%94%ED%95%9C-%ED%95%AD%EB%AA%A9%EC%9D%84-%EC%84%A0%EB%B3%84%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%953))