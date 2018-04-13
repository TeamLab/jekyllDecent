---
layout:            post
title:             "(6) 이분법, Newton,Raphson, 리스트소개"
date:              2018-04-09 13:10:00 +0300
tags:              MIT
category:          MIT - Introduction to Computer Science and Programming in Python
author:            kimyoonjin
math:              true
published:         true
comments:          true
---
# [MIT - Introduction to Computer Science and Programming in Python](https://www.inflearn.com/course/mit-%EA%B3%B5%EA%B0%9C%EA%B0%95%EC%A2%8C-python/)

함수를 만드는 가치
- 계속해서 사용할 수 있다.


## 이분법

아래 그림처럼 답은 낮은 경계와 위의 경계 사이 어딘가에 있다.
   <img src="/media/img/이분법.png">

e.g.1)
```
4분의 1의 제곱근 = 2분의 1
4분의 1 < 2분의 1
```
e.g.2) 어떤 수의 제곱근이 어떤 수보다 큰 경우의 수정 코드
```
>>> def squareRootBi(x, epsilon):
...     assert x>= 0, 'x must be non-negative, not' + str(x)
...     assert epsilon > 0, 'epsilon must be postive, not' + str(epsilon)
...     low =0
...     high = max(x,1) # 수정한 코드
...     guess = (low + high) / 2.0
...     ctr =1
...     while abs(guess**2 -x) > epsilon and ctr <= 100:
...             if guess**2 < x:
...                     low = guess
...             else:
...                     high = guess
...             guess = (low + high)/2.0
...             ctr += 1
...     assert ctr <= 100, 'Iteration count exceeded'
...     print ('Bi method. Num. iterations: ', ctr, 'Estimate: ', guess)
...     return guess
```


### 추측값의 탄젠트 값 = 좋은 근사치
탄젠트 선은 해결책 근처의 값들의 곡선에 아주 좋은 근사치.
탄젠트의 x절편은 현재의 추측 값보다 더 정답에 가까워질 것이다.
탄젠트 선이 정말 안 맞는 곳 : **변곡점**
이 때. 뉴턴의 방법 시행한다.(첫 번째 추측값은 **0** 으로 하지말기)

e.g.1)  뉴턴 랩슨의 NR
```
>>> def squareRootNR(x, epsilon):
...     assert x>=0, 'x must be non-negative, not' + str(x)
...     assert epsilon > 0, 'epsilon must be positive, not' + str(epsilon)
...     x = float(x)
...     guess = x/2.0
...     guess = 0.001
...     diff = guess**2 - x
...     ctr = 1
...     while abs(diff) > epsilon and ctr <= 100:
...             guess = guess - diff/(2.0*guess)
...             diff = guess**2 - x
...             ctr += 1
...     assert ctr <= 100, 'Iteration count exceeded'
...     print('NR method. Num. iterations : ',ctr,'Estimate; ', guess
... )
...     return guess
...
>>> def compareMethods():
...     print(' squeareRoot(2,0.01)')
...     squareRootNR(2,0.01)
...     raw_input()
...
>>> compareMethods()
 squeareRoot(2,0.01)
NR method. Num. iterations :  14 Estimate;  1.4142398737439434
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 4, in compareMethods
NameError: name 'raw_input' is not defined
```

중요한 점
답이 틀릴 수 있다.
설명 :  두 개의 근사치가 있다.이것들이 다를 때, 어떤 것은 틀렸다라고 한다.이 부분에 대해선 학기말에 자연스레 알 수 있을 것이다.

### non sclar type, 불변 :  튜플, strings

### non scalr type, 변함 : 리스트
리스트는 문자열과 두 가지 면에서 다르다.
1. 리스트는 변경할 수 있다.
2. 값은 char타입이 될 필요가 없다.

e.g.1) 리스트의 가변성
```
>>> techs = ['MIT','Cal Techs']
>>> ivys = ['Havard','Yale','Brown']
>>> univs = []
>>> univs.append(techs)
>>> univs
[['MIT', 'Cal Techs']]
>>> for i in univs:
...     print (i)
...     for k in i:
...             print (k)
...
['MIT', 'Cal Techs']   # 첫 번째 출력 값 : 리스트
MIT           # 리스트 안의 요소들 출력
Cal Techs
>>> Univs = techs + ivys     #flatten
>>> Univs
['MIT', 'Cal Techs', 'Havard', 'Yale', 'Brown']
>>> ivys.remove('Havard') #리스트 안의 특정 요소 제거
>>> ivys
['Yale', 'Brown']
```
