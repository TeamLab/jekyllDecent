---
layout:            post
title:             "Basic Linear Algebra"
date:              2018-03-21 20:30:00 +0300
tags:              Basic Linear Algebra(SVD and Determinant over 3-Dimension)
category:          Basics for ML / DL
author:            Hyundoo-Jin
math:              true
---

 데이터를 컴퓨터에서 다루기 위해서 보통 데이터프레임 형태를 많이 사용합니다. 데이터프레임 형태는 Matrix와 비슷하게 여러 Rows와 Columns로 이루어져 있으며,  
 하나의 Row는 하나의 개체(Data instance)를 뜻하며, 각 Coumn은 데이터의 Feature를 의미합니다.  
 또한 Deep Learning의 여러 Cost Function들과 Activation Function, 머신 러닝의 여러 알고리즘 작동 원리를 이해하기 위해서는 행렬과 벡터의 이해와 그 연산에 대한  
 이해가 필수적이라 생각되어 짧은 지식이나마 기록하여 정리하고자 합니다.

 ## What is Linear Equations?

 $$ a_1x_1\,+\,a_2x_2\,+\,...\,+\,a_nx_n\,=\,b\;(a_1,\,a_2,\,...\,,\,a_n\,not\;all\;0)$$

선형식(Linear Equations)란, 1차항들의 다항 방정식을 의미합니다. 방정식의 최고차항은 1차항을 넘어서는 안 되며, $ xy, \, sinx,\, x^{1/2}) $ 과 같은  
비선형 항도 존재해서는 안 됩니다.
<figure>
   <img src="{{ "/media/img/Linear equation examples.png" | absolute_url }}" />
   <figcaption>Linear equation 예제</figcaption>
</figure>

#### System of Linear Equations
같은 변수를 포함한 두 개 이상의 Linear Equations을 묶어 하나의 System으로 표현할 수 있습니다.  
이 변수들을 미지수라고 하며, 주어진 Linear Equations을 모두 충족시키는 미지수를 해(Solution)라고 합니다.  
또한 System의 해를 보다 쉽게 구하기 위해 각 방정식의 계수를 나열하여 표현하는 첨가 행렬(Augmented Matrix)에 대해 알앙보겠습니다.
<figure>
   <img src="{{ "/media/img/System of Linear Equations.png" | absolute_url }}" />
   <figcaption>System of Linear Equations 예제</figcaption>
</figure>
그런데, 이 System은 해가 없을 수도 있고, 하나의 해를 갖거나 무수히 많은 해를 가질 수도 있습니다.  
이 때 하나 이상의 해를 갖는 경우를 **Consistent**, 해가 없는 경우를 **inconsistent** 라고 합니다.

<figure>
   <img src="{{ "/media/img/consistent inconsistent 2d.png" | absolute_url }}" />
   <figcaption>solutions in 2d</figcaption>
</figure>
<figure>
   <img src="{{ "/media/img/consistent inconsistent 3d.png" | absolute_url }}" />
   <figcaption>solutions in 3d</figcaption>
</figure>

#### How to solve a Linear System
이렇게 Augmented Matirx로 표현하면 보다 쉽게 해를 구할 수 있습니다. 각 행에 세 가지 기본 연산을 통해 해를 구하는 방법과, 이를 응용한  
가우스 소거법에 대해 알아보겠습니다.
