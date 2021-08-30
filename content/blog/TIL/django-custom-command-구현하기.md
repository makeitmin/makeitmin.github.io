---
title: Django Custom Command 구현하기
date: 2021-08-26 12:08:73
category: TIL
thumbnail: { thumbnailSrc }
draft: false
---

Django Custom Command는 특정 앱 내부의 특정 함수를 커맨드로 사용할 수 있는 기능이다. 더 쉽게 말하면, 개발자가 Django 앱 내부에 원하는 동작을 함수로 구현하고, 이를 커맨드로 manage.py에 등록하여 사용하는 개념이다.   

다음 순서로 Django Custom Command를 구현할 것이다.  

- Django 앱 생성하기  
- Custom Command 작성하기
 
## Django 앱 생성하기

``example_app``이라는 Django 앱을 다음과 같이 생성한다.
```bash
python manage.py startapp example_app
```  

## Custom Command 작성하기

1. 커맨드가 작성될 폴더를 생성한다. 폴더 이름은 바꿀 수 없으며, 고정이다.

	```bash
	mkdir management/commands
	```

	tree 명령어로 구조를 확인해보자.

	```bash
	tree -L 3
	```  

	```bash
	django_test
		|-- django_test
		|   |-- __init__.py
		|   |-- asgi.py
		|   |-- settings.py
		|   |-- urls.py
		|   `-- wsgi.py
		|-- example_app
		|   |-- __init__.py
		|   |-- admin.py
		|   |-- apps.py
		|   |-- migrations
		|   |-- models.py
		|   |-- tests.py
		|   |-- views.py
		|   `-- management/commands
		`-- manage.py
	```

2. 해당 폴더 밑에 Python 파일을 작성한다. handle 함수는 커맨드가 실행될 때 메인 함수 역할이다. 여러 함수를 구현하고, 커맨드가 동작할 때 실행되도록 handle 함수에 선언해준다.
	```python
	# custom_command.py
	from django.core.management.base import BaseCommand

	class Command(BaseCommand):
	    def handle(self, *args, **options):
	        example_command_1()
	        example_command_2()
	   
	    def example_command_1(self):
		    # 데이터베이스에서 필요 없는 데이터를 삭제하는 코드
		    # ORM Query etc.

	    def example_command_2(self):
	       # 데이터베이스 일괄 업데이트하는 코드
	       # ORM Query etc.
        ...
	```
	
	
3. 커맨드를 실행한다.

	```bash
	python manage.py custom_command
	```

## 참고자료

- Django Custom Management Commands ([문서](https://docs.djangoproject.com/ko/3.2/howto/custom-management-commands/))