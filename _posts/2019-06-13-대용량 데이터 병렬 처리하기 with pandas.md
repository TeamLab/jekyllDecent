---
layout:            post
title:             "대용량 데이터 병렬 처리하기 with pandas"
date:              2018-03-30 13:30:00 +0300
tags:              Big Data, Pandas, Python, Multiprocessing, Parallel, Preprocessing
category:          Big Data
author:            choiseokkyu
math:              true
published:         true
comments:          true
---
# Pandas와 multiprocessing을 이용한 대용량 특허 데이터 처리

모델 학습에 있어서 학습하기에 충분히 많은 양의 데이터를 모으는 일은 매우 중요한 일입니다. 어떻게 해서 데이터를 충분히 모았다고 한들 모은 데이터로 모델을 학습하기 위해서는 전처리가 필요합니다. 하지만 데이터가 많으면 많을수록 전처리 시간은 더욱 늘어나게 됩니다. 우리가 가지고 있는 자원은 한정되어 있기 때문에 자원을 최대한 효율적으로 사용하여 전처리 시간을 줄여줘야 합니다. 이 글에서는 보다 효율적인 전처리를 위해 Python의 데이터 분석 도구인 Pandas와 프로세스 기반 병렬 처리 모듈인 multiprocessing을 간단히 소개하겠습니다.

## Pandas Dataframe
특허데이터의 간단한 예시를 보면 abstract 같은 텍스트데이터와 cpc code, reference 같은 메타데이터로 구성되어있습니다. 그 중 cpc code의 그래프 정보를 이용하기 위한 전처리로 각 특허의 코드의 길이를 구해주고 코드가 하나인 즉 연결관계가 없는 특허들을 제거해 줄 것입니다. pandas는 내부적으로 병렬처리 기능을 지원하지 않기 때문에 데이터가 크고 전처리 함수가 복잡하면 시간이 매우 오래 걸릴 수 있습니다. 그러므로 이 글에서는 데이터프레임을 CPU 코어 수 만큼 분할한 뒤 multiprocessing을 이용해 전처리를 해준 뒤 다시 합치는 방식을 이용할 것입니다.

<img src = "https://i.imgur.com/skDOohD.png">

우선 자신의 cpu에 있는 코어의 수를 multiprocessing.cpu_count()를 이용하여 확인해줍니다.

```python
import os
import pandas as pd
import numpy as np
import multiprocessing as mp

num_cores = mp.cpu_count() # cpu의 코어 수를 반환

```

그 다음 데이터프레임을 코어의 수로 나눠주고 전처리 함수를 multiprocessing으로 처리해주는 함수를 만들어 줍니다.

```python
def parallel_dataframe(df, func):
    df_split = np.array_split(df, num_cores)
    pool = Pool(num_core)
    df = pd.concat(pool.map(func, df_split))
    pool.close()
    pool.join()
    return df
```

그 다음 병렬로 처리될 각 특허의 cpc code의 개수를 구해주는 전처리 함수를 만들어 줍니다. 각각 다른 프로세스로 돌아가는지를 확인하기 위해 os.getpid()를 이용하여 PID(Process IDentifier)를 print 해주도록 합니다.

```python
def multiply_columns(data):
    print('PID :', os.getpid())
    data['length_of_word'] = data['species'].apply(lambda x : len(x))
    return data
```
결과를 보면 각기 다른 8개의 프로세스로 처리가 되었음을 알 수 있습니다.

<img src = "https://i.imgur.com/oN6DX1M.png">

엄청 큰 데이터가 아니라면 map과 apply만으로도 충분히 빠른 성능을 낼 수 있기 때문에 multiprocessing이 항상 해답은
아닙니다.
