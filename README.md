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
cf) VSCode에서 commit과 push 하는 법.
```
git add --all

git commit -m "커밋 메세지"

git remote add origin https://github.com/<your-github-username>/my-first-blog.git

git push -u origin master
```


## PythonAnywhere에 블로그 설정하기
- PythonAnywhere(www.pythonanywhere.com)에서 무료 계정인 "초보자(Beginner)"로 회원가입.
- 사용자 이름은 블로그 주소의 일부가 됨.
  ex) 사용자 이름이 DPBell 이면 URL은 DPBell.pythonanywhere.com 이 되는 형식.



## GitHub에서 PythonAnywhere로 코드 가져오기

- PythonAnywhere에 가입하면 대시보드 또는 콘솔(Console)페이지가 있음.
- PythonAnywhere의 Console에서 GitHub에 있는 저장소를 복제해 PythonAnywhere로 옮김.
```
git clone <내 GitHub Repository>
```

- PythonAnywhere에 올라간 Repository확인
`tree <repository 이름>`



## PythonAnywhere에 가상환경(virtualenv) 생성하기
- 로컬에서 작업하는 VSCode에서 사용하는 가상환경은 gitignore에 포함되어 있기 때문에 PythonAnywhere에서 사용할 가상환경 생성
  `cd <repository 이름>`
  `virtualenv --python=python3.6 <가상환경 이름>`
  `source <가상환경 이름>/bin/activate` cf)윈도우의 경우 `source <가상환경 이름>/Scripts/activate`를 사용하지만 Linux, Mac에서는 Scripts대신 bin 사용
- django 설치
  `pip install django`


  ## PythonAnywhere에서 데이터베이스 생성하기
  - 로컬컴퓨터와 PythonAnywhere 서버는 서로 다른 데이터베이스를 사용함.
  - 때문에 사용자 계정과 글은 로컬컴퓨터와 서버가 다를 수 있음.
  - PythonAnywhere의 서버에서도 데이터베이스를 초기화 해야함.
    `python manage.py migrate` -> `python manage.py createsuperuser`



## web app으로 블로그 배포하기
- 이제 코드는 PythonAnywhere 서버에 있고 서버에 가상환경과 정적파일도 모여있으며 데이터베이스까지 초기화 시킴 -> 웹 앱으로 배포할 준비 완료
- PythonAnywhere 대시보드로 돌아가 Web apps를 클릭하고 Add a new web app 선택
- 도메인 이름 확정 후(무료버전은 도메인 이름 변경 불가) manual Configuration(수동설정) 선택 후 Python 3.6 선택


## 가상환경(virtualenv) 설정하기
- PythonAnywhere 설정화면으로 이동함. 서버 앱에 변경사항이 있을 때 이 설정화면으로 들아가야 함.
- 가상환경(virtualenv) 섹션에서 가상환경 경로 입력


## WSGI 파일 설정하기
- 장고는 `WSGI 프로토콜`을 사용해 작동한다.
- 이 프로토콜은 파이썬을 이용한 웹 사이트를 서비스하기 위한 표준으로 PythonAnywhere에서도 지원함.
- WSGI 설정 파일을 수정해 여태까지 만든 장고 블로그를 PythonAnywhere에서 인식하게 해야함.
- web 탭에서 Code 섹션에 있는 `WSGI configuration file`의 경로를 클릭하면 에디터를 볼 수 있음.
```
# 모든 내용 삭제하고 아래 코드 복붙. <your-PythonAnywhere-username>에 내 PythonAnywhere 사용자 이름 쓰는 걸 잊지 말도록
import os
import sys

path = '/home/<your-PythonAnywhere-username>/my-first-blog'  # PythonAnywhere 계정으로 바꾸세요.
if path not in sys.path:
    sys.path.append(path)

os.environ['DJANGO_SETTINGS_MODULE'] = 'mysite.settings'

from django.core.wsgi import get_wsgi_application
from django.contrib.staticfiles.handlers import StaticFilesHandler
application = StaticFilesHandler(get_wsgi_application())
```
- 해당파일은 PythonAnywhere엑 웹 애플리케이션의 위치와 Django 설정 파일명을 알려주는 역할을 함.
- `StaticFilesHandler`는 CSS를 다루기 위한 것으로 `runserver` 명령으로 로컬 개발 중에 자동으로 처리 됨.
- 해당 문서의 뒷 부분에서 CSS를 작성할 때 정적 파일에 대해 좀 더 알아볼 것.
- 저장을 한 뒤 Web 탭으로 돌아가고 Reload하기. -> 해당 URL로 접속시 애플리케이션 볼 수 있음.



## 웹 사이트가 배포됨.
- 웹 개발 작업과정은 로컬에서 변경하고, 변경 사항을 GitHub에 적용하고, 변경 사항을 실제 웹 서버로 가져온다. 이를 통해 실제 웹 사이트를 손상시키지 않고 작업하고 테스트 해볼 수 있다.



## URL 이란
- URL은 웹 주소임. 웹 사이트를 방문할 때마다 브라우저의 URL을 확인 가능함.
- 인터넷의 모든 페이지는 고유한 URL을 가지고 있어야 함. 애플리케이션은 사용자가 URL을 입력하면 어떤 내용을 보여줘야 하는지 알고 있음.
- 장고는 `URLconf (URL configuration)`를 사용함.
- `URLconf`는 장고에서 URL과 일치하는 뷰를 찾기 위한 패턴들의 집합임.



## 장고 URL은 어떻게 작동하는지
- `mysite/urls.py`를 열면 아래의 코드가 존재할거임.
```
from django.contrib import admin
from django.urls import path

urlpatterns = [
    path('admin/', admin.site.urls), # 관리자 URL
]
```
- 장고는 `admin/`로 시작하는 모든 URL을 view와 대조해 찾아낸다.
- 무수히 많은 URL이 `admin URL`에 포함될 수 있어 일일이 모두 쓸 수 없다. 때문에 정규표현식을 사용한다.



## 나의 첫 번째 Django url
- `http://127.0.0.1:8000/` 주소를 블로그 홈 페이지로 지정하고 여기에 글 목록을 보여줄 것임.
- `mysite/urls.py` 파일을 깨끗한 상태로 유지하기 위해 `blog` 애플리케이션에서 메인 `mysite/urls.py` 파일로 url들을 가져올 것임
- 먼저 `blog.urls`를 가져오는 코드를 추가해보자
- `blog.urls`를 가져오려면 `include`함수가 필요함. `from django.urls` 행을 찾고 `import` 뒤에 `include` 함수 추가해라.
- `path('', include('blog.urls'))` 덕에 `http://127.0.0.1:8000/`로 들어오는 모든 접속 요층을 `blog.urls`로 전송해 추가 명령을 찾을 것임.



## blog.urls
- `blog/urls.py`라는 새 파일을 생성한 뒤 아래 코드를 추가해라.
```
# 장고 함수인 path와 blog 애플리케이션에서 사용할 모든 views를 가져옴.
from django.urls import path
from . import views
```

- 후에 첫 번째 URL 패턴을 추가해라.
```
urlpatterns = [
    path('', views.post_list, name='post_list'),
]
```
- 이제 `post_list` 라는 `view`가 루트 URL에 할당이 되었음.
- 해당 URL 패턴은 장고에게 누군가 웹 사이트에 `http://127.0.0.1:8000/`주소로 들어왔을 때 `views.post_list`를 보여주라고 말해줌.
- `name='post_list'`는 URL에 이름을 붙인 것으로 뷰를 식별하는 역할을 함.
- 뷰의 이름과 같을 수도 완전히 다를 수도 있음.
- 앱의 각 URL마다 이름 짓는 것은 중요함. 고유한 이름을 붙여 외우고 부르기 쉽게 만들어야함. -> 프로젝트 후반에 사용.



## 장고 뷰 만들기
- 뷰(view)는 애플리케이션의 `로직`을 넣는 곳임.
- 뷰는 우리가 만든 `모델`에서 필요한 정보를 받아와서 `템플릿`에 전달하는 역할을 함.
- 뷰는 `views.py`파일 안에 생성함. 우리는 views를 `blog/views.py`파일에 추가할 것임.



## blog/views.py
- `blog/views.py`에서 간단한 view 생성
```
def post_list(request):
    return render(request, 'blog/post_list.html', {})
```
- 해당 코드로 `post_list`라는 함수를 만듦. 이 함수는 `요청(request)`를 넘겨받아 `render`메서드를 호출함
- 이 함수는 `render` 메서드를 호출하여 받은(return된) `blog/post_list.html` 텝플릿을 보여줌.



## HTML 시작하기
- 템플릿은 서로 다른 정보를 일정한 형태로 표시하기 위해 재사용 가능한 파일을 말함.
- 예로 편지에도 같은 템플릿을 사용 가능함
- 편지의 내용이나 수신인 주소는 달라져도 같은 디자인, 레이아웃을 사용하는 경우도 있기 때문임
- 장고 템플릿 양식은 HTML을 사용함.



## 첫 번째 템플릿
- 템플릿은 `blog/templates/blog`디렉토리에 저장할 거임
- `blog`디렉토리 안에 하위 디렉토리인 `templates`를 생성하고 `templates` 디렉토리 내 `blog`라는 하위 디렉토리를 생성하셈.
- `blog/templats/blog` 디렉토리 내에 `post_list.html`파일을 생성.
- 그리고 서버를 실행하여 홈페이지를 확인하면 빈 템플릿인걸 확인할 수 있음.
```
<html>
    <p>Hi there!</p>
    <p>It works!</p>
</html>
```
- 해당 내용처럼 html 작성시 변경사항들을 확인할 수 있음.



## Head & body
- head는 문서 정보를 가지고 있지만, 웹 페이지에서 보이지 않는 정보들을 담는 영역 -> 브라우저에 페이지에 대한 설정을 알려줌.
- body는 웹 페이지에서 직접적으로 보이는 내용이 들어감. 웹 페이지의 내용은 모두 이 body 태그 안에 들어감. -> 실제 페이지에 보여줄 내용을 알려줌.
- `blog/templates/blog/post_list.html`
```
<html>
    <head>
        <title>Ola's blog</title>
    </head>
    <body>
        <p>Hi there!</p>
        <p>It works!</p>
    </body>
</html>
```
