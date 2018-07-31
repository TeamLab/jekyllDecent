---
layout:            post
title:             "카카오톡 챗봇 만들기 with Python, Flask, AWS"
date:              2018-07-30 13:10:00 +0300
tags:              simple chatbot
category:          Tutorials
math:              false
author:            shinhyuna
published:         true
comments:          true
---

이 포스팅은 자연어 처리 없이 파이썬의 리스트로 사용자의 입력값을 비교해 응답하는 챗봇을 구현하는 튜토리얼입니다.
사용한 파이썬 모듈은 flask 이며, aws 서버를 사용했습니다. aws 인스턴스 생성에서부터 카카오톡 플러스친구와 연동하는 방법까지 차례대로 말씀드리도록 하겠습니다.


## 1. AWS 가입하기 및 인스턴스 생성, 설정하기

### 1. AWS 가입하기
AWS가 처음이신 분들은 Amazon Web Service Korea 에서 제공하는 영상을 참고해 회원가입을 진행하시는 것을 권장드립니다.
 - [초보자를 위한 AWS 클라우드 시작하기 - aws 가입하기 및 프리티어 소개 ](https://www.inflearn.com/course/aws-%ED%81%B4%EB%9D%BC%EC%9A%B0%EB%93%9C-%EC%8B%9C%EC%9E%91%ED%95%98%EA%B8%B0/)

- [스타트업을 위한 AWS 클라우드 시작하기 - aws 가입하기 및 프리티어 소개 ](https://www.inflearn.com/unit/%ec%8a%a4%ed%83%80%ed%8a%b8%ec%97%85%ec%9d%84-%ec%9c%84%ed%95%9c-aws-activate-%ea%b0%80%ec%9e%85%eb%b0%a9%eb%b2%95/)

 - [대학생을 위한 AWS 클라우드 시작하기 - aws 가입하기 및 프리티어 소개 ](https://www.inflearn.com/unit/%eb%8c%80%ed%95%99%ec%83%9d%ec%9d%84-%ec%9c%84%ed%95%9c-aws-educate-%ea%b0%80%ec%9e%85%eb%b0%a9%eb%b2%95/)

 회원가입을 마치셨다면, 인스턴스를 생성 해 보도록 하겠습니다.

 ### 2. 인스턴스 생성하기

1. 콘솔로 이동 해 주세요

   <img src="{{ "/media/img/chatbot_blogging/1.aws_console.png" | absolute_url }}">

2. EC2를 클릭해 이동 한 뒤, 인스턴스 시작을 클릭 해 주세요

   <img src="{{ "/media/img/chatbot_blogging/2.EC2.png" | absolute_url }}">

3. 저희가 생성 할 인스턴스의 운영체제는 프리티어 옵션으로 제공되는 Ubuntu server 16.04입니다.

   <img src="{{ "/media/img/chatbot_blogging/3.start_instance.png" | absolute_url }}">

4. CPU, 메모리 등의 옵션을 선택할 수 있습니다. 저희는 프리티어 옵션으로 제공되는 t2.micro를 선택하도록 하겠습니다.

   <img src="{{ "/media/img/chatbot_blogging/4.choice_instance.png" | absolute_url }}">

5. 세부정보 구성을 생략하고, 바로 검토 및 시작으로 이동 한 뒤 키페어를 생성/선택 해 주세요.

    <img src="{{ "/media/img/chatbot_blogging/5.make_key.png" | absolute_url }}">

    *키페어를 잃어버리지 않도록 이동식 장치 등에 보관 하시는것을 권장드립니다.*

6. 여러분들의 인스턴스가 생성되었습니다!

   <img src="{{ "/media/img/chatbot_blogging/6.instance_started.png" | absolute_url }}">


### 3. 탄력적 IP 할당하기
> 저희는 고정적인 한 컴퓨터를 빌리는 것이 아니라, 동적 클라우드이기 때문에 고정적으로 접근할 수 있는 IP주소를 필요로 합니다.

1. 탄력적 IP 할당하기

   탄력적 IP를 클릭해 탄력적 IP 페이지로 이동 해 주세요
   <img src="{{ "/media/img/chatbot_blogging/8.elastic_ip.png" | absolute_url }}">

   새 주소 할당을 클릭합니다.

   <img src="{{ "/media/img/chatbot_blogging/9.new_address.png" | absolute_url }}">

   할당버튼을 누르시면 IP주소가 할당됩니다.
   
   <img src="{{ "/media/img/chatbot_blogging/10.new_address_succeed.png" | absolute_url }}">

   완료하셨다면 위와 같은 화면이 나옵니다.

2. 할당된 탄력적 IP와 인스턴스 연결하기

   <img src="{{ "/media/img/chatbot_blogging/11.connect_address.png" | absolute_url }}">

   탄력적 IP 중 새로 생성한 IP를 우클릭해 주소 연결 페이지로 이동합니다.

   <img src="{{ "/media/img/chatbot_blogging/12.connect_address1.png" | absolute_url }}">

   연결하고자하는 인스턴스를 선택합니다.

   <img src="{{ "/media/img/chatbot_blogging/13.connect_address2.png" | absolute_url }}">

3.  이제 고정적인 IP주소가 생겼습니다!
   
   <img src="{{ "/media/img/chatbot_blogging/14.succeed_connect_address.png" | absolute_url }}">

   주소연결까지 마치셨다면, 마지막으로 보안그룹 설정을 해 주셔야 합니다.


### 4. 보안그룹 설정하기
  > 보안그룹 설정은 접근을 허용할 소스, 프로토콜, 포트범위 등을 설정하는 작업입니다.

1. 보안그룹 설정하기
   <img src="{{ "/media/img/chatbot_blogging/15.inbound_rule.png" | absolute_url }}">

   인스턴스 생성 및 설정을 마쳤습니다. 이제 고정적인 ip주소로 ssh 연결을 하실 수 있습니다!


## 2 플러스친구 개설하기

### 1. 플러스친구 등록하기
  1. [플러스친구 관리자센터](https://center-pf.kakao.com/)로 이동 해 주세요
  
     <img src="{{ "/media/img/chatbot_blogging/16.plus_friend_admin.png" | absolute_url }}">
        
  2. 플러스친구 만들기로 이동하신 다음, 오른쪽 상단의 새 플러스친구 만들기 버튼을 눌러주세요
  
     <img src="{{ "/media/img/chatbot_blogging/17.make_new_friend.png" | absolute_url }}">

  3. 원하는 아이디, 이름의 플러스친구를 만들어주세요.
  
     <img src="{{ "/media/img/chatbot_blogging/18.new_friend_name.png" | absolute_url }}">

     저는 '퀴즈봇'으로 시작하도록 하겠습니다.

  4. 플러스친구가 개설되었습니다.
  
     <img src="{{ "/media/img/chatbot_blogging/19.new_friend_succeed.png" | absolute_url }}">


### 2. 상세설정하기

  1. 개설이 완료되었다면 상세설정페이지의 스마트채팅 페이지로 이동 한 뒤 API형을 선택합니다.
  
     <img src="{{ "/media/img/chatbot_blogging/21.choice_faq.png" | absolute_url }}">


  2. 서버주소 및 개방한 포트를 입력 합니다.

     <img src="{{ "/media/img/chatbot_blogging/22.failed_because_keyboard_api_test.png" | absolute_url }}">

     여러분들의 IP주소와 flask 를 통해 개방한 포트번호로 URL을 등록하실 수 있습니다.

     *하지만 카카오톡 플러스친구는 API테스트를 통과해야 등록이 가능합니다.*

         따라서 현재 1. 포트 설정이 되지 않았고 2. 프로세스가 구동중이지 않으며 3. 코드를 작성하지않아 keyboard initialize가 되 지 않았기 때문에 에러가 발생합니다.

    만약 에러가 발생하게된다면 포트설정, 프로세스 구동 확인, 응답에 해당하는 코드 확인을 해 보시는것을 권장 드립니다.




## 3. 챗봇 모듈 생성하기

[카카오톡 플러스친구 API 기술문서](https://github.com/plusfriend/auto_reply)와 [파이썬과 flask로 만든 간단한 카카오톡챗봇](http://aidev.co.kr/chatbotdev/1268)을 참고해 챗봇을 구현했습니다.

  ### 0. Overview

  - 구현할 챗봇은 버튼형으로 오지선다로 퀴즈를 내고, 정답이면 알려주는 챗봇입니다.
  - 시나리오는 다음과 같습니다.

         (사용자의 요청) 버튼으로 퀴즈의 카테고리를 선택 - 퀴즈를 메시지로 보여주며 버튼으로 문항을 제공 - 정답일경우 정답메시지와 초기 버튼 띄우기, 정답이 아닐경우 오답 메시지와 초기 버튼 띄우기
         
         
   - 전체 코드는 다음과 같습니다.(개발환경에 flask가 설치되어있다 가정합니다.)

          #-*- coding: utf-8 -*-
          import os
          from flask import Flask, request, jsonify
          import json

          app = Flask(__name__)

          default_buttons = ['파이썬 퀴즈', '웹 퀴즈', '컴퓨터 퀴즈']

          quiz_dict = {'python_quiz_buttons' : ['Guido van Rossum', 'Mark Elliot Zuckerberg', 'Geoffrey Everest Hinton', 'Yann LeCun', 'Andrew Ng'],
         'web_quiz_buttons': ["HTML", "XML", "XHTML", "MXML", "JSON"],
         'computer_quiz_buttons' : ['애니악', "에드삭", "에드박", "유니박","일리악"]}

         answer_list = [quiz_dict['python_quiz_buttons'][0], quiz_dict['web_quiz_buttons'][1]]

         choice_list = list(quiz_dict.values())[0]
         for i in list(quiz_dict.values())[1:]:
             choice_list =  choice_list+ i


         @app.route('/keyboard')
         def keyboard():
             return jsonify({
                 'type' : 'buttons',
                 'buttons' : default_buttons
                })



         @app.route('/message', methods=["POST"])
         def true_or_false():
             dataRecieve = request.get_json()
             user_input = dataRecieve["content"]
             if user_input == default_buttons[0]:
                 response_data = {
                 'message' : {
                     "text":'다음 인물들 중 파이썬의 개발자는 누구입니까?'
                 },
                 "keyboard" : {
                     "buttons" : quiz_dict['python_quiz_buttons'],
                     "type" : "buttons",
                   }
                 }

             elif user_input == default_buttons[1]:

                 response_data = {
                   'message' : {
                       "text":'다음 보기중 마크업언어가 아닌것은 무엇입니까?'
                   },
                   "keyboard" : {
                       "type" : "buttons",
                       "buttons" : quiz_dict['web_quiz_buttons']
                   }
                 }
             elif user_input == default_buttons[2]:

                 response_data = {
                 'message' : {
                     "text":'다음 보기중 최초의 컴퓨터는 무엇입니까?'
                 },
                 "keyboard" : {
                       "type" : "buttons",
                       "buttons" : quiz_dict['computer_quiz_buttons']
                      }
                 }

             elif user_input in choice_list :
                  if user_input in answer_list:
                      response_data = {
                          'message' : {
                           "text":'정답입니다. 다른 퀴즈도 풀어보시겠어요?'
                          },
                          "keyboard" : {
                              "type" : "buttons",
                              "buttons" : default_buttons
                             }
                         }
             else:
                 response_data = {
                     'message' : {
                         "text":'틀렸습니다. 다른 퀴즈도 풀어보시겠어요?'},
                     "keyboard" : {
                        "type" : "buttons",
                        "buttons" : default_buttons
                     }
                   }

            return jsonify(response_data)


          if __name__=="__main__":
              app.run(host="0.0.0.0", port=5000)





 ### 1. keyboard initialize


    default_buttons = ['파이썬 퀴즈', '웹 퀴즈', '컴퓨터 퀴즈']

    @app.route('/keyboard')
    def keyboard():
      return jsonify({
        'type' : 'buttons',
        'buttons' : default_buttons
        })

  - 사용자가 최초로 채팅방에 접속시 보여 줄 버튼을 설정 해 줍니다.
  - default_buttons 라는 변수에 버튼을 저장해 처음과 마지막 응답에 사용할 예정입니다.



### 2. 응답 메시지 작성하기

  사용자의 요청이 들어 올 경우 그에 반응해 다른 버튼들을 보여주는 역할을 하는 코드입니다.


  1. json type로 들어온 사용자의 요청을 dataRecieve라는 변수에 저장 해 줍니다. 사용자의 요청은 앞서 제공한 버튼들 중 하나이고, 이를 미리 설정해 둔 버튼들과 비교할 예정입니다.

         @app.route('/message', methods=["POST"])
         def true_or_false():
             dataRecieve = request.get_json()
             user_input = dataRecieve["content"]

  2. 사용자의 요청과 리스트에 저장해 둔 값을 비교해 참일 경우 반응 할 메시지와 버튼에 관한 코드를 작성 합니다.

         if user_input == default_buttons[0]:
             response_data = {
                 'message' : {
                     "text":'다음 인물들 중 파이썬의 개발자는 누구입니까?'
                      },
                 "keyboard" : {
                     "buttons" : quiz_dict['python_quiz_buttons'],
                     "type" : "buttons",
                  }
               }



  3. 사용자의 요청이 정답인지, 아닌지에 대해 반응하는 코드를 작성 해 줍니다.
     > 퀴즈봇 예제의 경우 list에 하나씩 할당하기 보다 dict를 사용하게되면 직관적이고, 효율적인 코드를 구현할 수 있습니다.


         quiz_dict = {'python_quiz_buttons' : ['Guido van Rossum', 'Mark Elliot Zuckerberg', 'Geoffrey Everest Hinton', 'Yann LeCun', 'Andrew Ng'],
         'web_quiz_buttons': ["HTML", "XML", "XHTML", "MXML", "JSON"],
         'computer_quiz_buttons' : ['애니악', "에드삭", "에드박", "유니박","일리악"]}

         answer_list = [quiz_dict['python_quiz_buttons'][0], quiz_dict['web_quiz_buttons'][1]]

         choice_list = list(quiz_dict.values())[0]
         for i in list(quiz_dict.values())[1:]:
             choice_list =  choice_list+ i


         '''----------------------------------------------------------------------
                                       codes
         ----------------------------------------------------------------------'''

         elif user_input in choice_list :
             if user_input in answer_list:
               response_data = {
               'message' : {
                   "text":'정답입니다. 다른 퀴즈도 풀어보시겠어요?'
                   },
               "keyboard" : {
                   "type" : "buttons",
                   "buttons" : default_buttons
                   }
                 }
         else:
             response_data = {
             'message' : {
                 "text":'틀렸습니다. 다른 퀴즈도 풀어보시겠어요?'},
             "keyboard" : {
                 "type" : "buttons",
                 "buttons" : default_buttons
                 }
               }


  4.  마지막으로 flask 모듈을 사용해 스크립트를 구동하도록 하겠습니다.
      > port 변수에 할당한 포트번호는  카카오톡 api에게 허용하는 포트입니다.

           app = Flask(__name__)

          '''----------------------------------------------------------------------
                                      codes
          ----------------------------------------------------------------------'''


          if __name__=="__main__":
              app.run(host="0.0.0.0", port=5000)


## 3. 카카오톡과 연동하기

### 1. 모듈 실행하기
   <img src="{{ "/media/img/chatbot_blogging/26.run_quizbit.png" | absolute_url }}">

   서버의 5000번포트에서 모듈이 실행중인것을 확인하실 수 있습니다.

### 2. 카카오톡 플러스친구 관리자센터 - 스마트채팅 - 앱 URL 설정하기

   다음과 같이 앱 URL에 입력 해 주세요
   > 서버 주소 : flask에서 허용한 포트

   <img src="{{ "/media/img/chatbot_blogging/27.kakaotalk_integration.png" | absolute_url }}">

   이전과는 다르게 에러가 발생하지 않습니다.


### 3. 관리 - 상세설정 - 플러스친구 공개설정하기

   <img src="{{ "/media/img/chatbot_blogging/30.kakaotalk_settings.png" | absolute_url }}">

   챗봇이 완성됐습니다. 이제 플러스친구 이용이 가능해졌습니다.


### 4. 플러스친구 추가 후 확인해보기

   <img src="{{ "/media/img/chatbot_blogging/31.output.png" | absolute_url }}">


## reference :
  - [Teamlab,Applied Database System with Python Programming,2018.06.08,github](https://github.com/TEAMLAB-Lecture/database-101)
  - [plusfriendeam,카카오톡 플러스친구 API v.2.0 개요, 2018.07.19,github](https://github.com/plusfriend/auto_reply)
  - [깊은바다, 파이썬과 Flask로 만든 간단한 카카오톡 챗봇, 2017.04.22,AI Dev - 인공지능 개발자 모임](http://aidev.co.kr/chatbotdev/1268)
  - [VPC의 보안그룹, Amazon Virtual Private Cloud , n.d](https://docs.aws.amazon.com/ko_kr/AmazonVPC/latest/UserGuide/VPC_SecurityGroups.html)
