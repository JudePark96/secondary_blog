---
layout: post
title:  "CS20 : TensorFlow for Deep Learning Research (2)"
date:   2018-02-19
excerpt: "Implementation of Linear Regression"
tag: [Deep Learning, Tensorflow]
comments: true
---

CS20 을 공부하면서 남기는 글이다. 이번 글에서는 **dependent variable Y** 와 **explanatory variables X** 의 선형 관계 모델인 Linear Regression 을 간단하게 구현한다. **X: birth rate** 이고 **Y: life expectancy** 이다. 즉, 태어난 연도와 기대 수명 간의 관계라고 보면 될 것 같으며 190 여개 국가의 데이터셋을 바탕으로 한다.

```python
import tensorflow as tf
import os
import utils

import matplotlib.pyplot as plt

%matplotlib inline

# Warning?
# The TensorFlow library wasn't compiled to use SSE4.1 instructions, but these are available on your machine and could speed up CPU computations.
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'
```
우선 Tensorflow 와 같은 필요한 모듈을 불러온다. 

> %matplotlib inline 은 서버에 띄워져있는 Jupyter Notebook 의 Invalid Display Option 에러를 피하기 위해서 해놓았다.

```python
dataset, samples = utils.read_birth_life_data('birth_life_2010.txt')
```
Dataset 을 불러온다. samples  는 len(dataset) 과 똑같다.

```python
X = tf.placeholder(tf.float32, name='X')
Y = tf.placeholder(tf.float32, name='Y')

w = tf.Variable(0.0, name='weights')
b = tf.Variable(0.0, name='bias')
e = 0.001
```
X, Y 를 정의하고, 우리가 구하려고 하는 Weight, Bias 또한 정의해놓는다. e 는 Learning Rate 이며 조절해가며 바꿀 수 있다.

```python
Y_predicted = w * X + b 
```
`Weight 와 Bias 를 통해 계산한 값이 이러한 값이 나오더라` 라고 해주는 Y 의 추측값을 나타내는 부분이다. 이 값을 바탕으로 Loss Function 을 작성한다.

```python
loss = tf.square(Y - Y_predicted, name='loss')
optimizer = tf.train.GradientDescentOptimizer(learning_rate=e).minimize(loss)
```
$$
loss = (y_i-y_p)^2, y_i = real, y_p = predicted
$$

위의 수식을 바탕으로 나온 값을 `tf.train.GradientDescentOptimizer(learning_rate=e).minimize(loss)`로 작성하면 Gradient Descent 를 통한 최적화가 진행된다. 

Weight 와 Bias 를 구하려는 것이 핵심이지만 에러를 줄여 최적의 Weight, Bias 를 구하는 것이 관건임을 명심하자.

```python
with tf.Session() as sess:
    sess.run(tf.global_variables_initializer()) # Tf.Variable 초기화
    
    for i in range(100): # 100 epochs
        total_loss = 0 # 처음의 에러는 0
        for x, y in dataset:
            # 데이터 셋의 X, Y 값을 대입하여 Loss 값을 갱신해나감.
            _, l = sess.run([optimizer, loss], feed_dict={X:x, Y:y})
            total_loss += l 
        print('Epoch {0}: {1}'.format(i, total_loss / samples))
        
    # 최종적으로 연산 결과를 바탕으로 한 최적의 Weight 와 Bias 를 구함.
    w_out, b_out = sess.run([w, b])
```
코드로 설명하는게 역시 쉬워서 주석에 써두었다. 결과는 아래와 같이 나온다.

    Epocj 0: 1661.863764550287
    Epocj 1: 956.3224439573916
    Epocj 2: 844.6737683409139
    Epocj 3: 750.7312372197838
    Epocj 4: 667.659830722252
    Epocj 5: 594.1417484349327
    Epocj 6: 529.0787271179651
    Epocj 7: 471.5003584364135
    Epocj 8: 420.5458252520938
    Epocj 9: 375.45531067297253
    
    ...
    
    Epocj 91: 30.03219517776896
    Epocj 92: 30.032402951199575
    Epocj 93: 30.03264380555698
    Epocj 94: 30.033044778692265
    Epocj 95: 30.03343712379727
    Epocj 96: 30.033913317535955
    Epocj 97: 30.03442924663878
    Epocj 98: 30.0349335548615
    Epocj 99: 30.03552558278714
결과를 잘 보면 훈련을 거듭할 수록 에러가 줄어듬을 알 수 있다. 한 번 PyPlot 을 통해 시각적으로 표현해보자.

```python
# plot the results
plt.plot(dataset[:,0], dataset[:,1], 'bo', label='real')
plt.plot(dataset[:,0], dataset[:,0] * w_out + b_out, 'ro', label='predict')
plt.figure()
plt.show()
```
![](https://github.com/ParkJude/parkjude.github.io/blob/master/assets/img/linear_regression_output.png?raw=true)

파란색 점들이 실제 데이터, 빨간색 점들이 예측 값들이다.

빠르고 간단하게 Linear Regression 을 Tensorflow 로 구현하는 법을 알아보았다. CS20 에 [Huber Loss](https://en.wikipedia.org/wiki/Huber_loss) 라는 Loss Function 을 소개하는 슬라이드가 있는데 Loss Function 을 바꾸는 것 뿐일테니 관련 코드를 작성하진 않았다.

더 빠르게, 열심히 공부해서 Word Embedding 하는 부분을 빨리 보고 싶다.

## Reference

http://web.stanford.edu/class/cs20si/syllabus.html