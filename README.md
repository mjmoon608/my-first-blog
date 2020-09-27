# DjangoGirls Tutorial
(https://tutorial.djangogirls.org/ko/django_models/)



## 장고프로젝트 시작하는 법
  1. 가상환경을 만들고 pip 업그레이드 후 django를 설치한다.
  2. django-admin.py startproject {프로젝트이름} .

- manage.py 는 스크립트로 사이트 관리를 도와주는 역할을 함.

  - 이 스크립트로 다른 설치 작업 없이, 컴퓨터에서 웹 서버를 시작할 수 있음.

- mysite/setting.py 는 웹 사이트 설정이 있는 파일

- urls.py 파일은 urlresolver가 사용하는 패턴 목록을 포함하고 있음.

  - 웹 서버에 요청이 오면 장고로 전달됨.
  - 장고 <br>urlresolver</br>는 웹 페이지의 주소를 가져와 무엇을 할지 확인(urlsolver는 웹 사이트 주소인 URL을 통해 이해)
  - 패턴 목록을 가져와 URL과 맞는지 처음부터 하나씩 대조해 식별.
  - 만약 일치하는 패턴이 있으면 장고는 해당 요청을 관련된 함수(view)에 전달.

  ex) 집배원의 경우 거리를 걸으며 집마다 편지와 대조해서 주소와 번지를 확인함. 주소와 번지가 일치하면 그곳에 편지를 배달
  urlsolver가 집배원과 같은 역할이라고 생각하면 편함.

  cf) - 모든 재미난 일들은 view 함수에서 처리됨: 특정 정보를 데이터베이스에서 찾을 수 있음.

  - 사용자가 데이터를 바꿔달라고 수정을 요청시
  - view함수는 수정할 수 있는 권한이 있는지 확인하고나서 정보를 수정하고 완료했다표시하고
  - view는 답장을 생성하고 장고는 그 답장을 사용자의 웹 브라우저에 보내주는 역할을 수행.

- python manage.py migrate : 데이터베이스를 생성하기 위함(?)



## 객체(Object)

1. 객체 지향 프로그래밍은 프로그램이 어떻게 작동해야 하는지 모든 것을 하나하나 지시하는 것 대신, 모델을 만들어 그 모델이 어떤 역할을 가지고 어떻게 행동해야 하는지 정의하여 서로 알아서 상호작용 할 수 있도록 만드는 것
2. 객체란 : 속성과 행동을 모아놓은 것.
   ex) 고양이라는 객체를 모델링할 때.

   - 고양이는 여러 속성을 가짐: 색깔, 나이, 분위기, 주인(사람일 수도 있고, 길고양이일 시 주인이 없어 속성이 빈 값일 수도 있음.)
   - 기본적으로 객체지향설계 개념은 현실에 존재하는 것을 속성과 행위로 나타내는 것. 속성은 객체 속성(properties), 행위는 메서드(Method)로 구현

   - 이 튜토리얼은 블로그를 만드는 것. 그렇다면 블로그 글을 모델로 만들때 블로그 글이란 무엇인가? 어떤 속성들을 가져야 할까? 라는 질문에 답을 해야함.
   - 블로그는 제목과 내용이 필요하고 누가 썼는지 알 수 있게 작성자도 추가, 글이 작성된 날짜와 게시된 날짜가 있음 좋겠다

     ```
     Post(게시글)
     --------------
     title(제목)
     text(내용)
     author(글쓴이)
     created_date(작성일)
     published_date(게시일)
     ```

   - 블로그 글로 할 수 있는 행위는 어떤 것이 있는가? -> 글을 출판하는 메서드(Method)가 필요함. -> publish 메서드 만들어야하는 이유.



## 장고 모델

  - 장고 안의 모델은 객체의 특별한 종류임.
  - 이 모델을 저장하면 그 내용이 데이터베이스에 저장되는 점 때문.
  - SQLite는 기본 장고 데이터베이스 어댑터.
  - 데이터베이스 안의 모델이란 엑셀 스프레드시트와 같음. 엑셀 스프레드시트를 보면 열(필드)와 행(데이터)로 구성됨. 모델도 마찬가지.



## 어플리케이션 만들기

  - 잘 정돈된 상태에서 시작하기 위해 프로젝트 내부에 별도의 어플리케이션을 만듦.
    -> python manage.py startapp {앱 이름}

  - 어플리케이션을 생성한 후 장고에 사용해야 한다고 알려줘야함. 이 역할을 하는 파일이 `mysite/settings.py`임.
  - `mysite/settings.py` 안의 `INSTALLED_APPS`에 `'{앱 이름}'`을 추가해줘야함.



## 블로그 글 모델 만들기
- 모든 `Model`객체는 `blog/models.py` 파일에 선언하여 모델은 만듦. 이 파일에 튜토리얼에서 만드는 블로그의 글 모델도 정의할 것.
```
# 해당 코드 복붙
from django.conf import settings
from django.db import models
from django.utils import timezone


class Post(models.Model):
    author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
    title = models.CharField(max_length=200)
    text = models.TextField()
    created_date = models.DateTimeField(
            default=timezone.now)
    published_date = models.DateTimeField(
            blank=True, null=True)

    def publish(self):
        self.published_date = timezone.now()
        self.save()

    def __str__(self):
        return self.title
```

- `class Post(models.Model)`은 모델을 정의하는 코드(모델은 `객체(object)`라고 함)
  - `class`는 특별한 키워드로 객체를 정의한다는 것을 알려줌.
  - `Post`는 모델의 이름. 특수문자와 공백을 제외하면 다른 이름을 붙일 수도 있음. 클래스의 첫 글자는 항상 대문자
  - `models`는 Post가 장고 모델임을 의미함. 이 코드 때문에 Post가 데이터베이스에 저장되어야 한다고 알게 됨.

- 속성을 정의할 경우 필드마다 어떤 종류의 데이터 타입을 가지는지를 정해야한다. 이때 데이터 타입에는 텍스트, 숫자, 날짜, 사용자 같은 다른 객체 참조 등이 있다.
- 해당 블로그 제작 튜토리얼의 경우 `title`, `text`, `created_date`, `published_date`, `author`의 속성이 있음.

- `models.CharField` : 글자 수가 제한된 텍스트를 정의할 때 사용함. 글 제목같이 짧은 문자열 정보를 저장할 때 사용함.
- `models.TextField` : 글자 수에 제한이 없는 긴 텍스트를 위한 속성. 블로그 콘텐츠에 사용하기 좋음.
- `models.DateTimeField` : 날짜와 시간을 의미
- `models.ForeignKey` : 다른 모델에 대한 링크를 의미.

cf) 모델의 필드와 정의하는 방법: (https://docs.djangoproject.com/en/2.0/ref/models/fields/#field-types) - 장고 공식 문서.

- `def publish(self)` : 앞에서 말한 `publish` 메서드
  - 메서드를 정의할 때는 소문자와 언더스코어 사용.
  - 메서드는 자주 무언가를 되돌려 준다.(return)
  - 그 예로 `__str__` 메서드에서는 해당 메서드를 호출하면 Post 모델의 제목 텍스트(string)를 얻게 된다.



## 데이터베이스에 모델을 위한 테이블 만들기
- 데이터베이스에 우리가 만든 Post 모델을 추가할 것.
- 먼저 장고 모델에 방금 우리가 만든 몇가지 변화가 생겼다는 것을 알게 해줘야함
-> `python manage.py makemigrations blog`
-> 장고는 데이터베이스에 지금 반영할 수 있도록 마이그레이션 파일(migration file)이라는 것을 준비함.
- `python manage.py migrate blog`를 통해 실제 데이터베이스에 모델 추가를 반영함.



## 장고 관리자
- 방금 막 모델링 한 글들을 장고 관리자에서 추가하거나 수정, 삭제할 수 있음.
- `blog/admin.py` 파일에 내용 아래와 같이 변경
```
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```
- 앞에서 정의한 Post 모델을 가져오고(import) 있음.
- 관리자 페이지에서 만든 모델을 보려면 `admin.site.register(Post)`로 모델을 등록해야함.

- `python manage.py runserver`로 웹 서버를 실행하고 주소창에 `http://127.0.0.1:8000/admin` 입력시 로그인 페이지를 볼 수 있음.
- 로그인하기 위해서는 모든 권한을 가지는 <br>슈퍼 사용자(superuser)</br>를 생성해야함. 
-> `python manage.py createsuperuser`

cf) 장고 관리자 공식문서 : https://docs.djangoproject.com/en/2.0/ref/contrib/admin/



## 배포하기
- 비교적 배포 과정이 간단한 PythonAnywhere(https://www.pythonanywhere.com/)을 사용할 예정.
- PythonAnywhere는 방문자가 아주 많지 않은 소규모 애플리케이션을 위한 무료 서비스를 제공함.
- 로컬 컴퓨터는 개발 및 테스트를 수행하고 개발이 완료되면 프로그램 복사본을 GitHub에 저장함.웹 사이트는 PythonAnywhere에 있고 GitHub에서 코드 사본을 업데이트 할 거임.



## Git 저장소 만들기
```
$ git init
Initialized empty Git repository in ~/djangogirls/.git/
$ git config --global user.name "Your Name"
$ git config --global user.email you@example.com
```

`.gitignore`생성
```
*.pyc
*~
__pycache__
myvenv
db.sqlite3
/static
.DS_Store
```

- `git add` 하기 전이나 변경된 것이 있는지 잘 모를 때마다 `git status` 명령어를 사용하는 것은 좋은 방법이다.
- 잘못된 파일이 추가되거나 커밋된 경우, 이를 복구할 수 있다.
- `git status`는 미추적/수정된/스테이지된 파일, 브랜치 상태와 그 외 많은 정보를 보여줌.

- 우리가 만든 코드들을 저장소에 넣음.
```
$ git add --all .
$ git commit -m "My Django Girls app, first commit"
 [...]
 13 files changed, 200 insertions(+)
 create mode 100644 .gitignore
 [...]
 create mode 100644 mysite/wsgi.py
```

## GitHub에 코드 배포하기.
- new repository 생성 후 업로드
```
$ git remote add origin https://github.com/<GitHub 사용자 이름>/my-first-blog.git
$ git push -u origin master
```



## PythonAnywhere에 블로그 설정하기
- PythonAnywhere(www.pythonanywhere.com)에서 무료 계정인 "초보자(Beginner)"로 회원가입.
- 사용자 이름은 블로그 주소의 일부가 됨.
  ex) 사용자 이름이 DPBell 이면 URL은 DPBell.pythonanywhere.com 이 되는 형식.



## GitHub에서 PythonAnywhere로 코드 가져오기

- PythonAnywhere에 가입하면 대시보드 또는 콘솔(Console)페이지가 있음.

git add --all

git commit -m "커밋 메세지"

git remote add origin https://github.com/<your-github-username>/my-first-blog.git

git push -u origin master