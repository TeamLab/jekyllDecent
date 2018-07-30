---
layout:            post
title:             fast.ai 라이브러리 설치하고 딥러닝 모델 만들기
date:              2018-07-28 17:00:00 +0300
tags:              fast.ai, CNN
category:          Tutorials
author:            hyeonjuLee
math:              true
published:         true
comments:          true
---

### fast.ai 라이브러리 설치하고 딥러닝 모델 만들기
fast.ai 라이브러리는 pytorch 를 기반으로 만들어졌습니다. 이 라이브러리는 딥러닝 모델을 만드는 코드 스킬 없이 빠르게 딥러닝 모델을 학습시켜서 사용할 수 있도록 하는 것을 목표로 개발되어서, 복잡한 구현 없이 딥러닝 모델을 생성할 수 있습니다.   
fast.ai 의 핵심은 모델 아키텍처의 선택과 함께 모든 중요한 데이터 선택 (예 : 전처리, 확대, 테스트, 교육 및 유효성 검사 세트, 다중 클래스 대 단일 클래스 분류 대 회귀 등)을 캡슐화한 클래스를 만든 것이라고 합니다.
fast.ai 라이브러리를 설치하는 방법과 사용하는 방법에 대해서 알아보도록 하겠습니다.

--------------------------------------
### Installation

#### 1. Normal installation

fast.ai 라이브러리는 Anaconda env 를 기본 환경으로 사용합니다. 설치하지 못한 분들은 `https://conda.io/docs/user-guide/install/linux.html` 를 보고 anaconda(or miniconda) 를 설치한 후 아래 과정을 진행해주시기 바랍니다.

1) github 프로젝트 다운받기
``` console
foo@bar:~$ git clone https://github.com/fastai/fastai.git
```

2) fastai 폴더로 이동
  ``` console
  foo@bar:~$ cd fastai
  ```

3) 가상환경 생성하기(conda)
- GPU 버전
  ``` console
  foo@bar:~/fastai$ conda env update
  ```

- CPU 버전
  ``` console
  foo@bar:~/fastai$ conda env update -f environment_cpu.yml
  ```

4) 환경 생성 확인 : fastai 라는 이름의 가상환경이 새로 만들어집니다.(cpu 버전은 fastai-cpu 입니다.)
  ``` console
  foo@bar:~/fastai$  conda env list
  ```

5) 가상환경 실행하기
- linux(ubnutu)
``` console
foo@bar:~/fastai$ source activate fastai
```

6) 설치 확인하기
``` console
foo@bar:~/fastai$ pytest tests
```

<img src="{{ "/media/img/fastai_pytests.png" | absolute_url }}" width=500/>


모두 pass 가 되면 fast.ai 실행을 위한 모든 환경에 대한 설치가 완료된 것입니다.  
혹시 하나라도 fail 이 나온다면 코드 및 가상환경을 업데이트한 후(git pull, conda env update) 다시 테스트해보시기 바랍니다.

#### 2. fastai 라이브러리 사용하기
설치가 끝나고 가상환경을 실행한 후에, fastai 라이브러리를 사용하려고 하면 git project의 root 폴더에서는 import 를 할 수 있습니다.  
하지만 다른 위치에서 fastai 라이브러리를 불러오려고 하면  `No module named fastai` 에러가 납니다.  
프로젝트 폴더 내의 fastai 폴더 안에는 라이브러리를 사용할 때 쓰는 코드들이 담겨 있어서,  git 프로젝트의 root 위치에서 불러온 fastai 는  
pip 나 conda 에 설치된 것이 아닌 프로젝트 폴더 내에 있는 fastai 폴더를 참조합니다.  

항상 저 파일들을 복사하면서 사용할 수는 없기 때문에 다른 폴더에서 편하게 사용하기 위해서 2가지 방법을 제시합니다.  
두 방법 중 하나를 선택해서 진행해주세요.

-  심볼릭 링크 만들기
``` console
foo@bar:~/fastai$ ln -s fastai <사용할 위치>
```

- pip 로 설치하기(두 가지 중 선택)

  - pip release 버전
    ``` console
    (fastai)foo@bar:~$ pip install fastai
    ```

  - github 최신 버전
    ``` console
    (fastai)foo@bar:~$ pip install git+https://github.com/fastai/fastai.git
    ```

#### 3. 업데이트 하기

- 1) code 업데이트
  ``` console
  foo@bar:~/fastai$ git pull
  ```

- 2) dependencies 업데이트
  ``` console
  foo@bar::~/fastai$ conda env update
  ```

----------------------------------

### 예제 실행하기
fastai 라이브러리 예제로 Convolutional Neural Network 를 사용해서 개와 고양이를 분류하는 모델을 만들어보겠습니다.  
가상환경을 실행한 후 아래 예제를 따라해주세요

#### 1) 사용할 데이터 다운받기
``` console
foo@bar:~$ wget http://files.fast.ai/data/dogscats.zip
```
혹은 브라우저 주소창에 `http://files.fast.ai/data/dogscats.zip `를 입력해서 다운받은 후 원하는 위치로 파일을 옮겨주세요.

##### 데이터 형태
파일을 받아 압축을 해제하면 아래 형태로 되어있습니다.
1. model(빈 폴더)
2. sample
  -  /train/cats
  -  /train/dogs
  -  /valid/cats
  -  /valid/dogs
  -  /valid/features.npy
  -  /valid/lables.npy
3. test1
4. train
  - /cats
  - /dogs
5. valid
  - /cats
  - /dogs

train, valid 폴더 내 cats, dogs 등은 나중에 모델에서 label 로 사용됩니다.

#### 2) 라이브러리 불러오기
``` python
from fastai.imports import * # all the main external libs we'll use
from fastai.transforms import * # crop, resize 등 데이터 변환
from fastai.conv_learner import * #cnn 모델을 만들기
from fastai.model import *
from fastai.dataset import *
from fastai.sgdr import *
from fastai.plots import * # 화면 그리기
```

#### 3) GPU 사용 확인
위에서 말한 것과 같이, fastai 는 torch 를 기반으로 설계되었습니다. 그래서 아래 명령어를 통해 gpu 사용 여부를 확인할 수 있습니다.
```
torch.cuda.is_available()
```
```
torch.backends.cudnn.enabled
```

#### 4) Pretrained model 사용하기

fastai 에서는 resnet, densenet, inception 등 여러 딥러닝 아키텍처를 직접 코드를 작성하지 않고도 사용할 수 있습니다.
이 아키텍처는 torchvision 에서 제공하며, 아래에서 사용할 resnet34 는 2015 Imagenet Competition 버전으로 작성되어 있습니다.

##### Train
``` python
from fastai.conv_learner import resnet34
from fastai.dataset import ImageClassifierData
from fastai.dataset import tfms_from_model
from fastai.conv_learner import ConvLearner

arch = resnet34
sz = 224
PATH = 'data/dogscats' # PATH 는 데이터 저장 위치로 수정해주세요
data = ImageClassifierData.from_paths(path=PATH, bs=64, tfms=tfms_from_model(arch, sz), trn_name='train', val_name='valid')
'''
path : 이미지 위치 지정
bz : batch_size 지정(default=64)
tfms : image trainsformer 지정
    - tfms_from_model : 모델 아키텍처, image input size, 어떤 형태로 변환할 것인지 값 지정(padding, zoom, crop 등)
train_name : training 대상 이미지를 모아둔 폴더명
val_name : validation 대상 이미지를 모아둔 폴더명
ex)
- data/dogscats/train/dog/
- data/dogscats/train/cat/
- data/dogscats/valid/dog/
- data/dogscats/valid/cat/
'''
learn = ConvLearner.pretrained(arch, data, precompute=True) # pretrained model 사용할 경우
# pretrained = True 이면 마지막 레이어에 대해 weight 업데이트
# pretrained = False 이면 전체 레이어에 대해 weight 업데이트
learn.fit(lrs=0.01, n_cycle=2) # learning_rate : 0.01, epoch
```

##### Predict
``` python
print(data.val_y) # 위 ImageClassifierData 로 지정한 validation set 의 y 값 array([0, 0, 0, ..., 1, 1, 1]) 등으로 출력
print(data.classes) # y 의 실제 value(valid 폴더 내 이미지 구분된 폴더 이름)

log_preds = learn.predict() # validation set 으로 지정한 이미지 예측 결과, log scale 된 값
preds = np.argmax(log_preds, axis=1)  # 예측값을 0, 1 로 변환시킴(큰 값을 가지는 value 의 index 저장) [ , 2] -> [, 1]
probs = np.exp(log_preds[:,1]) # 0 ~ 1 사이 값으로 변환
```

#### Choosing Learning rate
fastai 에서는 최적의 learing rate 찾는 데에 도움을 주는 모듈을 제공합니다.

``` python
learn = ConvLearner.pretrained(arch, data, precompute=True) # Learner 지정

learn.lr_find() # 적절한 learning rate 찾기

learn.sched.plot_lr() # plot 차트 그리기(가로축 iteration, 세로축 learning rate)
```
``` python
learn.sched.plot() # plot 차트 그리기(가로축 learning rate, 세로축 validation loss)
```
iteration 는 SGD로 한번만 학습해서 그린 결과로, 실제 모델을 학습했을 때는 결과가 달라질 수 있습니다.


#### Data augmentation
tfms_from_model의 파라미터인 aug_tfms에 적용할 함수 목록을 전달하면 이미지를 임의로 변경하여 데이터를 증가시킬 수 있습니다.  
fastai 라이브러리에 이미 정의된 모듈을 사용하여 data augmentaion을 해봅시다.

```python
from fastai.transforms import transforms_side_on, transforms_top_down,  transforms_basic
print(transforms_side_on) # 어떤 변환 기능이 사용되는지 확인할 수 있음
"""
<fastai.transforms.RandomRotate at 0x7fc5b671b400>,
<fastai.transforms.RandomLighting at 0x7fc5b671b470>,
<fastai.transforms.RandomFlip at 0x7fc5b671b4a8>]
"""
tfms = tfms_from_model(resnet34, sz, aug_tfms=transforms_side_on, max_zoom=1.1)
```
##### 확인

``` python
def plots(ims, figsize=(12,6), rows=1, titles=None):
    f = plt.figure(figsize=figsize)
    for i in range(len(ims)):
        sp = f.add_subplot(rows, len(ims)//rows, i+1)
        sp.axis('Off')
        if titles is not None: sp.set_title(titles[i], fontsize=16)
        plt.imshow(ims[i])

def get_augs():
    data = ImageClassifierData.from_paths(PATH, bs=2, tfms=tfms, num_workers=1)
    x,_ = next(iter(data.aug_dl))
    return data.trn_ds.denorm(x)[1]

ims = np.stack([get_augs() for i in range(6)])
plots(ims, rows=2)
```

<img src="{{ "/media/img/augmentation_result.png" | absolute_url }}" width=600/>

aug_tfms 값을 transforms_top_down, transforms_basic 으로도 바꿔서 데이터가 어떻게 바뀌는지도 확인해보세요.

##### 학습 모델에 적용하기

``` python
data = ImageClassifierData.from_paths(PATH, tfms=tfms)
learn = ConvLearner.pretrained(arch, data, precompute=True)
learn.fit(1e-2, 1)
```

#### model 저장하기
```python
learn.save('224_all') # '224_all' --> 저장할 이름
```
모델 파일은 {Path}/models 에 저장됩니다.

#### model 불러오기
```python
arch = resnet34
data = ImageClassifierData.from_paths(PATH, tfms=tfms_from_model(arch, sz), )
learn = ConvLearner.pretrained(arch, data, precompute=True)
learn.load('224_all')
```




fastai 라이브러리에서는 CNN 외에도 Column Data, NLP 관련된 기능을 사용할 수 있습니다.  
더 많은 예제는 https://github.com/fastai/fastai/tree/master/courses 에서 보고 실습할 수 있습니다.  
예제에 대한 설명 영상은 http://course.fast.ai/lessons/lesson1.html 에서 볼 수 있습니다.
