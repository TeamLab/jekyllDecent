---
layout: post
title: "How To Install ELK Stack On Ubuntu 16.04"
date: 2018-04-02 16:55:00 +0300
tags: ELK Installation document
category: Tutorials
author: kimjinhong
math: false
---

이번 포스팅에서는 로그 수집 및 분석에 용이한 도구인 ELK Stack 구축 방법에 대해 설명하도록 하겠습니다.

## ELK Stack
ELK Stack이란 사용자가 서버로부터 원하는 모든 유형의 데이터를 가져와서
실시간으로 해당 데이터를 검색, 분석 및 시각화 할 수 있도록 도와주는 [Elastic](https://www.elastic.co/kr/)의 오픈소스 서비스 제품입니다.  
ELK는 Elastic search와 , Logstash 그리고 Kibana로 구성되어 있습니다.

<figure>
   <img src="{{ "/media/img/elk/workflow.png" | absolute_url }}" />
   <figcaption>ELK Work Flow</figcaption>
</figure>

ELK의 Flow를 간단히 살펴보면,  
(1) Logstash를 통해 로그 및 데이터를 수집하고,  
(2) Elastic search를 통해  데이터를 저장&검색하며,  
(3) Kibana를 통해 수집한 데이터를 시각화하여 분석합니다.   

그럼 각각의 구체적인 기능과 설치 방법에 대해 살펴보도록 하겠습니다.

## [Elastic Search](https://www.elastic.co/guide/en/elasticsearch/reference/current/index.html)

### What is Elastic
Elastic Search란 분산형 RESTful 검색 및 분석 엔진입니다.
Elastic Stack의 핵심으로, 데이터를 중심부에 저장하여 예상되는 항목을 검색하고 예상치 못한 항목을 밝혀낼 수 있습니다.
- 정형, 비정형, 위치정보, 메트릭 등 원하는 방법으로 다양한 유형의 검색을 수행하고 결합할 수 있습니다.
- 표준 RESTful API와 JSON을 사용합니다.
- 기존 관계형 데이터베이스에서 사용하는 방법처럼 CRUD(Create, Read, Update, Delete) 동작을 수행할 수 있습니다.

### Installation
 ```commandline
$ java -version
  ```
위의 명령어로 자바 설치 여부를 확인한다.

##### Install JAVA(java가 이미 우분투에 깔려있는 분은 [여기로](#Install Elasitc Search))
- Install JDK
    ```commandline
    $ sudo add-apt-repository ppa:webupd8team/java
    $ sudo apt-get update
    $ sudo apt-get install oracle-java8-installer
    ```

##### Install Elasitc Search
- [Elastic Search 다운로드 사이트](https://www.elastic.co/kr/downloads/elasticsearch)에서 최신 버전의 Elastic search(DEB)를 다운받습니다.
- 다운로드 받은 폴더로 이동한 후 아래의 명령어로 압축을 해제합니다.
    ```commandline
    # 압축 헤제
    $ sudo dpkg -i elasticsearch-6.2.3.deb
    ``` 
    - installed location: /usr/share/elasticsearch
    - config file location: /etc/elasticsearch
    - init script file location: /etc/init.d/elasticsearch

- Elastic search 서비스를 등록하여 실행 & 종료합니다.
    ```commandline
    # enable elasticsearch service
    $ sudo systemctl enable elasticsearch.service
    ```
    ```commandline
    # elasticsearch start
    $ sudo service elasticsearch start
    # check that elasticsearch is running
    $ curl -XGET 'localhost:9200'
    ```
    ```commandline
    # elasticsearch stop
    $ sudo service elasticsearch stop
    ```
    
    > `service elastic start` 실행 시 에러 발생할 경우  대처법  
    (1) **curl: (6) Could not resolve host: 'localhost** 발생 시,  
    localhost 대신 ip 주소 혹은 hostname을 입력한다.  
    (2) **curl: (7) Failed to connect to HOST_NAME port 9200: 연결이 거부됨** 발생 시,   
        - `sudo ufw allow 9200` 명령어로 9200번 포트를 열어준다.  
        - /etc/elasticsearch/elasticsearch.yml 파일에 `network.host: 0.0.0.0`, `network.bind_host: 0.0.0.0` 으로 설정해준다.
        
 
## [Logstash](https://www.elastic.co/guide/en/logstash/current/index.html)

### What is Logstash
Logstash란 오픈소스 서버측 데이터 처리 파이프라인으로,
다양한 소스에서 동시에 데이터를 수집(Ingest)하여 변환한 후 자주 사용하는 "스태쉬-보관소(Elastic search)"로 보냅니다. 
- [입력](https://www.elastic.co/guide/en/logstash/current/input-plugins.html) : 다양한 입력을 지원하여, 여러 공통 소스에서 이벤트들로 모든 소스, 사이즈 및 형태의 데이터를 수집합니다.
- [필터](https://www.elastic.co/guide/en/logstash/current/filter-plugins.html) : 데이터 이동 중 구문을 분석하고, 명명된 필드를 식별하여 구조를 구성하며, 이를 공통 형식으로 변환 & 통합하여 분석을 쉽게 만들고 시간을 단축시켜줍니다.
    - grok을 통해 비정형 데이터에서 구조 도출
    - IP 주소에서 위치 좌표 해독
    - PII 데이터를 익명화하고, 민감한 정보 필드를 완전히 제외
    - 데이터 소스, 형태 또는 스키마의 전체의 용이한 처리
- [출력](https://www.elastic.co/guide/en/logstash/current/output-plugins.html) : Elastic search 뿐만 아니라 다양한 stash로 데이터를 라우팅합니다.

### Installation
##### Install Logstash
- [Logstash 다운로드 사이트](https://www.elastic.co/kr/downloads/logstash)에서 최신 버전의 Logstash(DEB)를 다운받습니다.
- 다운로드 받은 폴더로 이동한 후 아래의 명령어로 압축을 해제합니다. 
    ```commandline
    # 압축 해제
    $ sudo dpkg -i logstash-6.2.3.deb 
    ```
##### Start Logstash
- 원하는 위치에 logstash **conf** 파일을 생성합니다. (**확장자 유의** ex. sample.conf)
- conf 파일에 입력, 필터, 출력에 대한 내용을 작성합니다. [[참고]](https://www.elastic.co/guide/en/logstash/6.2/advanced-pipeline.html)
- `sudo /usr/share/logstash/bin/logstash -f {config 파일 위치}` 명령어로 logstash를 실행합니다.
    > logstash start error 발생 시,  
    `sudo /usr/share/logstash/bin/logstash --path.settings=/etc/logstash/ -f {config 파일 위치}` 로 실행합니다.
    
## [Kibana](https://www.elastic.co/guide/en/kibana/current/index.html)

### What is Kibana
Kibana란  Elasticsearch 데이터를 시각화하고 실시간 검색을 지원하는 도구입니다.

### Installation
##### Install Kibana
- [Kibana 다운로드 사이트](https://www.elastic.co/kr/downloads/kibana)에서 최신 버전의 Kibana(DEB)를 다운받습니다.
- 다운로드 받은 폴더로 이동한 후 아래의 명령어로 압축을 해제합니다. 
    ```commandline
    # 압축 해제
    $ sudo dpkg -i kibana-6.2.3-amd64.deb
    ``` 

##### Setting Kibana
- /etc/kibana/kibana.yml 파일에 아래와 같이 설정해줍니다.
    ```commandline
    elasticsearch.url: http://{YOUR HOST NAME (default : localhost)}:9200
    server.host: {YOUR HOST NAME (default : localhost)}
    ```
   
##### Start Kibana
- 아래의 명령어로 kibana를 시작할 수 있습니다.
    ```commandline
    # 아래 두개의 명령어 중 편한 명령어를 사용하시면 됩니다.
    $ /usr/share/kibana/bin/kibana
    $ sudo service kibana start
    ```
- http://{YOUR HOST NAME}:5601 로 접속하면 Kibana 화면을 볼 수 있습니다.