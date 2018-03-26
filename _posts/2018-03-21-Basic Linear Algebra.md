---
layout:            post
title:             "Basic Linear Algebra"
date:              2018-03-21 20:30:00 +0300
tags:              Basic Linear Algebra(SVD and Determinant over 3-Dimension)
category:          Basics for ML / DL
author:            jinhyundu
math:              true
---

 데이터를 컴퓨터에서 다루기 위해서 보통 데이터프레임 형태를 많이 사용합니다. 데이터프레임 형태는 Matrix와 비슷하게 여러 Rows와 Columns로 이루어져 있으며,  
 하나의 Row는 하나의 개체(Data instance)를 뜻하며, 각 Coumn은 데이터의 Feature를 의미합니다.  
 또한 Deep Learning의 여러 Cost Function들과 Activation Function, 머신 러닝의 여러 알고리즘 작동 원리를 이해하기 위해서는 행렬과 벡터의 이해와 그 연산에 대한  
 이해가 필수적이라 생각되어 짧은 지식이나마 기록하여 정리하고자 합니다.

 #### What is Linear Equations?

 $$ a_1x_1\,+\,a_2x_2\,+\,...\,+\,a_nx_n\,=\,b\;(a_1,\,a_2,\,...\,,\,a_n\,not\;all\;0)$$

선형식(Linear Equations)란, 1차항들의 다항 방정식을 의미합니다. 방정식의 최고차항은 1차항을 넘어서는 안 되며, $ xy, \, sinx,\, x^{1/2} $ 과 같은  
비선형 항도 존재해서는 안 됩니다.
<figure>
   <img src="{{ "/media/img/Linear equation examples.png" | absolute_url }}" />
   <figcaption>Linear equation 예제</figcaption>
</figure>

#### System of Linear Equations
같은 변수를 포함한 두 개 이상의 Linear Equations을 묶어 하나의 System으로 표현할 수 있습니다.  
이 변수들을 미지수라고 하며, 주어진 Linear Equations을 모두 충족시키는 미지수를 해(Solution)라고 합니다.  
또한 System의 해를 보다 쉽게 구하기 위해 각 방정식의 계수를 나열하여 표현하는 [첨가 행렬(Augmented Matrix)](https://ko.wikipedia.org/wiki/%EC%B2%A8%EA%B0%80_%ED%96%89%EB%A0%AC)에 대해 알아보겠습니다.
<figure>
   <img src="{{ "/media/img/System of Linear Equations.png" | absolute_url }}" />
   <figcaption>System of Linear Equations 예제</figcaption>
</figure>
그런데, 이 System은 해가 없을 수도 있고, 하나의 해를 갖거나 무수히 많은 해를 가질 수도 있습니다.  
이 때 하나 이상의 해를 갖는 경우를 **Consistent**, 해가 없는 경우를 **inconsistent**라고 합니다.

<figure>
   <img src="{{ "/media/img/consistent inconsistent 2d.png" | absolute_url }}" />
   <figcaption>solutions in 2d</figcaption>
</figure>
<figure>
   <img src="{{ "/media/img/consistent inconsistent 3d.png" | absolute_url }}" />
   <figcaption>solutions in 3d</figcaption>
</figure>

##### *Augmented Matrix*
매트릭스를 변수와 계수를 모두 곱해서 표현하는 것은 매우 번거롭고 직관적이지 못합니다.
그렇기 때문에 계수들만 표현하는 Augmented Matrix 표현방법을 주로 사용하며, 이 Augmented Matrix에
Row Operation(행 연산)을 사용하여 해를 쉽게 구할 수 있습니다.

바(|)를 기준으로 왼 쪽이 Linear System의 계수들이고, 오른 쪽은 해당 Linear combination의 상수항입니다.
<figure>
   <img src="{{ "/media/img/Augmented Matrix.png" | absolute_url }}" />
   <figcaption>Augmented Matrix example</figcaption>
</figure>
-----------------------------------
#### How to solve a system of linear equations.
Linear system의 해를 구하기 위한 가장 일반적인 방법은 [가우스 소거법](https://ko.wikipedia.org/wiki/%EA%B0%80%EC%9A%B0%EC%8A%A4_%EC%86%8C%EA%B1%B0%EB%B2%95)입니다.
Augmented Matrix로 표현된 상태에서 세 가지 종류의 행연산(Elementary Row Operations)을 이용하여
행사다리꼴 행렬(Echelon Matrix)을 찾는 과정을 통해 해를 찾습니다.

##### *Three Elementary Row Operations*
하나의 Linear system에 대해 세 가지 기본적인 행 연산을 수행할 수 있으며, 이 세 연산을 기본 행 연산(Elementary Row Operation)이라고 합니다. 세 연산은 아래와 같습니다.  
1) 한 행(Row)에 0이 아닌 상수를 곱하는 것  
2) 두 행의 위치를 바꾸는 것  
3) 한 행에 임의의 상수 k 를 곱해 다른 한 행에 더하는 것  
<figure>
   <img src="{{ "/media/img/Three Elementary Row Operations.png" | absolute_url }}" />
   <figcaption>Elementar Row Operations example</figcaption>
</figure>

##### *Echelon Matrix*
세 기본 행 연산을 통해 행사다리꼴 행렬(Echelon Matrix) 또는 기약행사다리꼴 행렬(Reduced row Echelon Matrix)을 찾음으로써
Linear System에 대한 해를 구할 수 있습니다.
행사다리꼴 행렬이 되기 위한 조건은 다음과 같습니다.  
1) 원소가 모두 0인 행은 모두 밑바닥에 위치해야 한다.  
2) 원소가 모두 0이 아닌 행이 있다면, 그 행에서 0이 아닌 첫 번째 원소는 1이어야 한다. (이 때 첫 번째 원소 1을 Leading one이라고 한다.)  
3) 원소가 모두 0이 아닌 행들은 위아래로 맞붙어 있어야 하며, 이 때 위 쪽의 Leading One은 아래 쪽의 Leading One보다 왼 쪽에 위치해야 한다.

위 세 조건을 만족하는 행렬을 행사다리꼴 행렬이라고 합니다. 여기에서 하나의 조건을 더 만족한다면 기약행사다리꼴 행렬이라고 표현합니다.

4) 각 행의 Leading One이 존재하는 열에서, 해당 Leading One을 제외한 나머지 원소들은 모두 0이어야 한다.
<figure>
   <img src="{{ "/media/img/Echelon Matrix.png" | absolute_url }}" />
   <figcaption>Echelon Matrix and Row Reduced Echelon Matrix</figcaption>
</figure>

##### *Gaussian Elimination(Gauss-Jordan Elimination)*
이제 Linear system의 해를 구하기 위한 준비는 모두 끝났습니다. 우리가 해야 할 일은 Augmented Matrix로 표현된 linear system을  
세 가지 기본 행 연산을 통해 행사다리꼴 행렬로 표현하기만 하면 됩니다. 이 때 행사다리꼴 형태로 표현하여 해를 찾는 것을 가우스 소거법(Gaussian Elimination), 기약행사다리꼴 형태로 표현하는 것을 가우스-조던 소거법(Gauss-Jordan Elimination)이라고 합니다.  
가우스 소거법은 **행렬식을 찾거나 역행렬을 찾을 때에도 유용하게 사용**되므로 충분히 이해해야 합니다.
<figure>
   <img src="{{ "/media/img/Gaussian Elimination.png" | absolute_url }}" />
   <figcaption>Gaussian Elimination example</figcaption>
</figure>
-----------------------------------
#### Matrices and Matrix Operations
Linear system의 개념과 해를 구하는 방법에 대해 간단히 알아보았습니다. 그러나, 우리가 선형대수를 공부하는 목적은  
이런 연립방정식을 쉽게 풀기 위함이 아닙니다. 데이터를 Matrix로 표현하고, 표현된 Matrix에 다양한 연산을 수행하기 위해서  
Transpose, Multiplying 등 다양한 연산과 [행렬식(Determinant)](https://ko.wikipedia.org/wiki/%ED%96%89%EB%A0%AC%EC%8B%9D), [특이값 분해(SVD)](https://ko.wikipedia.org/wiki/특잇값) 등이 갖는 의미와  
계산법을 알아보고자 합니다.  

##### *Square Matrix*
행과 열의 수가 같은 행렬을 정방행렬(Square Matrix)이라고 합니다. 뒤에 기술할 Determinant는 Square Matrix에서만  
정의되는 개념이며, [역행렬(Inverse Matrix)](https://ko.wikipedia.org/wiki/%EA%B0%80%EC%97%AD%ED%96%89%EB%A0%AC) 역시 Square Matrix에서만 정의되는 개념입니다.  
보통 행과 열의 크기 n에 따라 n차 정방행렬(square matrix of order n)이라고 표현합니다.
<figure>
   <img src="{{ "/media/img/Square Matrix.png" | absolute_url }}" />
   <figcaption>Square Matrix of order n</figcaption>
</figure>

##### *Main Diagonal*
행렬의 원소 중 같은 행과 열 순서에 있는 원소들을 뜻합니다. 즉, (1, 1), (2, 2), ... , (n, n)번째 원소들을 뜻하며,  
반드시 Square Matrix가 아니더라도 정의되는 개념입니다.  그러나 특별히 Square Matrix 중 이 대각 원소들을 제외한  
다른 모든 원소들이 0인 행렬을 [대각행렬(Diagonal Matrix)](https://ko.wikipedia.org/wiki/%EB%8C%80%EA%B0%81%ED%96%89%EB%A0%AC)이라고 부르며, 이 대각행렬 중 대각행렬이 1인 행렬을  
특별히 [단위행렬(Identity Matrix)](https://ko.wikipedia.org/wiki/%EB%8B%A8%EC%9C%84%ED%96%89%EB%A0%AC)이라고 합니다.
<figure>
   <img src="{{ "/media/img/Main Diagonal.png" | absolute_url }}" />
   <figcaption>Diagonal and Identity Matrix</figcaption>
</figure>
Identity Matrix는 행렬에서 곱셈의 항등원으로 사용되는 행렬이며, 일반적으로 크기 n인 단위행렬을 $ I_n $ 과 같이 표현합니다.  

##### *Addition, Subtraction and Scalar Product*
**크기가 같은 행렬**이라면 같은 위치에 있는 원소끼리(Elementwise) 덧셈과 뺄셈 연산을 할 수 있습니다.  
또한 행렬에 scalar를 곱하는 연산 역시 각 원소의 값에 scalar값을 곱하여 계산합니다. 이와 관련한 몇 가지 수학적 성질들은 다음과 같습니다.  
1) $ A + B = B + A $
2) $ (A + B) + C = A + (B + C) $
3) $ (kl)A = k(lA) $` ` ` ` ` ` ` `  * *for some constant k and l*
4) $ (k + l)A = kA + lA $` ` ` ` ` ` ` `  * *for some constant k and l*
5) $ k(A + B) = kA + kB $` ` ` ` ` ` ` `  * *for some constant k*
<figure>
   <img src="{{ "/media/img/Addition Subtraction.png" | absolute_url }}" />
   <figcaption>Elementwise operations</figcaption>
</figure>

##### *Multiplying Matrices*
앞서 크기가 같은 행렬일 때에만 연산할 수 있는 덧셈, 뺄셈과 크기에 관계없이 연산할 수 있는 scalar곱 연산방법을 알아보았습니다.  
행렬과 행렬을 곱하는 것은 지금까지의 연산 방법과는 조금 다릅니다. 우선, 행렬의 곱셈은 결합법칙은 성립하지만 **교환법칙은 성립하지 않습니다.**  
즉, $ AB \neq BA $이며, 심지어 $ AB $는 존재하더라도, $ BA $는 정의되지 않을 수 있습니다. 이에 관한 몇 가지 성질들을 알아보겠습니다.  
1) $ A(BC) = (AB)C $
2) $ A(B + C) = AB + AC $
3) $ (B + C)A = BA + CA $
4) $ k(AB) = (kA)B = A(kB) $` ` ` ` ` ` ` `  * *for some constant k*  

$ AB$를 연산하기 위해서는 **A의 열의 수와 B의 행의 수가 같아야 합니다.** 또한 연산의 결과로 A의 행의 수와 B의 열의 수의 크기를 갖는 행렬이 반환됩니다.  
행렬의 곱은 A의 행과 B의 열 간의 [점 곱셈(Dot product)](https://ko.wikipedia.org/wiki/%EC%8A%A4%EC%B9%BC%EB%9D%BC%EA%B3%B1)으로 정의됩니다. 행렬간의 곱셈이 연산되는 과정을 자세히 알아보겠습니다.
<figure>
   <img src="{{ "/media/img/Multiplying Matrices.png" | absolute_url }}" />
   <figcaption>Matrices Multiplying Process</figcaption>
</figure>  

이러한 행렬간 곱의 연산법을 이용하여 Linear System을 Coefficient Matrix, Variable Vector, 그리고 Constant Vecor로 분리할 수 있습니다.
<figure>
   <img src="{{ "/media/img/Describe by Multiplying.png" | absolute_url }}" />
   <figcaption>Describe a Linear System by Multiplying</figcaption>
</figure>  

##### *Transpose and Trace*
행렬의 원소들의 위치를 이용한 두 가지 연산을 소개하겠습니다. [전치행렬(Transpose)](https://ko.wikipedia.org/wiki/%EC%A0%84%EC%B9%98%ED%96%89%EB%A0%AC)은 앞서 공부한 Main Diagonal을 기준으로 행과 열을 바꾸는 연산입니다. 이렇게 행과 열의 바꾼 행렬을 전치행렬(Transposed Matrix)이라고 하며, 행렬 A에 대한 전치행렬은 $ A^T $로 표현합니다.  
전치 연산에는 몇 가지 특징이 있습니다.  
1) $ (M + N)^T = M^T + N^T $
2) $ (cM)^T = cM^T $
3) $ (M^T)^T = M $
4) $ (MN)T = N^TM^T $
5) $ (M^T)^{-1} = (M^{-1})^T $` ` ` ` ` ` ` `  * *if $ M^{-1} exists*  

특히 4), 5)의 경우 다양한 증명 등에 사용되므로, 익혀두는 것이 좋습니다.
<figure>
   <img src="{{ "/media/img/Transpose of a Matrix.png" | absolute_url }}" />
   <figcaption>Transpose Matrix example</figcaption>
</figure>  

[대각합(Trace)](https://ko.wikipedia.org/wiki/%ED%96%89%EB%A0%AC#%EB%8C%80%EA%B0%81%ED%95%A9)의 경우 좀 더 이해하기 쉽습니다. 우선, Trace는 **Square Matrix에서만 정의**됩니다. 연산하는 방법은, 그냥 Square Matrix의 모든 대각원소들을 합해주면 됩니다.  
Trace에 대해서도 몇 가지 특징이 있습니다.  
1) $ tr(A^T) = tr(A) $
2) $ tr(cA) = ctr(A) $` ` ` ` ` ` ` `  * *for some constant c*
3) $ tr(A + B) = tr(B + A) $
4) $ tr(A - B) = tr(B - A) $
5) $ tr(AB) = tr(BA) $
