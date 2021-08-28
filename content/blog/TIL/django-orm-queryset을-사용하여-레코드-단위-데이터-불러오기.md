---
title: Django ORM Query로 레코드 단위의 데이터 불러오기
date: 2021-08-27 13:08:64
category: TIL
thumbnail: { thumbnailSrc }
draft: false
---

Django ORM Query를 사용하면서, 레코드 단위로 한 줄씩 데이터를 불러오는 경우를 구현할 일이 생겼다. 접속이 뜸해 특정 유형으로 전환되는 사용자에게 메일을 보내는 일반 배치 프로그램이었는데, 단순히 생각하면 **대상이 되는 사용자를 filter 메소드로 일괄 불러와서 로직을 처리해야겠다**는 생각이 들 수도 있다.  

하지만, 한꺼번에 불러와서 메일을 보내는 로직을 구현할 경우,  

1. 특정 유형으로 전환
2. 안내 메일 발송

이 2가지 작업이 서버에서 진행되는 도중에 사용자가 접속하는 경우를 커버할 수 없다. 왜냐하면 filter 메소드로 한꺼번에 여러 레코드를 불러오면, **해당 사용자의 접속시점이 현재로 업데이트 되어도 이미 Query가 배치 프로그램을 시작하면서 업데이트 전의 데이터를 전부 불러온 후이기 때문**이다.  
그렇게 되면 사전에 안내된 기한을 넘기지 않으려고 간발의 차로 접속한 사용자를 그냥 오래 접속하지 않은 사용자로 전환해버리고 메일을 보내는 상황이 발생할 수도 있다. 

## SQL로 생각해보기

ORM Query를 사용해본 적이 없어서, 나는 일단 SQL로 생각하는 것이 편했다.

```sql
SELECT * FROM table_name WHERE target_field < target_value LIMIT 0, 1;
```

이렇게 하면 특정 조건을 만족하는 레코드 중에 가장 첫 번째 레코드를 볼 수 있다. 

## ORM Query로 바꿔보기

이제 이걸 ORM Query로 바꿔보자.

```python
Model.objects.filter(target_field__lt=target_value)[0]
# or
Model.objects.filter(target_field__lt=target_value)[:1]
```

한 줄씩 가져오는 게 목적이므로 첫 번째 Query가 조금 더 적합해보여서 그렇게 적용했다. SQL에서 `LIMIT 0, 1`은 **0번째부터 1개의 레코드**를 의미한다. ORM Query에서는 이 경우를 **Python 리스트 타입의 인덱싱 방식**으로 구현할 수 있다.

## 참고자료

- Django Limiting QuerySets ([문서](https://docs.djangoproject.com/en/3.0/topics/db/queries/#limiting-querysets))

