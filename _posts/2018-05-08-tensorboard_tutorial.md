---
layout: post
title: "TensorBoard: Visualizing Learning"
date: 2018-04-30
tags: TensorBoard Tutorial
category: Tutorials
author: jangyehoon
---

이번 포스팅에서는 TensorBoard의 간단한 사용법에 대해 다양한 tensorflow를 활용한 예시 code와 함께 알아보도록 하겠습니다.

-----------------


> *TensorBoard 사용법에 대해 궁금해 하는 사람들 중 TensorFlow를 모르는 사람은 없을 거라 생각하지만 간단히 TensorFlow와 TensorBoard에 대해 살펴보고 Tutorial로 넘어가도록 하겠습니다 :-)*


## 1. TensorFlow? TensorBoard?

### [TensorFlow](https://www.tensorflow.org/)
TensorFlow는 Google의 인공지능 연구 조직인 Google Brain 팀이 기계학습(Machine Learning)과 심층 신경망(Deep Neural Network) 연구를 위해 개발한 오픈소스 소프트웨어 라이브러리입니다. TensorFlow는 데이터 플로우 그래프(Data Flow Graph) 방식을 사용하여 모델 내에서 수행되는 방대한 양의 연산과 데이터의 흐름을 표현합니다. 데이터 플로우 그래프는 수학연산을 나타내는 노드(node)와 그 노드들 간의 입/출력 관계를 나타내는 엣지(edge)로 구성되어 있으며, **TensorFlow** 의 이름에서도 알 수 있듯이 다차원 데이터 배열인 Tensor는 이 엣지들을 통해 노드 간 흐름을 따릅니다.

### [TensorBoard](https://www.tensorflow.org/programmers_guide/summaries_and_tensorboard)
TensorFlow는 매우 견고하면서도 유연한 아키텍처를 가지고 있어 별도의 코드 수정 없이 학습 모델을 설계할 수 있고 실행시킬 수 있습니다. 하지만 거대하고 복잡한 뉴럴넷의 코드만을 보고 계산과정이나 데이터의 흐름을 이해하기란 매우 추상적이며 복잡하고 난해할 수 있습니다. 디버깅과 최적화도 매우 어려운 작업이 될 수 있습니다. 이에 TensorFlow는 **TensorBoard** 라는 시각화 툴을 통해 사용자들이 그래프 내에서 Tensor의 흐름을 쉽게 이해하고, 각 변수들의 양적 변화를 한 눈에 알 수 있도록 돕는 여러 기능을 제공합니다.


## 2. TensorBoard

### Tensor Operation
TensorBoard를 본격적으로 시작하기에 앞서 간단한 TensorFlow 코드를 살펴보겠습니다.

{% highlight python %}
import tensorflow as tf

const1 = tf.constant(3.0, name='const1')
const2 = tf.constant(4.0, name='const2')
print(const1, const2)

add = tf.add(const1, const2)
print(add)

sess = tf.Session()
sess.run(add)

{% endhighlight %}

위의 코드를 실행시켜 보면 터미널창에는 아래와 같은 화면이 출력될 것입니다.
{% highlight pycon %}
Tensor("const1:0", shape=(), dtype=float32) Tensor("const2:0", shape=(), dtype=float32)
Tensor("Add:0", shape=(), dtype=float32)
{% endhighlight %}

이처럼 TensorFlow는 `Session`이 만들어져서 `run`함수가 호출되기 전까지는 각각의 **Tensor** 만 생성이 될 뿐 어떠한 연산도 실제로 실행되지 않습니다. 연산에 필요한 모든 Tensor들이 준비된 후 Session이 만들어지고 난 후, 실제 연산은 `sess.run(add)` 이 한 줄에서 실행됩니다. 위 코드의 마지막줄에 `print(sess.run(add))`를 추가하여 다시 실행하면 `const1`과 `const2`가 `add`라는 함수로 입력되어 연산된 결과를 확인 할 수 있습니다.


### Data Flow Graph

#### Summary
`const1`과 `const2`가 `add`라는 함수로 입력되어 연산되는 **Data Flow Graph** 를 TensorBoard를 통해 확인해보겠습니다. 코드는 아래와 같습니다.

{% highlight python %}
import tensorflow as tf
import os

tb_dir = os.path.join(os.getcwd(), "tb_tutorial")
cur_dir = os.path.join(tb_dir, "ex")

const1 = tf.constant(3.0, name='const1')
tf.summary.scalar('const1', const1)

const2 = tf.constant(4.0, name='const2')
tf.summary.scalar('const2', const2)

add = tf.add(const1, const2)
tf.summary.histogram('add', add)

merged = tf.summary.merge_all()

sess = tf.Session()
sess.run([add, merged])

writer = tf.summary.FileWriter(cur_dir, graph=sess.graph)
writer.close()
{% endhighlight %}

TensorBoard는 `summary` operation을 이용하여 저장한 데이터들을 merge한 후 그래프를 시각화합니다. 따라서 우리는 summary operation이 로그 데이터를 저장할 디렉터리를 먼저 생성한 후 튜토리얼을 진행하겠습니다. (`line 4~5`) 앞으로 진행될 튜토리얼의 모든 로그 데이터들은 `tb_tutorial` 디렉터리에 각 주제에 맞게 하위 디렉터리를 생성 후 그 곳에 저장하도록 하겠습니다.

> *TensorBoard는 해당 디렉터리에 로그를 누적하여 저장하며 가장 최근에 저장된 로그를 참고하여 그래프를 그립니다.*


`line 8`, `11`, `14`를 보면 알 수 있듯이 `tf.summary`를 이용하여 각 연산자들에 대한 요약 데이터를 저장하고있습니다. 각 summary operation들의 자세한 설명은 [여기](https://www.tensorflow.org/api_guides/python/summary)를 참고하시기 바랍니다. TensorBoard에서도 마찬가지로 각각의 요약 데이터들은 그래프 내에서는 지엽적인 존재입니다. 요약 데이터들을 연결하기 위해서는 `merge_all()` 함수를 이용하여 모든 요약 데이터들을 하나로 합쳐야 합니다.(`line 16`) 그러면 모든 요약 데이터를 가지고 있는 summary 프로토버퍼 오브젝트를 생성할 수 있습니다. 우리는 요약 데이터를 디스크에 저장하기 위해 `FileWriter`에 프로토버퍼 오브젝트를 넘겨줘야합니다. `cru_dir`로 이벤트 파일이 저장될 디렉터리를 지정해주었고, `graph` parameter를 설정하여 TensorBoard를 통해 Data Flow Graph를 확인할 예정입니다.(`line 21`)


위 코드를 실행하면 터미널창에는 아무것도 나타나지 않고, `tb_tutorial/ex/` 디렉터리가 생성 된 것을 확인할 수 있을 것입니다. 이제, TensorBoard에서 그래프를 확인하기 위해서 터미널창에 `tensorboard --logdir='FileWriter에서_지정한_경로'`를 입력해주면 됩니다. 위의 예시코드를 실행한 후 TensorBoard를 확인하는 경우라면 `tensorboard --logdir=tb_tutorial/ex/`라고 명령어를 입력하시면 됩니다.

> *logdir 후의 경로가 이벤트 파일이 있는 디렉터리까지 가지 않아도 (이벤트 파일이 있는 디렉터리의 상위 디렉터리여도, 즉 예시의 경우 tb_tutorial까지여도) TensorBoard에서 알아서 구조를 매핑하여 시각화해줍니다.*

아무런 에러없이 터미널창에
{% highlight pycon %}
Strarting TensorBoard at http://localhost:6006
{% endhighlight %}
과 같은 화면이 출력 됐으면 거의 다 왔습니다. 웹 브라우저를 켜고 주소창에 `http://localhost:6006`을 입력하신 후 조금 기다리시면 아래와 같은 그래프가 나올 것입니다.

<figure>
   <img src="{{ "/media/img/tb_tutorial/add_graph.png" | absolute_url }}" />
   <figcaption>Add Graph</figcaption>
</figure>

각각의 노드들을 클릭하면 해당 노드에 대한 간단한 description이 오른쪽 상단에 나옵니다. 그래프를 살펴보면 `const1`과 `const2`라는 Constant가 Add 연산을 하는 노드의 input으로 들어가 `add`라는 summary histogram으로 출력되는 **Flow** 를 볼 수 있습니다.

> *tensorboard는 logdir 뒤 디렉터리명을 잘못 입력해도 별도의 에러 메시지가 뜨지 않습니다. 현재까지는 path 에러 발생의 유무를 http://localhost:6006 에 접속하여 결과가 표시되는지 확인하는 것 이외에는 별도의 방법이 없습니다 T_T*


#### Simple Operation
또 다른 예시코드를 보겠습니다.
{% highlight python %}
import tensorflow as tf
import os

tb_dir = os.path.join(os.getcwd(), "tb_tutorial")
cur_dir = os.path.join(tb_dir, "power")

a = tf.placeholder(tf.float32)
b = tf.placeholder(tf.float32)
c = tf.placeholder(tf.float32)

multiple = tf.multiply(a, b)
tf.summary.histogram('multiple', multiple)

power = tf.pow(multiple, c)
tf.summary.histogram('power', power)

merged = tf.summary.merge_all()
sess = tf.Session()
print(sess.run([power], feed_dict={a:[1, 2, 3], b:[2, 4, 6], c:2}))
writer = tf.summary.FileWriter(cur_dir, graph=sess.graph)
writer.close()
{% endhighlight %}

위와 마찬가지로 요약 데이터 로그가 저장될 디렉터리를 지정해 준 뒤(`line 4~5`), `a`, `b`, `c`라는 placeholder를 생성하고 곱셈 연산 노드와 지수연산 노드를 생성해 줍니다. 노드들을 생성할 때마다 `summaary`로 연산자의 요약 데이터도 생성해 준 뒤(`line 7~15`), 연산의 마지막 단계에서 `merge_all` 함수를 호출해줍니다(`line 17`). `Session`을 생성 한 뒤 `run` 함수를 이용하여 연산을 실행합니다. 이때, `a`, `b`, `c`에 들어갈 값을 `feed_dict` 로 지정해줍니다(`line 18~19`). `FileWriter`를 이용해 코드 도입부분에서 지정해준 디렉터리에 이벤트 파일을 저장해줍니다(`line20~21`). 코드를 실행하였으면 이제 터미널창으로 돌아가서 `tensorboard --logdir='FileWriter에서_지정한_경로'` 를 입력한 후 웹 브라우저 주소창에 `http://localhost:6006` 입력하여 tensorboard에 나오는 그래프를 확인합니다.

<figure>
   <img src="{{ "/media/img/tb_tutorial/power_graph.png" | absolute_url }}" />
   <figcaption>Power Graph</figcaption>
</figure>


#### Complex Operation - Linear Regression
이번에는 연산 노드가 앞선 예제들보다 조금 더 많은 선형회귀 예제 코드를 TensorBoard로 그려보겠습니다.

{% highlight python %}
import tensorflow as tf
import numpy as np
import os

tb_dir = os.path.join(os.getcwd(), "tb_tutorial")
cur_dir = os.path.join(tb_dir, "fahrenheit_converter")

x_data = [12.0, 28.0, 36.5, 42.0, 29.8]
y_data = [53.6, 82.4, 97.7, 107.6, 85.64]

def norm(data):
    data = np.array(data)
    x_norm = np.zeros([len(data)])
    for i in range(len(data)):
        x_norm[i] = data[i] / 100
    return np.reshape(x_norm, [-1, 1])

x_data = norm(x_data)
y_data = np.reshape(y_data, [-1, 1])


x = tf.placeholder(tf.float32, shape=[None,1])
y = tf.placeholder(tf.float32, shape=[None,1])

W = tf.Variable(tf.random_normal([1,1]), name='weight')
b = tf.Variable(tf.random_normal([1]), name='bias')

hypothesis = tf.add(tf.matmul(x, W), b)

cost = tf.reduce_mean(tf.square(y - hypothesis))

optimizer = tf.train.GradientDescentOptimizer(learning_rate=0.01)
train = optimizer.minimize(cost)

merged = tf.summary.merge_all()

sess = tf.Session()
sess.run(tf.global_variables_initializer())

for step in range(30001):
    _cost, _W, _b, _ = sess.run([cost, W, b, train], feed_dict={x:x_data, y:y_data})
    if step % 1000 == 0:
        print("Step:", step, "\tCost:", _cost, "\tW:", _W[0], "\tb:", _b)

writer = tf.summary.FileWriter(cur_dir, graph=sess.graph)
writer.close()

print("X: 20, Y:", sess.run(hypothesis[0], feed_dict={x:norm([20])}))
print("X: 30, Y:", sess.run(hypothesis[0], feed_dict={x:norm([30])}))
print("X: 40, Y:", sess.run(hypothesis[0], feed_dict={x:norm([40])}))
print("X: 50, Y:", sess.run(hypothesis[0], feed_dict={x:norm([50])}))
print("X: 60, Y:", sess.run(hypothesis[0], feed_dict={x:norm([60])}))
{% endhighlight %}

> *실행결과*

{%highlight pycon %}
...
Step: 27000 	Cost: 0.00965826 	W: [ 179.03100586] 	b: [ 32.29016876]
Step: 28000 	Cost: 0.00663723 	W: [ 179.19673157] 	b: [ 32.24048233]
Step: 29000 	Cost: 0.00455425 	W: [ 179.33456421] 	b: [ 32.1991806]
Step: 30000 	Cost: 0.00312983 	W: [ 179.44841003] 	b: [ 32.16521835]
X: 20, Y: [ 68.05490112]
X: 30, Y: [ 85.9997406]
X: 40, Y: [ 103.94458008]
X: 50, Y: [ 121.88941956]
X: 60, Y: [ 139.83427429]
{% endhighlight %}
