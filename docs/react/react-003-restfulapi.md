---
layout: default
title: Django + RESTful API
parent: React
nav_order: 2
last_modified_date: 2021-02-26
---
# Django + Restful API
{: .no_toc }

Django는 Python 기반 웹 프레임워크이며 RESTful API는 REST 아키텍처의 제약조건을 준수해 호출과 응답을 구성한 프로그래밍 언어 간 통신 방식입니다. JSON, HTML, 텍스트 등 다양한 형식을 정보 송수신에 사용하지만 인간과 머신이 모두 읽을 수 있는 형태이자, 언어와 상관 없이 쓸 수 있는 JSON 형식을 가장 많이 사용합니다. 이 글에선 Python의 Django 백엔드 서버와 React(+Axios) 프론트엔드 서버의 RESTful API 통신에 대해 다룹니다. 검색(Ctrl + F)으로 원하는 내용을 찾아보세요.

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>
# 작업환경

※ 기본적인 Python, Pycharm, React 설치에 대해선 다루지 않는다.

- Windows 10 Home

- Pycharm 2020.3.2 Community Edition (어떤 Python 에디터라도 상관 없다)

- WSL Linux(CentOS 7) : 윈도우에 리눅스 설치 후 Bash에서 설치를 진행한다. 따라서 아래 설명은 Linux 명령어 위주로 되어 있다. 명령어 실행에 문제가 발생하면 [장고걸스 튜토리얼](https://tutorial.djangogirls.org/ko/django_installation/)에서 윈도우 명령어를 참고한다.

  

# django 설치

터미널을 열고 프로젝트를 시작할 디렉토리를 만들고, 진입한다.

```bash
mkdir django
cd django
```

### 가상환경 설정
{: .no_toc }

```bash
python3 -m venv venv					# venv 란 이름의 가상환경 생성
source venv/bin/activate				# venv 가상환경 실행
```

(venv) root@... 이런 식으로 터미널이 표시된다면 가상환경 정상 작동하는 것이다.

### django, DjangoRESTframwork 설치
{: .no_toc }

```bash
(venv) ~/django>  python -m pip install --upgrade pip 	# pip 업데이트
(venv) ~/django>  pip install django 						# 장고 설치
(venv) ~/django>  pip install djangorestframework 		# DRF 설치
(venv) ~/django>  pip install django-cors-headers			# CORS HTTP 접근제어 규약 (추후 설명)
```

설치가 끝나면 Django 서버를 설치할 폴더를 만들고, 진입한다.

```bash
(venv) ~/django>  mkdir backend
(venv) ~/django>  cd backend
```

djangoreactapi 라는 이름의 Django 프로젝트를 만든다. 마지막 점(.)은 상대경로로 현재 위치를 의미한다.

```bash
(venv) ~/django/backend>  django-admin startproject djangoreactapi .	# 현재 폴더에 djangoreactapi 라는 프로젝트 만들기
```

여기까지 진행하면 디렉 토리 구조가 아래와 같다. 



(사진)



### API로 호출할 앱 생성
{: .no_toc }

post란 이름의 앱 생성

```bash
(venv) ~/django/backend>  python manage.py startapp post
```

여기까지 진행하면 디렉 토리 구조가 아래와 같다. 



(사진)



### backend/djangoreactapi/settings.py
{: .no_toc }

settings.py 파일에서 아래와 같이 값을 추가 / 수정한다.

```python
# Application definition

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'post', # 추가
    'rest_framework', # 추가
    'corsheaders', # 추가
]

...

# REST_FRAMEWORK 전체 추가
REST_FRAMEWORK = {
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.AllowAny',
    ]
}

...

# CORS_ORIGIN_WHITELIST 전체 추가
CORS_ORIGIN_WHITELIST = [
    'http://localhost:3001',	# 추가 (React 서버 작동 주소 및 포트)
]

# CORS_ORIGIN 설정 전체 추가
CORS_ORIGIN_ALLOW_ALL = True
CORS_ALLOW_CREDENTIALS = True

...

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',  # 추가
    'django.middleware.common.CommonMiddleware',  # 추가
    
...

TIME_ZONE = 'Asia/Seoul'	# 변경

STATIC_URL = '/static/'
STATIC_ROOT = os.path.join(BASE_DIR, 'static')		# 추가

```

migrate 실행한다.

```bash
(venv) ~/django/backend>  python manage.py migrate
```

django 개발용 내부 웹 서버를 실행한다. 원하는 IP 주소와 포트 번호를 runserver 뒤에 붙여 설정할 수 있고, 아무것도 없으면 기본 포트 번호로 실행한다. 

```bash
(venv) ~/django/backend>  python manage.py runserver 0.0.0.0:7000
```

아래 사진이 나오면 서버가 잘 만들어진 것이다.



(사진)



## django MVC 정의



### backend/post/models.py
{: .no_toc }

```python
from django.db import models

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()

    def __str__(self):
        """A string representation of the model."""
        return self.title

class Question(models.Model):
    data = models.TextField()
```

### backend/post/admin.py
{: .no_toc }

```python
from django.contrib import admin
from .models import Post

admin.site.register(Post)
```



커맨드로 돌아가 backend 폴더에서 migrate 실행한다.

```bash
(venv) ~/django/backend>  python manage.py makemigrations
(venv) ~/django/backend>  python manage.py migrate
```

Superuser를 생성한다.

```bash
(venv) ~/django/backend>  python manage.py createsuperuser
```

다시 서버를 실행해 경로 뒤에 /admin을 붙여본다.

```bash
(venv) ~/django/backend>  python manage.py runserver 0.0.0.0:7000
```

임시로 POST를 추가한다.



## django-rest-framework(DRF) 구성

API 요청에 따라 주고 받는 데이터는 거의 JSON 포맷이며 반환 값을 JSON으로 직렬화(serialize)하는 것이 필요하다.

### backend/post/serializers.py (새로 만들기)
{: .no_toc }

```python
from rest_framework import serializers
from .models import Post, Question

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = (
            'id',
            'title',
            'content',
        )

class QuestionSerializer(serializers.ModelSerializer):
    class Meta:
        model = Question
        fields = '__all__'
```



### backend/post/views.py
{: .no_toc }

MVC 패턴에서 Views 를 작성한다. 이 글에선 django admin에서 작성한 POST 글 외에 mysql 쿼리로 받은 데이터를 보내는 것을 테스트한다.


```python
from rest_framework import generics
from rest_framework.views import APIView
from rest_framework.response import Response

from .models import Post
from .serializers import PostSerializer
from . import models
from . import serializers

import pymysql
import json
from django.core.serializers.json import DjangoJSONEncoder


class ListPost(generics.ListCreateAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer

class DetailPost(generics.RetrieveUpdateDestroyAPIView):
    queryset = Post.objects.all()
    serializer_class = PostSerializer

class QuestionDetail(APIView):
    def get(self, request, format=None):

        conn = pymysql.connect(host='아이피주소', user='아이디', password='비밀번호', db='데이터베이스이름', charset='utf8',cursorclass=pymysql.cursors.DictCursor)
        print('연결성공')

        cursor = conn.cursor()
        sql = "select * from 테이블이름"
        cursor.execute(sql)
        data = json.dumps(cursor.fetchall(),cls=DjangoJSONEncoder)		
        # pymysql로 받은 데이터는 tuple이 기본형이다. .connect 메소드에 설정한 cursorclass와 json.dumps의 cls=DjangoJSONEncoder로 data를 JSON 객체로 파싱하면 React에서 데이터를 받을 때 추가 파싱 없이 JSON 객체로 쓸 수 있다.

        print(data)
        print(type(data))

        cursor.close()
        conn.close()

        question = models.Question(data=data)
        serializer = serializers.QuestionSerializer(question)
      
        return Response(serializer.data)
```

### backend/post/urls.py
{: .no_toc }

API URLs 요청을 앱의 Views에 연결하기 위한 URLs 패턴을 작성한다.

```python
from django.urls import path

from . import views

urlpatterns = [
    path('', views.ListPost.as_view()),
    path('<int:pk>/', views.DetailPost.as_view()),
    path('request', views.QuestionDetail.as_view()),
]
```

### backend/djangoreactapi/urls.py
{: .no_toc }

django 루트 디렉토리에는 앱 URLs 패턴의 부모 패턴을 작성한다.

```python
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('post.urls')),

]
```

다시 한번 서버를 실행해 서버 경로 뒤에 /api를 붙이면 admin에서 작성한 글 목록이 보인다. 



(사진)



부모 패턴 경로에 앱 urls.py에서 작성한 URLs 패턴을 붙이면 해당 POST만 조회할 수 있다.



(사진)



여기까지 끝나면 django의 API 서버 준비는 끝났다.



(사진)



### CORS 추가
{: .no_toc }

맨 처음 django-cors-headers 패키지를 설치했는데, 이는 script태그 안에서의 api를 통한 데이터의 접근제어를 위해 HTTP 접근제어 규약(CORS : Cross-Origin Resource Sharing)을 추가한 것이다. 기존의 HTTP요청은 img나 link 태그 등으로 다른 호스트의 css나 이미지파일 등의 리소스를 가져오는 것이 가능한데 script 태그로 쌓여진 코드에서의 다른 도메인에 대한 요청은 Same-origin policy에 대한 규약으로 인해 접근이 불가하다. 대부분의 경우 react는 script요청에 의해 페이지를 그리는 방식이므로 이제 대한 제약 해제가 필요하다. 이미 패키지 설치 후 settings.py 설정도 마쳤으므로 추가로 필요한 작업은 없다.



# React 앱 생성

React 앱 설치를 위해 create-react-app을 설치한다. 아래는 yarn이 설치된 경우에만 작동하며, 커맨드에서 React의 라이브러리를 설치하기 위한 명령어는 모두 npm 명령어로 설치 가능하다.

```bash
C:~/django>  yarn global add create-react-app
```

React 앱을 frontend라는 이름으로 설치한다. 설치가 끝나면 yarn start로 실행한다.

```bash
C:~/django>  create-react-app frontend
C:~/django>  cd frontend
C:~/django>  yarn start
```

### src/App.js
{: .no_toc }

```react
import React, { useState, useEffect } from 'react';
import TestApi from "./api/TestApi";

function App() {
  const [posts, postsChange] = useState([]);

  function listCall() { 
    TestApi.test()
    .then(res => {
      console.log(typeof(res.data));
      console.log(res.data);
      const posts = res.data;
      postsChange(posts);
    })
    .catch(err => {
      console.log('TestApi UseEffect 에러', err);
    });
  }

  function apiReceived() { 
    TestApi.request().then( res => {
      console.log(typeof(res.data.data));
      console.log(res.data.data);
      const temp = JSON.parse(res.data.data);
      console.log(temp);
    })
    .catch(err => {
      console.log('apiReceived 에러', err);
    });
  }

  return (
    <div>
      <button onClick={ apiReceived }>테스트</button>
      <button onClick={ listCall }>목록</button>
      {posts.map(item => (
        <div key={item.id}>
          <h1>{item.title}</h1>
          <span>{item.content}</span>
        </div>
      ))}
    </div>
  );
}

export default App;
```

### src/api/TestApi.js
{: .no_toc }

Axios 라이브러리를 통해 API의 URLs 패턴을 연결한 함수를 정의한다.

```react
import axios from 'axios';

const TEST_API_BASE_URL = "http://localhost:7000/api";

class TestApi {

    test() {
        return axios.get(TEST_API_BASE_URL);
    }
    
    request() {
        return axios.get(TEST_API_BASE_URL + "/request");
    }

}

export default new TestApi();
```



 React 앱을 실행해 두 개의 버튼을 누르며 django와 데이터 통신이 잘 되는지 확인한다.









