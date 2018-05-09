---
layout: post
title: docker로 django 개발하고 배포하기(+ nginx, gunicorn)
date: 2018-05-09 18:30:00 +0300
tags: docker, django
category: Tutorials
author: hyeonjuLee
math: false
published:         true
comments:          true
---

# docker로 django 개발하고 배포하기(+ nginx, gunicorn)

이 문서는 ubuntu 16.04 를 기준으로 작성되었습니다.

## Docker 란?
<img src="{{ "/media/img/docker-logo.png" | absolute_url }}" width='400'/>
- Docker는 애플리케이션을 신속하게 구축, 테스트 및 배포할 수 있는 소프트웨어 플랫폼을 말합니다.
- Docker는 소프트웨어를 컨테이너라는 표준화된 유닛으로 패키징하며, 이 컨테이너에는 라이브러리, 시스템 도구, 코드, 런타임 등 소프트웨어를 실행하는 데 필요한 모든 것이 포함되어 있습니다.

## Docker 설치하기
1. 자동 설치 스크립트를 이용하여 docker 설치하기(Terminal)
```
curl -fsSL https://get.docker.com/ | sudo sh
```
2. 도커 설치 확인하기(Terminal)
```
docker version
```

## docker-compose 사용하여 개발하기

### docker-compose 란?
- 하나의 서비스에 사용해야 하는 컨테이너가 여러 개인 경우, 컨테이너 여럿을 사용하는 도커 애플리케이션을 정의하고 실행하는 도구

### docker vs docker-compose
- Dockerfile vs Dockerfile-dev : 서버 구성을 문서화한 것
- docker build vs docker-compose build : 도커 이미지 만들기
- docker run 옵션 vs docker-compose.yml : 이미지에 붙이는 장식들
- docker run vs docker-compose up : 장식 붙은 이미지를 실제로 실행


### Dockerfile과 docker-compose 파일 만들기
- 여기서 사용하는 파일 모두(Dockerfile, docker-compose.yml, requirements.txt 등) project directory 의 root 위치에 만든다고 가정함.

- Dockerfile : Docker 의 자체 언어를 사용하여 이미지를 생성하는 과정을 작성한 파일
  ```
  FROM python:3.5 # 기본 이미지를 python3.5 로 설정
  ENV PYTHONUNBUFFERED 1 # 환경변수 설정 삭제할까?
  RUN mkdir /code # docker 내에서 /code 라는 이름의 폴더 생성
  WORKDIR /code # docker 내에서 코드를 실행할 폴더 위치를 /code 로 지정
  ADD requirements.txt /code/ # 로컬의 requirements.txt 파일을 docker /code/ 폴더로 마운트
  RUN pip install -r requirements.txt # docker 내 requirements.txt 파일을 이용하여 패키지 설치
  ADD . /code/ # 로컬 내 현재 위치에 있는 모든 파일 및 폴더를 docker 의 /code/ 폴더로 마운트
  ```

- docker-compose.yml
```
version : '2'
services: # docker-compose 에서 컨테이너(service) 단위를 묶음
    web: # 앱 service 이름
      build:
        dockerfile : Dockerfile # 빌드할 Docker 파일 위치

      command: python manage.py runserver 0.0.0.0:8000 # docker-compose up 을 command 창에 입력하면 실행할 명령어
      volumes: # 로컬 폴더(.)와 docker 의 /code 폴더를 마운트(연결)
        - .:/code
      ports: # docker 와 로컬에서 서로 입력 출력할 포트, 접근할 포트
        - "8000:8000"
      env: # docker 내에서 필요한 환경변수 설정
        - SECRET_KEY='abcabc'
        - DB_NAME='db'
```

### 개발 처음부터 docker 사용하기

1. requirements.txt 파일을 만듭니다.(django project 생성 및 실행에 필요한 최소한의 파이썬 패키지)  
  ```
  Django
  psycopg2
  ```

2. 터미널에서 아래 명령어로 django 프로젝트를 생성합니다.
  ```
  docker-compose run <service 이름> django-admin.py startproject <project 이름> .
  docker-compose run <service 이름> web django-admin.py startapp <app 이름> .
  ```

  - 명령 중 `django-admin.py startproject <project> .` 부분은 기존 django 사용법과 동일합니다.
  - 주의! service 이름은 docker-compose.yml 에 적힌 이름과 동일해야 합니다.


### 개발 중간부터 docker 사용하기
1. (requirements.txt 가 없을 경우) 아래 명령어를 사용하여 requirements.txt 파일을 만듭니다.(Terminal)
  ```
  pip freeze > requirements.txt
  ```
2. 아래 명령어를 입력합니다.(Terminal)

  - build 와 실행을 같이 할 경우
  ```
  docker-compose up --build
  ```

  - build 만 할 경우
  ```
  docker-compose build
  ```

  - 실행만 할 경우
  ```
  docker-compose up
  ```

3. (추가) DB 연동하기 : https://docs.docker.com/compose/django/

## nginx와 gunicorn 으로 django project 배포하기

1. requirements.txt 에 gunicorn을 추가합니다.

2. docker-compose.yml 에 nginx 내용을 추가하고, web 내의 내용(command, expose, ports 부분)을 수정합니다.
  ```
  version: '2'
  services:
    nginx:
      image: nginx:latest # nginx 서비스에서 사용할 도커 이미지
      ports:
        - "80:80"
      volumes:
        - .:/code
        - ./config/nginx:/etc/nginx/conf.d
      depends_on: # 서비스 간의 종속성 표현
        - web
    web:  
      build:
        dockerfile : Dockerfile
      command: gunicorn <프로젝트 이름>.wsgi:application --bind 0.0.0.0:8000
      volumes:
        - .:/code
      expose:
        - "8000"
    ```
3. nginx.conf 파일을 만듭니다.(파일 위치 : ./config/nginx/nginx.conf)

  ```
  upstream web {
    ip_hash;
    server web:8000; # 서버의 컨테이너 명
  }

  server {
    location / {
          proxy_pass http://web/;
      }
    listen 80;
    server_name localhost;
  }
  ```

4. settings.py 에 ALLOWED_HOSTS 를 추가합니다.
  ```
  ALLOWED_HOSTS = ['web']
  ```
5. 아래 명령어를 입력하여 실행한 후(Terminal), 인터넷 브라우저에 localhost를 입력하여 정상적으로 작동하는지 확인합니다.
  ```
  docker-compose up --build
  ```

### reference :
- https://aws.amazon.com/ko/docker/
- https://www.slideshare.net/raccoonyy/docker-compose-usages
- http://nberserk.github.io/default/2016/02/23/docker-django.html
- http://ruddra.com/2016/08/14/docker-django-nginx-postgres/
