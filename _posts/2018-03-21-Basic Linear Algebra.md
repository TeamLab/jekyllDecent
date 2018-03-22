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
