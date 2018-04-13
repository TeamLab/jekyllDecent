---
layout:            post
title:             "(5) 부동소수점, successive refinement, 루트 찾기"
date:              2018-04-02 13:10:00 +0300
tags:              MIT
category:          MIT - Introduction to Computer Science and Programming in Python
author:            kimyoonjin
math:              true
published:         true
comments:          true
---
# [MIT - Introduction to Computer Science and Programming in Python](https://www.inflearn.com/course/mit-%EA%B3%B5%EA%B0%9C%EA%B0%95%EC%A2%8C-python/)  
이 강의는 2008년에 개설된 강의로, 현재로부터 10년전 버전의 파이썬으로 진행되고 있다.  
5강에서 파이썬을 실습하고 그에 관해 말하고자 하는 바가 10년이 지난 지금은 파이썬에서 모두 개선된 점이다. 띠리서, 옛날 파이썬 버전의 부족한 부분을 채우려고하는 강의가 5강이기 때문에 나는 이 5강이 현재와 동떨어져있다는 점을 말하고 싶다.  
5강을 공부하는 것은 10년전의 파이썬의 부족한 부분을 공부하는 것으로 이 글을 읽는 사람은 5강을 **20:42** 에서 시작하길 바란다.  
e.g.1)
```
>>> a = 2**1000
>>> a
10715086071862673209484250490600018105614048117055336074437503883703510511249361224931983788156958581275946729175531468251871452856923140435984577574698574803934567774824230985421074605062371141877954182153046474983581941267398767559165543946077062914571196477686542167660429831652624386837205668069376
>>> b = 2**999
>>> b
5357543035931336604742125245300009052807024058527668037218751941851755255624680612465991894078479290637973364587765734125935726428461570217992288787349287401967283887412115492710537302531185570938977091076523237491790970633699383779582771973038531457285598238843271083830214915826312193418602834034688
>>> a/b
2.0
```
e.g.2)
```
>>> x = 0.1
>>> x
0.1
>>> s = 0.0
>>> for i in range(10): s += 0.1
...
>>> s
0.9999999999999999
>>> print(s)
0.9999999999999999
```
(20:42 이후 내용)
### - 부동 소수점에 대한 한 가지 : ==
e.g.1)
```
>>> import math
>>> a = sqrt(2)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
NameError: name 'sqrt' is not defined
>>> a = math.sqrt(2)
>>> a
1.4142135623730951
>>> a *a
2.0000000000000004
>>> a*a == 2
False
```
a는 제곱근의 **근사치** 이기 때문에 두 수를 곱하면 원래 수가 되지 못한다.

### - 진짜 제곱근을 구하는 프로그램
**이슈**
1. 정확한 답이 아닐 수도 있다.
  - e.g) 2의 제곱근
2. 모든 추측을 계산할 수 없다.
  - 셀수없는 수라면 계산할 수 없다.
3. 추측, 검사, 개선
  - 각 추측이 이전의 추측보다 낫다는 가정 하에, 적어도 높은 가능성을 가지고 계산하는 방법 찾는다. 연차 근사법이 그 예이다.

#### 연차근사법
대략적인 구조는 다음과 같다.
- 초기 추측을 설정
- 반복문
- 반복문 안의 조건문을 넣고
- 추측 값 반환.  

반환된 값이 찾는 값과 가깝지 않다면 더 나은 추측을 얻는다.
충분한 추측이 없다면 error로 끝난다.  

e.g.1)
```
>>> def squareRootBi(x, epsilon):
...     assert x>= 0, 'x must be non-negative, not' + str(x)
...     assert epsilon > 0, 'epsilon must be postive, not' + str(epsilon)
...     low =0
...     high = x
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
...
>>> squareRootBi(4, 0.001)
Bi method. Num. iterations:  1 Estimate:  2.0
2.0
>>> squareRootBi(9, 0.0001)
Bi method. Num. iterations:  18 Estimate:  2.999988555908203
2.999988555908203
>>> squareRootBi(1000, 0.0001)
Bi method. Num. iterations:  29 Estimate:  31.62277676165104
31.62277676165104
>>> squareRootBi(2, 0.0001)
Bi method. Num. iterations:  14 Estimate:  1.4141845703125
1.4141845703125
  ```
얼마나 많이 반복하고, 반복의 수는 무엇인지, 그것은 가변적이다.
그리고 적어도 다음 두 가지와 관련되어있다.
1. 어떤 수의 제곱근이 우리가 찾는 것인 수의 크기
2. 제가 답하고 싶은 정확성  

위 두 가지에 대한 것은 다음 시간에 다뤄질 것이다.
