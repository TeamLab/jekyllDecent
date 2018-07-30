---

layout: post title: 나만의 git 서버 - 간단 git lab 만들기 date: 2018-07-30 17:00:00 +0300 tags: git, gitlab category: Tutorials author: Chanwoo Jeong math: true published: true

comments: true
--------------

### 나만의 git 서버 git lab 만들기

#### git

분산 버전 관리 시스템을 대표하는 git은 연구자, 개발자라면 뗄 수 없는 시스템이다. 리눅스 토발즈가 리눅스 커널 개발을 위해 처음 만든 2005년 0.99버전 이후 2017년 2.14 버전에 이르기 까지 오픈소스를 기반으로 Ms, Google 등 다수의 기업과 개발자들이 사용하고 있다. git의 주요 특징은 저장 이력, 변경 기록 등을 추적하는 시스템으로 중앙 서버에 의존하지 않고 자신의 로컬 컴퓨터에서 작업을 한다. 또한 로컬 컴퓨터에서 끝낸 작업은 원격 저장소(Remote Repository)로 보내 개발 완료시 원격 저장소와 합치거나(Merge), 개발 중간에 Tag를 붙이고, 분기(Branch) 할 수 있다.

#### git hosting

git을 호스팅(hosting - 서버 공간의 전체 또는 일부 이용할 수 있게 임대하는 서비스)할 수 있는 대표적 사이트로 Github 이 있다. github는 Public은 무료, Private는 유료 서비스를 통해 수익을 얻는 구조를 가지고 있다. 대부분의 개인 github 사용자는 Public 버전을 사용하겠지만 오픈소스 정책으로 github의 모든 Repository를 공개하도록 하고 있다.

공동 프로젝트 또는 비공개 프로젝트를 원하는 단체 또는 개인은 Private 버전을 사용할 것이다. 그렇지만 https://github.com/pricing 에서 보다시피 팀을 단위로 Private을 이용할 시 시작 월 25달러 부터 해서 5인이 넘을 시 인원이 추가됨에 따라 월 9달러씩 추가가 된다. 다른 git 호스팅 사이트인 Bitbucket 또한 Private으로 일정 규모 이상의 팀은 사용자 수에 따라 월 요금을 청구한다.(https://ko.atlassian.com/software/bitbucket/pricing?tab=cloud\)

그렇다면 무료로 git 호스팅을 Private 하게 할 수 있는 대안은 어떤 것 일까?

#### Gitlab

Gitlab은 서버 설치를 목적으로 나온 호스팅 서비스로 다른 호스팅 사이트와 다르게 Community 버전으로 설치한다면 Private 서비스를 무료로 인원 제한 없이 이용할 수 있다. 또한 모든 Repository의 코드는 개인 서버에 저장이 된다.

다만, Community 버젼은 오픈소스 라이센스로 설치하는 과정이 길지만, 오픈소스 문서가 잘 정리되어 큰 문제는 없다. 물론 관리에 어려움이 따른 다면 웹 호스팅으로 이용이 가능하고 이 또한 무료로 Private 서비스를 제공하며, 협업자 수에 제한을 두지 않는다(단, 무료는 기술지원이 없다).

우리는 개인 서버에 코드를 저장하며, 무료로 Private Repository를 이용할 수 있는 Git server(원격 저장소) 구축을 목표로 하기에 Git Lab 기반으로 Git server를 구축해보고자 한다.

### GitLab 설치

설치 환경은 우분투 14.04.5 LTS 64비트 기준 입니다.[우분투 및 다른 환경 설치 파일](https://about.gitlab.com/installation/)

#### 설치

git lab community 버젼 gitlab -ce 설명서

[Install 문서](https://docs.gitlab.com/ce/install/README.html)[Gitlab ce 버전 문서](https://docs.gitlab.com/ce/README.html)[현재 막혀있는 우분투 ce 개발진 문서](https://about.gitlab.com/downloads/#ubuntu1604)

1) 기초 소프트웨어 설치

```console
$sudo apt-get install curl openssh-server ca-certificates postfix
```

-	postfix 선택 옵션은 우선 No configuration 선택
-	메일 발신 관련 내용으로 여기서는 다루지 않음
-	sudo dpkg-reconfigure postfix 으로 변경 가능

2) Gitlab 패키지 프로그램 저장소 추가

```console
$curl -sS https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
```

-	약간의 시간이 걸릴 수 있다.

3) 저장소 목록 업데이트

```console
$apt-get update
```

4) Gitlab Community Edition 설치

```console
$sudo apt-get install gitlab-ce
```

-	설치가 다 되었다면, 노란 별 기호로 GitLab 로고가 뜰 것이다.

5) GitLab 초기 설정

```console
$sudo gitlab-ctl reconfigure
```

-	Gitlab을 구성하는 gitlab.rb 루비 형식의 파일로 초기 설정을 하는 명령어 이다.

#### Gitlab 설정

Gitlab과 관련한 설정(메일,외부 url ...)은

```console
$cd / etc/gitlab/gitlab.rb
```

파일을 수정함으로 이뤄진다.

```console
$sudo vi cd /etc/gitlab/gitlab.rb
```

반드시 sudo 권한으로 에디터를 통해 파일을 열어야 루비 파일을 열고 수정 할 수 있다.

대표적으로 바꿀 곳은 3가지로 external_url, gitlab_workhorse['auth_backend'], unicorn['port'] 이다. 서버에 80, 8080포트가 비어있다면 크게 다루지 않아도 되나. 서버 80, 8080포트에 웹페이지, Apache 서버를 구동 하고 있는 경우가 있기에 수정하는 것을 권장한다. Gitlab은 추가 설정을 하지 않을 시 80 포트를 설정한다. 따라서 만약 수정을 하지 않을 시 포트 내부에서 충돌이 일어나 502 에러가 뜨게 될 것이다.

1) external_url - external_url 변경 전

```console
  ## external_url 'http://localhost'
```

이 부분을 수정하는데 이 부분은 gitlab 페이지의 웹사이트 주소이다. 반드시 앞 부분의 주석 처리(##)를 제거 하고 입력해야 루비 파일이 적용된다.

-	external_url 변경 후

```console
  external_url 'http://ip(입력):포트번호(입력)'
```

```console
  external_url 'http://localhost(입력):포트번호(입력)'
```

ip 또는 localhost 둘 중 하나를 입력 후 포트 번호는 80번, 8080을 피해서 하기를 바란다.

2) gitlab_workhorse & unicorn

```
gitlab_workhorse는 Gitlab의 역방향 프록시를 담당한다. 즉, 우리가 일반적으로 볼 수 있는 git의 push/ pull, Repository 다운로드, 업로드와 같은 HTTP 요청을 처리한다.
화면에서 esc를 누른 후 / 누르고 8080 을 입력하면 쉽게 찾을 수 있다.
```

-	주의 이 부분과 아래의 unicorn 부분의 포트는 80, 8080 포트를 피하여, 반드시 위의 external_url에서 설정한 포트와 다르게 설정하도록 한다. 웹 페이지와 http, 인증 관련된 내용이 충돌 할 수 있다. gitlab_workhorse 과 unicorn 의 포트 번호는 동일하다.

-	gitlab_workhorse 변경 전

```console
  ## gitlab_workhorse['auth_backend'] = "http://localhost:8080"
```

-	gitlab_workhorse 변경 후  

```console
  gitlab_workhorse['auth_backend'] = "http://localhost(수정안함):포트번호(입력)"
```

-	unicorn 변경 전  

```console
  ## unicorn['port'] = 8080
```

-	unicorn 변경 후  

```console
  unicorn['port'] = 포트번호(입력)
```

#### Gitlab 적용 및 재시작

```console
$sudo gitlab-ctl reconfigure
$sudo gitlab-ctl restart
```

위에서 처럼 루비파일을 수정하고 나면 Gitlab 재설정 명령어와 재시작 명령어를 통해 Gitlab을 적용 후 구동 할 수 있다.

```console
$sudo gitlab-ctl status
```

또한 status를 통해 gitlab 구동 상태 여부를 볼 수 있다.

이제 Gitlab의 틀은 완성했다. 접속을 해보도록 하겠다.

### Gitlab

이제 웹 브라우저를 키고 external_url로 설정한 주소를 입력하면 구축된 Gitlab 화면이 나온다.

```console
http://external_url
```

<figure>
   <img src="{{ "/media/img/git_lab_initial.png" | absolute_url }}" />
   <figcaption>git_lab 초기 접속</figcaption>  
</figure>

초기 화면에서 root 계정(Administrator)의 비밀번호를 설정하고 ID는 root 패스워드는 방금 설정한 패스워드로 로그인한다.

### Gitlab 실 사용

Gitlab은 Github와 같이 GUI 환경을 잘 구현해 놓았다. 따라서 사용에 있어 큰 문제가 없을 것이다. 기본적인 명령어 또한 Github와 동일하기 때문이다. 그렇지만 Github를 사용한 적이 없을 수 도 있기에 Gitlab의 Administrator를 통한 유저 추가, 권한 설정, 프로젝트 생성, 로컬 컴퓨터에서 사용을 다뤄보도록 하겠다.

#### 유저추가

유저를 추가 하기 위해서는 2가지 방법이 있다. 1. Administrator 계정이 직접 유저를 생성하고 유저가 메일을 통해 접속 및 패스워드 설정 2. 유저가 접속 링크를 통해 들어가서 계정 가입

1번 방법은 gitlab 루비 파일의 smtp 영역과 postfix 설정을 통해 가능하다. 그렇지만 이 부분은 다음에 다루도록 하고 가장 베이직한 2번 방법으로 다뤄 보겠다.

<figure>
   <img src="{{ "/media/img/gitlab_creating_account_1.png" | absolute_url }}" />
   <figcaption>git_lab 회원 가입</figcaption>  
</figure>
유저가 직접 정보들을 입력하고 Register하면 가입이 완료된다. 매우 간단하다.


#### 유저 관리 및 권한 설정
GitLab의 유저를 관리하기 위해서는 상단의 Admin area(스패너 모양)을 누르고
<figure>
   <img src="{{ "/media/img/gitlab_admin_area.png" | absolute_url }}" />
   <figcaption>git_lab Admin erea</figcaption>  
</figure>

왼쪽 Overview 부분의 User에 들어가서 해당 유저를 클릭 후 차단, 제거 그리고 우측의 Edit에 들어가서 권한 설정을 바꿀 수 있다.

<figure>
   <img src="{{ "/media/img/gitlab_user_setting_2.png" | absolute_url }}" />
   <figcaption>git_lab user setting</figcaption>  
</figure>
<figure>
   <img src="{{ "/media/img/gitlab_user_setting_3.png" | absolute_url }}" />
   <figcaption>git_lab user setting_2</figcaption>  
</figure>

#### 프로젝트 생성

프로젝트 생성에서는 Visibility Level 이 조정 가능하다. Private으로 깃랩 내부에서 자신만의 Reop를 만들거나 초대된 사람만 가능하게도 가능하고, Internal로 Gitlab의 유저만 접근 가능하게 할 수 있다.

<figure>
   <img src="{{ "/media/img/gitlab_project_make_3.png" | absolute_url }}" />
   <figcaption>git_lab project_1</figcaption>  
</figure>

<figure>
   <img src="{{ "/media/img/gitlab_project_make_1.png" | absolute_url }}" />
   <figcaption>git_lab project_2</figcaption>  
</figure>

원격 Repo 생성 이후 README 파일이나 License 파일을 추가할 수 있다.<figure> <img src="{{ "/media/img/gitlab_project_make_2.png" | absolute_url }}" /> <figcaption>git_lab project_3</figcaption>  
</figure>

#### 로컬에서 사용

이번에는 로컬 클라이언트 컴퓨터에서 Gitlab의 원격 Repo를 clone 해오고 수정 후 다시 원격 Repo에 push 하는 것을 설명하겠다. git 명령어에 익숙하다면 이 부분은 생각해도 무관하다.

먼저 git을 설치

```console
$sudo apt-get install git
```

##### git clone

서버의 프로젝트 URL을 카피 하여 git clone 명령어 뒤에 붙이면 폴더에 서버에서 만든 프로젝트가 복사된다.

```console
$cd '다운 위치 폴더'
$git clone http://'gitlab주소 : 포트'/ID/프로젝트명
$cd 프로젝트명
```

##### git push

로컬의 클라이언트 컴퓨터에서 작업을 완료한 후 서버에 있는 프로젝트에 합치는 방법은 git add, git commit, git push의 순서로 이루어진다. 예시로 로컬에서 test.txt 파일을 생성 후 push하는 것으로 하겠다.

```console
$echo "Gitlab is easy">test.txt
$git add test.txt
$git commit -m "테스트 문서 추가"
$git push -u origin master
```

git push가 이뤄질 때 계정과 패스워드를 입력하면 git push가 진행된다.

#### Gitlab 백업

### 정리

Gitlab은 Git 서버를 직접 구축하여 코드를 직접 보관하고 Private Repository를 경제적으로 이용하기에 적합한 대안이다. 인원수에 따른 비용이 없고, 오픈 소스로 부가적으로 크게는 Jenkins, Maven, Tomcat을 통한 빌드 관리 부터 배포까지의 기능을 부가할 수 있고 작게는 postfix, gitlab 루비파일의 smtp를 수정하여 메일관련 기능을 부가할 수 있다. 그리고 Gitlab은 Github에서 제공하는 이슈 관리, 프로젝트 진행상황을 다루는 마일스톤 기능을 제공한다.

나만의 서버를 구축함에 있어 당장에 큰 서버가 필요한 것이 아니라면 가벼운 대안으로 [라즈베리 파이 기반의 서버도](https://pimylifeup.com/raspberry-pi-gitlab/) 만들수 있고, [클라우드 기반의 서버도](https://docs.gitlab.com/ee/install/google_cloud_platform/) 만들 수 있다. 이 모든 것을 Gitlab은 다 지원한다. 다만, Gitlab은 5인까지 10G 용량의 Private 호스팅 서비스를 제공하기는 한다.
