---
layout:            post
title:             "Matrix factorization"
date:              2018-07-30 12:30:00 +0300
tags:              matrix factorization
category:          Basics for ML / DL
author:            jeonhongjoon
math:              true
comments:          true
---

## Recommendation using matrix factorization

 Recommend System 을 구현하기 위해서는 Matrix factorization 에 대한 이해는 필수적이다.
 먼저 Recommend System 이란 정보 필터링 기술의 일종으로, 특정 사용자가 관심을 가질만한 정보 (영화, 음악, 책, 뉴스, 이미지, 웹 페이지 등)를 추천하는 것이다.
 본글에서는 Matrix factorization 을 이용해 사용자 영화추천 시스템을 예로 들고자 한다.

#### Matrix Factorization

Matrix factorization의 가정은 original data matrix R가 low rank matrix라는 것이다. 따라서 우리가 복원하는 R̂  역시 low rank 조건을 가지게 되므로 constrained optimization 문제로 바꿔서 쓸 수 있게 된다. 이 경우 optimal한 matrix completion의 objective function은 다음처럼 표현된다.

 $$min\, rank(\widehat{R})\,\, s.t. \Omega(r_{ui} - \widehat{r}_{ui}) = 0\,\forall_{u,i}$$

여기에서 Ω(Aij−Bij)는 matrix A와 B의 i,j 번째 element 중 하나라도 비어있으면 0, 둘 다 element가 존재하면 둘의 차이로 정의가 된다.
low rank 는 어떤 의미가 있는 지에 대해 먼저 생각해보면 먼저 모든 matrix는 다른 두 matrix의 곱으로 표현이 가능하다. 이때 만약 matrix의 rank 가 작다면 두 matrix 의 rank 역시 더 작은 형태로 표현이 가능하게 되는데 이는 원래 n x m matrix R 이 n이나 m보다 작은 k 만큼의 rank를 가졌을 때, R은 n x k matrix P와 m x k matrix Q의 곱으로 표현할 수 있다는 것이다. 즉, $R=PQ_{T}$ 으로 표현이 된다.

$ r_{ui} $ 가 사용자 u가 movie i를 얼마나 좋아할 것인지 나타내는 값이라는 가정 하에 $ r_{ui} = p_{u} \times q_{i} $ 라는 수식을 얻을 수 있는데
사용자 u가 movie i의 점수를 주는 방식은, 사용자 u의 movie들 에 대한 latent interest $p_{u}$와 그에 대응하는 movie 들의 숨겨진 특성 $q_{i}$에 의해 결정이 되는 것이다.
설명했던 rank를 minimize하는 문제는, 최대한 적은 latent feature를 사용하여 user와 movie를 표현하도록 하는 문제가 되는 것이다. 그러나 실제로는 rank condition 이 convex optimization 이 아니기 때문에 이 문제를 optimal 하게 풀 수 없다는 문제점이 존재한다. 이러한 문제에는 여러가지 방법이 있는데 본 글에서는 RMSE를 minize 하는 방법을 사용하도록 하겠다.

$$ min\sum_{u, i\in k} (r_{ui} - \widehat{r}_{ui})^2 \,\, s.t.\,\, rank(\widehat{R}) = k $$



#### The mathematics of matrix factorization

평점이 채워진 행렬을 R이라 할 때, User행렬 P와 Movie행렬 Q의 Matrix 는 $ \widehat{R} $ 로 표현된다. ($R$: 비어있는 곳이 없는 원래 데이터, $ \widehat{R} $: 비어있는 곳을 복구한 데이터)

$ R \approx P \times Q^T = \widehat{R} $

오차를 최소로 줄이기 위해 Gradient Descent를 적용하고 그를 위해서 error 에 대해서 정리하면,

$ e^2_{ij} = (r_{ui} - \widehat{r}_{ui})^2 = (r_{ui} - \sum_{k=1}^K p_{ik}q_{kj})^2 $

여기서 p, q 에 대해서 미분하게되면

$ \frac{\partial}{\partial q_{ik}}e^2_{ij} = -2(r_{ui} - \widehat{r}_{ui})(p_{ik}) = -2e_{ij}p_{ik}  $

$ \frac{\partial}{\partial q_{ik}}e^2_{ij} = -2(r_{ui} - \widehat{r}_{ui})(q_{kj}) = -2e_{ij}q_{kj}  $

따라서 아래와 같은 식을 얻게 된다.

$ p'_{ik} = p_{ik} + \alpha\frac{\partial}{\partial p_{ik}}e^2_{ij} = p_{ik} + 2\alpha e_{ij}q_{kj} $

$ q'_{ik} = q_{ik} + \alpha\frac{\partial}{\partial q_{ik}}e^2_{ij} = q_{ik} + 2\alpha e_{ij}p_{kj} $

$ E = \sum_{(u_{i}, d_{j}, r_{ij)}\in T} (r_{ij} - \sum_{k=1}^K p_{ik}q_{kj})^2$

자 여기서 한단계 더 나아가 Regularization term 을 주도록 하자. 이는 Overfitting 을 막기 위해서 주어지는 term 이다.

$ e^2_{ij} = (r_{ij} - \sum_{k=1}^K p_{ik}q_{kj})^2 + \frac{\beta}{2}\sum_{k=1}^K (\parallel P \parallel^2 + \parallel Q \parallel^2)$

$ p'_{ik} = p_{ik} + \alpha\frac{\partial}{\partial p_{ik}}e^2_{ij} = p_{ik} + (2e_{ij}q_{kj}-\beta p_{ik}) $  

$ q'_{ik} = q_{ik} + \alpha\frac{\partial}{\partial q_{ik}}e^2_{ij} = q_{ik} + (2e_{ij}p_{ik}-\beta q_{kj}) $

#### Recommend System using tensorflow

이제 tensorflow 기반의 TFFM(TensorFlow implementation of an arbitrary order Factorization Machine) 라이브러리를 사용하여 모델을 학습하고 MSE 값을 측정해보자.

데이터는 RecSys 2015 challenge dataset(http://2015.recsyschallenge.com/challenge.html) 을 사용하였다.
먼저 라이브러리를 불러오도록 하자.

``` python
import pandas as pd
import numpy as np
from collections import counter
import tensorflow as tf
from tffm import TFFMRegressor
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
```

데이터를 불러오고 one-hot encode 를 적용하자.

``` python
temp_buy_df = pd.read_csv(buys, names = ['Session ID', 'Timestamp', 'Item ID', "category", 'Quantity'])
temp_click_df = pd.read_csv(clicks, names = ['Session ID', 'Timestamp', 'Item ID', "category", 'Quantity'])

transforme_buy_df = pd.get_dummies(temp_buy_df)
transforme_click_df = pd.get_dummies(temp_click_df)

```
historical data 를 추출하고 원본데이터와 병합한다.

``` python
filtered_buy = transforme_buy_df.filter(regex = "item.*|Category.*")
filtered_click = transforme_click_df.filter(regex = "item.*|Category.*")

historical_buy_data = filtered_buy.groupby(filtered_buy.index).sum()
historical_buy_data = historical_buy_data.rename(columns=lambda column_name: "buy history:" + column_name)

historical_click_data = filtered_click.groupby(filtered_click.index).sum()
historical_click_data = historical_click_data.rename(columns=lambda column_name: "click history:" + column_name)

merged_buy = pd.merge(transformed_buy_df, historical_buy_data, left_index = True)
merged_click = pd.merge(merged_buy, historical_click_data, left_index = True)

```

Training set과 test set으로 분리하고(test set size 0.2), test set을 가지고 다시 cold start 셋을 구성한다. cold start 셋은 말 그대로 historical data 가 존재하지 않으므로 historical data 를 제거해주도록 한다.

``` python
X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.3)

X_test, X_test_cs, y_test, y_test_cs = train_test_split(X_test, y_test, test_size = 0.5)
X_test_cs = pd.DataFrame(X_test_cs, columns = merged_click.columns)

for column in X_test_cs.columns:
  if ('buy' in column or 'click' in column) and ('Category' in column):
    X_test_cs[column] = 0
```

TFFM 라이브러리를 사용하여 학습모델 구성하자.

``` python
model = TFFMRegressor(
  order = 2,
  rank = 7,
  optimizer = tf.train.AdamOptimizer(learning_rate = 0.1)
  n_epochs = 70,
  batch_size = -1,
  init_std = 0.001,
  input_type = 'dense'
)
```

Gradient Descent 적용하므로 이에 맞는 파라미터 값들을 설정해준다. 대게 Optimizer 는 Adam 을 사용한다. full size batch 를 사용하기위해 batch size 를 -1 로 사용하였다.

이제 각 데이터에 대해 학습을 실시하고 MSE 값을 측정해보자.

``` python
model.fit(X_train, y_train)
predictions = model.predict(X_test)
cs_predictions = model.predict(X_test_cs)

print('MSE: {}'.format(mean_squared_error(y_test, predictions)))
print('cold-start MSE: {}'.format(mean_squared_error(y_test_cs, cs_predictions)))
model.destroy()
```
이제 모델에 대한 결과 값을 확인하면 된다.

---------------------------------------
추천시스템을 이해하는데 필수적이며 기본이라고 할 수 있는 matrix factorization 에 대해서 알아보았다.
나도 계속 추천시스템을 배우는 중이라 아직 많이 미흡하지만 이 글이 추천시스템 입문자들에게 많은 도움이 되었으면 하는 바램이다.
