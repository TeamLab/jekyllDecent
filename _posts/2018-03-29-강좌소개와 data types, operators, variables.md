---
layout:            post
title:             " 강좌소개와 data types, operators, variables"
date:              2018-03-29 13:10:00 +0300
tags:              MIT
category:          Introduction to Computer Science and Programming in Python
author:            kimyoonjin
math:              true
published:         true
comments:          true
---
# [MIT - Introduction to Computer Science and Programming in Python](https://www.inflearn.com/course/mit-%EA%B3%B5%EA%B0%9C%EA%B0%95%EC%A2%8C-python/)

## 강좌소개
- MIT 공대에서 많은 사람들에게 지식을 전달하기 위해 운영하는 MIT OPEN COURSE WARE 에서 제공하는 컴퓨터과학 입문 강좌
-  프로그래밍 경험이 없거나 적은 학생들을 위한 강의
- 문제를 해결하는 데 computation 개념을 이해
- 학생들의 전공과 상관없이 작은 프로그램들을 짜면서 그들의 능력에 대한 자신감을 키워 유용한 목적들을 성취할 수 있도록 도움을 줌
- 강의를 진행하면서 사용하는 컴퓨터 프로그래밍 언어는 파이썬(Python)

**-컴퓨터 과학자처럼 생각한다는 것은?**
1. computation이 무엇인가?

2. 그에 앞서 지식은 무엇인가?  
 본 강의는 지식을 서술적인 지식과 명령하는 지식으로 나눔을 제안한다.이 때, 명령하는 지식은 computation이 기본적으로 무엇인지에 대한 것이다.


| 서술적 지식  | 명령하는 지식 (Recipe) |
|---|---|
| 사실의 진실|  무언가를 어떻게 추정하는지에 대한 서술 |
|2x=0, x=0|     repeat ( if ~ / else~ )   |

**-Program is a Recipe**
일반적인 레시피는 사실 초기 명령들의 작은 세트에 기반한다.  
computation을 하기 위해 우리는 **레시피** 를 설명하길 원하고 초기 명령들에 만들어진 단계의 순서이자 계속 거치는 단계의 순서들인 **제어의 흐름** 에 대해 설명하길 원한다. 마지막으로 레시피들을 서술하기 위해 **언어** 가 필요하다.
## Python
1. 고 수준인가? 저 수준인가?  
저 수준(assembly programming)
초기 명령들은 매우 간단한 연산을 통해 문자 그대로 데이터들을 메모리의 한 영역에서 다른 영역으로 움직이는 것.
고 수준
설계자가 초기 명령들을 더 많이 만든다.
```
파이썬은 고수준 언입니다.
```
2. 일반적인 언어인가? 무엇을 목표로 하는 언어인가?  
초기 명령 세트들이 넓은 범위의 응용 프로그램들을 지원해 줍니까 아니면 아주 특정한 응용프로그램을 목표로 합니까
```
파이썬은 기본적으로 일반적인 목적의 언어입니다.
예를 들어, 숫자보다 문자열을 조작하는 데 적합하다.
```
3. Interpretered vs Compiled  
Interpretered이란  
인터프로터 언어에서 소스코드라는 것을 가지는데 간단한 검사기를 거쳤지만 기본적으로 인터프리터로 간다. 인터프리터는 기계 내부에 있는 것으로 명령의 각 하나를 거치는 흐름을 제어하고 출력한다.
그래서 인터프리터는 런타임 때 코드를 바로 간단하게 연산하는 것.  
Compiled이란?  
컴파일된 언어에서는 중간 단계를 가진다.
소스코드가 있다면, 검사기 또는 컴파일러 또는 둘다 거친다. 그리고 오브젝트 코드를 만든다.
오브젝트 코드는 두 가지 일을 한다. 하나는 코드에서 버그를 잡는 것을 돕는다. 그리고 실제로 런하기 전에 코드를 종종 명령의 더욱 효과적인 단계로 변환한다.
```
파이썬은 Interpretered 언어입니다.
코드를 실행하기 전 디버그를 잡는 데는 좋지 않지만, 주저않고 진행할 때 디버깅이 쉽다.
```

4. 언어의 의미 : 고정적 의미 VS 완전한 의미   
고정적의미란  
기본적으로 어느 프로그램이 의미 있는가를 말한다.
문법적으로 맞지만 의미적으로 봤을 땐 의미가 없는 것
고정적의미에서 어떤 표현을 해서 어떤의미를 가지는지
ex) "My desk is Rosa"
```
파이썬은 문법적으로 고정적으로 맞지 않는 코드를 찾도록 도와준다.
그러나 모든 것이 정확하게 잡히는 것은 아니다.
```
완전한 의미란  
프로그램이 의미하는 것은 무엇인가를 말한다.
코드를 실행하면 무슨일이 일어나는가를 의미하는 것이다.

```
Q : 파이썬에서의 합법적인 표현은 무엇입니까?   
A : 문법적으로 맞고 옳은 고정된 의미를 가지면 코드 실행 후 옳은 답을 줄 수 있다.
```

### 변수와 연산
변수
 - 숫자
   - integer(정수)
   - float(실수)
 - 문자열
   - string

```
>>> 52
52
>>> "abc"
'abc'
>>> "ac bc"
'ac bc'
>>> '52'
'52'
```

연산
```
>>> 52*7
364
>>> print(52*7)
364
>>> print("52*7")
52*7
>>> print('ab')
ab
>>> print('3'*'3')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can't multiply sequence by non-int of type 'str'
>>> print('3'*3)
333
```
```
>>> 3*5
15
>>> 3+5
8
>>> 3**5
243
>>> 3/5
0.6
>>> 3.0/5
0.6
>>> print("ab" + "cde")
abcde
>>> mystring = "rosa"
>>> mystring
'rosa'
>>> mystring+'flower'
'rosaflower'
```
