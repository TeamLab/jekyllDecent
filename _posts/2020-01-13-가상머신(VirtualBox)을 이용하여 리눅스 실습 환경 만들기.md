---
layout: post
title: "가상머신(VirtualBox)을 이용하여 리눅스 실습 환경 만들기"
date: 2020-01-13 13:00:00 +0300
tags: linux, ubuntu, virtualbox
category: Tutorials
author: leejiheon
math: false
published: true
comments: true
---


이 포스팅은 Linux를 직접 설치 하지 않고 가상머신(VirtualBox)을 이용하여 Linux 실습 환경을 만드는 튜토리얼입니다. Windows에 VirtualBox를 설치하여 가상머신을 생성하고 가상 머신에 Linux(Ubuntu)를 설치하는 방법을 차례대로 말씀드리도록 하겠습니다.


## 가상머신(VirtualBox)을 이용하여 리눅스 실습 환경 만들기

### 리눅스
리눅스(Linux)는 리누스 토발즈가 커뮤니티 주체로 유닉스(UNIX) 운영체제를 기반으로 만들어진 컴퓨터 운영체제입니다. 리눅스(Linux)는 유닉스(UNIX)와 마찬가지로 다중 사용자, 다중 작업(멀티태스킹), 다중 스레드를 지원하는 네트워크 운영 체제(NOS)입니다. 현재는 상당수의 웹 서버와 모바일 장치(안드로이드 등)를 구동하는 운영체제로도 많이 이용이 되고 있습니다.
- LINUX (Linux is Not Unix)  : '리눅스는 유닉스가 아니다'의 약자
- NOS (Network Operation System) : 사람이 컴퓨터를 사용할 수 있도록 하는 프로그램 중 하나<br>

### 리눅스를 사용하는 이유
1. 모든 소스가 오픈되어 있어 광범위하게 사용된다.
2. 다중사용자, 다중작업을 지원하기 때문에 시스템적으로 서버를 운영하기에 적합하다.
3. 리눅스는 무료이며 개발 환경이 풍부하다.<br>

### 가상머신
하드웨어를 소프트웨어적으로 구현해서 그 위에서 운영체제가 작동하도록하는 기술이다.<br>

### 가상머신을 사용하는 이유
1. 다른 운영체제를 사용해야 하는 경우(Windows에서 Linux)
2. 독립된 작업공간이 필요한 경우(바이러스 회피, 백업)<br>

### VirtualBox
오라클에서 만든 가상머신 솔루션이다. 오픈소스이며 무료이다.<br>

### VirtualBox 설치
구글에서 VirtualBox를 검색하여 VirtualBox 홈페이지를 선택합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72217645-23b89800-3574-11ea-9e87-223fd75f2370.PNG"><br>
<br>왼쪽 메뉴에서 Downloads를 선택하여 Windows hosts를 클릭하면 VirtualBox가 다운로드 됩니다. 아래 All supported platforms를 클릭하여 확장팩도 다운로드 합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72218125-c6741500-357a-11ea-88e6-8d8db32aa0c0.PNG"><br>

### Ubuntu iso 파일 다운로드
<br>구글에서 Ubuntu를 검색하여 Download Ubuntu Desktop을 선택합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72218534-00dfb100-357f-11ea-90ef-5fe8d4f2b4fa.PNG"><br>
<br>Download 클릭하여 Ubuntu iso 파일을 다운로드 합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72218563-44d2b600-357f-11ea-9cbb-4ed41a87e7d5.PNG"><br>

### VirtualBox 설치 과정과 환경설정
<br>VirtualBox 설치파일을 더블클릭하여 설치를 시작합니다. 다음 화면까지 계속 Next, Yes, install 해주세요.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223037-5b443600-35ae-11ea-9c04-89de4766b159.PNG"><br>

<br>VirtualBox에서 USB 장치를 인식하기 위해 필요합니다. 설치를 해줍니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223054-86c72080-35ae-11ea-97d1-39e5fc0d96ab.PNG"><br>

<br>Finish 버튼을 클릭하면 VirtualBox가 실행됩니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223055-86c72080-35ae-11ea-8a03-5be912ac5d16.PNG"><br>

<br>VirtualBox에 대한 설정을 하기 위해 환경 설정 버튼을 클릭합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223056-8af33e00-35ae-11ea-9e09-f4bf7b86faeb.PNG"><br>

<br>기본 머신 폴더에서 가상 머신이 저장되는 위치를 변경할 수 있습니다. 초기값을 원한다면 그대로 두시면 됩니다. 그 다음 왼쪽 메뉴에서 입력을 선택합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223057-8b8bd480-35ae-11ea-88e3-dbec998e0657.PNG"><br>

<br>가상머신 탭을 선택하면 호스트 키 조합의 단축키 Right Control를 한글 윈도우에서는 동작하지 않기 때문에 **Shift+Ctrl**로 재설정합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223058-8b8bd480-35ae-11ea-93f9-4dd217e39a6a.PNG"><br>

<br>왼쪽 메뉴에서 확장을 선택하 오른쪽 +버튼을 클릭하여 VirtualBox 확장 패키지를 선택하고 열기 버튼을 클릭합니다. 그 다음 확장 기능 패키지 설치를 진행하고 설치 완료 후 환경설정에서 확인을 눌러서 나갑니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223059-8b8bd480-35ae-11ea-8ab5-f5cd2acf51d0.PNG"><br>

### Ubuntu를 위한 가상 머신 생성
가상머신은 윈도우의 하드웨어 자원을 우분투에서 사용할 수 있게 해줍니다. 새로 만들기를 클릭하여 가상머신의 이름을 적어주고 종류와 버전은 다음과 같이 진행합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223060-8b8bd480-35ae-11ea-9872-e8b17a4afe22.PNG"><br>

<br>가상머신에서 사용할 메모리 크기를 설정합니다. 초록색 범위 내에서 선택해야 합니다. (우분투의 최소 요구량은 2기가입니다. 메모리 여유가 있다면 더 크게 해도 됩니다.)<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223101-ee7d6b80-35ae-11ea-991f-6b32df5e4a69.PNG"><br>

<br>다음과 같이 계속 진행합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223102-ee7d6b80-35ae-11ea-89de-226f9ee820c2.PNG"><br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223103-ef160200-35ae-11ea-8a29-02fd42989eb7.PNG"><br>

<br>하드 공간 절약을 원한다면 동적할당, 빠른 속도를 원한다면 고정 크기를 선택하세요.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223104-ef160200-35ae-11ea-9d1d-7f6a06464392.PNG"><br>

<br>가상 하드 디스크에서 사용할 크기를 지정합니다. (우분투의 최소 요구량은 25기가이지만 추가 설치를 대비해 크게 잡는게 좋습니다.) 만들기를 클릭하면 가상머신 생성이 시작됩니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223105-ef160200-35ae-11ea-8b99-fc2f9def3b23.PNG"><br>

<br>가상머신을 부팅시키기 전에 몇가지 설정을 해줘야 합니다. 설정을 선택하고 왼쪽 메뉴 시스템을 클릭한 후 성능향상을 위해 칩셋을 ICH9로 변경합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223106-efae9880-35ae-11ea-804a-a651d70c13c8.PNG"><br>

<br>프로세서 탭을 클릭하여 가상머신이 사용할 프로세서 개수를 초록색 범위 내에서 설정해줍니다.<br>
<br><img src="(https://user-images.githubusercontent.com/48443734/72223235-f8ec3500-35af-11ea-856b-3d14442cec47.PNG"><br>

<br>왼쪽 디스플레이 메뉴에서 가상머신이 사용할 비디오 메모리 크기를 최대로 올려줍니다. 그리고  3차원 가속을 체크합니다. 2차원 가속은 사용할 수 없습니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223108-efae9880-35ae-11ea-8687-f1023b868ec6.PNG"><br>

<br>왼쪽 저장소 메뉴에서 부팅에 사용할 우분투 iso 이미지를 가상광학드라이브에 삽입합니다. (디스크 모양 버튼을 클릭하여 우분투 iso 파일을 열어주세요.)<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223154-651a6900-35af-11ea-828f-82f8d7d5bd44.PNG"><br>

<br>왼쪽 USB 메뉴에서 USB 3.0 컨트롤러로 변경한 후 확인 버튼을 클릭하여 설정을 마칩니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223162-706d9480-35af-11ea-949d-394ac78094e1.PNG"><br>

<br>시작을 클릭하면 가상머신에서 우분투가 부팅됩니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223163-706d9480-35af-11ea-8e4f-cc2ae6308321.PNG"><br>

<br>왼쪽 메뉴에서 한국어를 선택하고 Ubuntu 설치를 클릭한 후 다음화면까지 계속하기 버튼을 클릭합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223164-71062b00-35af-11ea-9fd4-d30e208734c1.PNG"><br>

<br>설치 용량을 줄이려면 최소 설치를 선택하세요. 아래 있는 체크 박스를 클릭합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223165-71062b00-35af-11ea-9e51-5d67ae098688.PNG"><br>

<br>디스크를 지우고 Ubuntu 설치를 선택하고 진행합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223166-71062b00-35af-11ea-8a81-3e797b889730.png"><br>

<br>정보를 입력한 후 계속하기 버튼을 클릭하면 설치가 진행됩니다. 설치가 완료되면 지금 다시 시작을 클릭합니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223167-71062b00-35af-11ea-92ed-057929bb7899.png"><br>

<br>재부팅 후 로그인 하면 다음과 같은 화면이 보입니다. 우분투 설치가 완료되었습니다.<br>
<br><img src="https://user-images.githubusercontent.com/48443734/72223168-719ec180-35af-11ea-875d-e75abfe1a68e.png"><br>

위의 모든 과정을 마친 후 Windows에서 Linux(Ubuntu)를 사용하는 것을 볼 수 있습니다. 실습 환경이 만들어졌으니 다음과 같은 리눅스 명령어들을 학습합니다.

### 리눅스 명령어 정리
- **디렉토리와 파일**
  - **PWD**<br>
  현재 경로 보기
  <pre>$ pwd</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71870559-af3bbe80-3159-11ea-94dc-cf828b629c1a.PNG">
  <pre>현재 경로는 /root 라고 표시되어 있다.</pre><br>

  - **CD**<br>
  해당 디렉토리 이동하기
  <pre>$ cd {인자값}
      cd . : 현재 디렉토리로 이동
      cd .. : 상위 디렉토리로 이동
      cd ~ : 홈 디렉토리로 이동
      cd - : 이전 작업 디렉토리로 이동</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71871481-a7314e00-315c-11ea-80b1-6691f79b4c04.PNG">
  <pre>~/workspace 로 이동하였다.</pre><br>

  - **LS**<br>
  파일 내역 출력
  <pre>$ ls {옵션} {디렉토리/파일}
      -a: 모든 파일과 디렉토리 표시
      -l: 자세히 출력</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71872085-af8a8880-315e-11ea-9f8e-c26bdee9cb30.PNG">
  <pre>/workspace 폴더의 모든 파일과 디렉토리를 자세히 출력</pre><br>

  - **CP**<br>
  파일, 디렉토리 복사
  <pre>$ cp {옵션} {복사파일} {복사위치}
      -f: 강제로 복사
      -r: 하위 경로 포함하여 복사
      -v: 복사 진행 상황 출력</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71872088-b0bbb580-315e-11ea-9525-c601a8fb11f2.PNG">
  <pre>test1.txt 파일의 내용을 복사하여 test2.txt 파일을 생성함</pre><br>

  - **MKDIR**<br>
  디렉토리 생성
  <pre>$ mkdir {옵션} {이름}
      -m: 디렉토리 생성 시 기본 권한 설정
      -p: 상위 디렉토리 생성
      --help: 도움말</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71874138-7fde7f00-3164-11ea-8d77-0385432db14c.PNG">
  <pre>mv_test 디렉토리가 생성된 것을 확인</pre><br>

  - **MV**<br>
  파일, 디렉토리 이동
  <pre>$ mv {옵션} {이동소스} {이동타겟}
      -f: 강제로 이동
      -v: 이동 진행 상태 출력</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71874141-810fac00-3164-11ea-975b-baeac4c0fbe8.PNG">
  <pre>test1.txt를 mv_test 디렉토리로 이동한 것을 확인</pre><br>

  - **RM**<br>
  파일, 디렉토리 삭제
  <pre>$ rm {옵션} {디렉토리/파일}
      -f: 강제삭제
      -r: 디렉토리 삭제 시 하위 경로와 파일 삭제
      -v: 파일 삭제 정보를 자세히 보여줌</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71874251-cb912880-3164-11ea-885d-0a566b4e9b48.PNG">
  <pre>mv_test 디렉토리가 삭제된 것을 확인</pre><br>

  - **CAT**<br>
  텍스트 파일 내용 출력
  <pre>$ cat {옵션} {파일 이름}
      >: 내용 덮어 씌우기
      >>: 기존 파일 내용 추가</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71874592-abae3480-3165-11ea-9f50-1190b9799d2b.PNG">
  <img src="https://user-images.githubusercontent.com/48443734/71874598-ae108e80-3165-11ea-8c9b-ff04354e1433.PNG">
  <pre>test2.txt 파일에 내용이 출력된 것을 확인</pre><br>

  - **TOUCH**<br>
  비어있는 파일 생성
  <pre>$ touch {파일이름}</pre><br>
  <img src="https://user-images.githubusercontent.com/48443734/72221195-a56fec00-359b-11ea-8a7b-b747cac85da5.PNG">
  <pre>test1.txt가 생성됨</pre><br>

  - **HEAD**<br>
  파일 내용 중 처음부터 10줄 출력
  <pre>$ head {파일이름}</pre><br>

  - **TAIL**<br>
  파일 내용중 마지막부터 10줄 출력<br>
  <pre>$ tail {파일이름} </pre><br>

  - **CHOWN**<br>
  chown(change the owner of a file) 파일의 소유권을 바꾸기 위해 사용<br>
  <pre>$ chown {옵션} {변경할유저이름:변경할그룹이름} {파일이름}
      -R : 하위 디렉토리에도 모든 권한 변경
    - chown {변경할유저이름} – 소유자만 변경
    - chown {:변경할그룹이름} – 그룹만 변경
    - chown {변경할유저이름:} – 소유자와 그룹 모두 동일한걸로 변경
    - chown {변경할유저이름:변경할그룹이름} – 소유자와 그룹을 서로 다른걸로 변경</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71878313-ad302a80-316e-11ea-95bb-b588dcc5ba66.PNG"><br>
  <per>빨간색 선 기준으로 왼쪽은 소유자, 오른쪽은 그룹</per><br>

  - **CHMOD**<br>
    - 파일의 권한을 변경
    - 8진수 형태와 심볼릭 형태로 사용 가능
    - 심볼릭이 기능적인 면으론 좋지만 조금 복잡
    - 쉽게 쓸려면 8진수 형태
  <pre>$ chmod {옵션} {8진수Permission} {파일명} – 8진수 형태
  $ chmod {옵션} {대상}{+/-/=}{rwx} {파일명} – 심볼릭 형태
      -R : 하위 디렉토리에도 모든 권한 변경
      -c : 권한 변경 파일내용을 출력</pre>
  <img src="https://user-images.githubusercontent.com/48443734/71878246-7e19b900-316e-11ea-9046-7167cbb3fbbe.PNG"><br>
  <per>d는 디렉토리, -는 파일<br>
  3칸 기준으로<br>
  첫번째는 owner
  두번째는 group
  세번째는 other</per>
  <pre>chmod 명령어 8진수<br>
  8진수 0~7은 아래와 같이 2진수로 표현이 가능
  0 : 000
  1 : 001
  2 : 010
  3 : 011
  4 : 100
  5 : 101
  6 : 110
  7 : 111
  위 2진수 세자리는 rwx 세자리와 일치하며 2진수가 1이면 해당 권한을 부여, 0이면 해당 권한을 제거</pre>

  <pre>chmod 명령어 심볼릭<br>
  - 대상<br>
  u : user의 권한
  g : group의 권한
  o : other의 권한
  a : 모든 사용자 권한

  - +/-/=<br>
  + : 해당 권한을 추가
  – : 해당 권한을 제거
  = : 해당 권한을 설정한데로 변경

  - rwx<br>
  r : 읽기 권한
  w : 쓰기 권한
  x : 실행 권한

- **검색**
  - **FIND**<br>
  파일 찾기<br>

    **- 파일명검색 <-name>**
         <pre>파일 명에 'test'가 들어간 파일 검색 : # $ find .-name '*test'</pre>
    **- 파일 형식 <-type>**
         <pre>- f : 일반파일
          - d : 디렉토리
    디렉토리이면서 이름중에 git이 들어간 것을 검색 : # $ find .-type d -name '*git'</pre>
    **- 파일의 소유자 <-user>**
         <pre>소유자가 root인 파일 검색 : # $ find .-user root</pre>
  - **GREP**<br>
  텍스트 검색 기능을 가짐
  <pre>$ grep {검색어} {파일이름}</pre>
  <img src="https://user-images.githubusercontent.com/48443734/72221491-9a6a8b00-359e-11ea-88d5-eaba8285ec82.PNG">
  <pre>test2.txt 파일의 내용에서 TEAM을 검색</pre><br>

- **프로세스**
  - **PS**<br>
  ps(process status) 시스템에서 현재 수행되고 있는 프로세스를 확인<br>
  <pre>$ ps aux</pre>
  <img src="https://user-images.githubusercontent.com/48443734/72221565-81160e80-359f-11ea-814d-503f52fbf36a.PNG"><br>

  - **Kill**<br>
  프로세스를 종료<br>
  <pre>$ kill {SIGNAL} {프로세스명}
  $ kill -9 {프로세스명}   # 응답없어도 강제종료
  $ kill -15 {프로세스명}  # 일반적 종료</pre>

  - **TOP**<br>
  시스템의 운용사항을 실시간으로 모니터링할 수 있는 유틸리티로, 윈도우의 작업관리자를 연상하면 된다. CPU, 메모리, 프로세스등을 확인할 수 있다.
  <pre>$ top</pre>
  <img src="https://user-images.githubusercontent.com/48443734/72221604-c9cdc780-359f-11ea-861d-b02ebf993bf0.PNG"><br>

  - **Daemon**<br>
  데몬 실행 및 종료
  <pre>$ sudo service {데몬이름} start # 데몬 실행
  $ sudo service {데몬이름} stop # 데몬 중지
  $ sudo service {데몬이름} restart # 데몬 재시작</pre><br>

- **SSH(원격제어)**
  - **SSH**<br>
  SSH server 설치 (sever 컴퓨터, client 컴퓨터)<br>
  <pre>$ sudo apt-get install openssh-server
  $ sudo apt-get install openssh-client</pre>

  SSH server 실행<br>
  <pre>$ sudo service ssh start</pre>

  Client 컴퓨터로 server 컴퓨터 원격제어하기<br>
  <pre>$ ssh {서버사용자이름}@{ip address}</pre><br>

  - **SSH-Keygen**<br>
  rsa라는 암호화 방식으로 키를 생성<br>
  <pre>$ ssh-keygen -t rsa</pre>

    키를 확인<br>
  <pre>$ ls -al ~/.ssh/</pre>

  파일|설명
  ----|-----
  id_rsa|private key, 절대로 타인에게 노출되면 안된다.
  id_rsa.pub|public key, 접속하려는 리모트 머신의 authorized_keys에 입력한다.
  authorized_keys|리모트 머신의 .ssh 디렉토리 아래에 위치하면서 id_rsa.pub 키의 값을 저장한다.<br>

  <br>기초적인 명령어들을 다루었습니다. 이제 리눅스 실습 환경이 필요할 때 윈도우에서 VirtualBox를 이용하여 리눅스를 직접 설치 하는 것 보다 더 쉽게 리눅스 실습 환경을 만들어서 학습하시면 될 것 같습니다.
