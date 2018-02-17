---
layout: post
title: "CS20 : TensorFlow for Deep Learning Research (1)"
date: 2018-02-16
tags: [Tensorflow, Deep Learning]
excerpt: "Summary of CS20 Lecture 1 ~ 2"
comments: true
---
```python
import tensorflow as tf
import os

# Warning?
# The TensorFlow library wasn't compiled to use SSE4.1 instructions, but these are available on your machine and could speed up CPU computations.
# os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'
```

`os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'` 를 통하여 위의 Warning 을 없앤다.


```python
# TF Constants

a = tf.constant([2])
b = tf.constant([[3,1], [1, 3]])
x = tf.add(a, b)

with tf.Session() as sess:
    print(sess.run(x))
    
# Broadcasting similar to NumPy
```

    [[5 3]
     [3 5]]

tf.Session() 에서 연산을 실행해야한다는 것을 제외하고는 NumPy 와 굉장히 유사하다. Broadcasting computation 도 NumPy 와 유사하다고 볼 수 있다.


```python
# Tensors filled with a zero value.
# tf.zeros(shape, dtype=tf.float32, name=None)

with tf.Session() as sess:
    print(sess.run(tf.zeros([2, 3], tf.int32)))

    print(tf.zeros([2, 3], tf.int32))
```
2 * 3 행렬을 0으로 채운 값으로 초기화해준다. 이 부분도 NumPy 의 무엇이 생각난다.

```python
# Tensors filled with a value 1

with tf.Session() as sess:
    print(sess.run(tf.ones([2, 3])))

print(tf.ones([2, 3]))
```
2 * 3 행렬을 1로 채운 값으로 초기화해준다.


```python
# Tesnors filled with a specific value
# tf.fill(dims, value, name=None) 

with tf.Session() as sess:
    print(sess.run(tf.fill([2, 3], 2)))
    
print(tf.fill([2, 3], 2))
```
정의한 Dimmension 에 사용자가 정의하는 특정한 값을 채워준다는 뜻이다. 위의 코드는 2 * 3 행렬에 모든 요소는 2로 채워진다.

```python
# Constants as sequences

# tf.lin_space -> must be the float value. unless, it throws the exception.
# tf.range -> not iterable. for i in tf.range(n) = TypeError
with tf.Session() as sess:
    print(sess.run(tf.lin_space(10.0, 13.0, 4)))
    print(sess.run(tf.range(3, 18, 3)))
    print(sess.run(tf.range(7)))

Output:
    [ 10.  11.  12.  13.]
    [ 3  6  9 12 15]
    [0 1 2 3 4 5 6]
```
tf.lin_space(start, end, num) 은 start ~ end 사이의 num 개의 요소를 나타낸다. tf.range(start, end, num) 과는 이 부분에서 조금 다르다.

```python
'''
Randomly Generated Constants

tf.random_normal -> 정규분포
tf.truncated_normal -> 절단정규분포
tf.random_uniform -> 균등분포
tf.random_shuffle -> 주어진 값을 랜덤으로 섞어줌.
tf.random_crop -> 주어진 사이즈만큼 랜덤하게 잘라냄.
tf.multinomial -> 다항분포로부터 샘플 뽑아줌.
tf.random_gamma -> 그래프 수준의 난수 시드를 설정합니다.

tf.set_random_seed(seed)
'''

tf.set_random_seed(1)

a = tf.random_uniform([100])

with tf.Session() as sess:
    print(sess.run(a))

Output:
    [ 0.19060242  0.64686596  0.45381951  0.00422084  0.22722006  0.75184584
      0.92537677  0.64524829  0.77563584  0.93218708  0.95229208  0.54201698
      0.33405375  0.27592516  0.16024303  0.64543045  0.85674727  0.40464294
      0.1989435   0.62238312  0.95383787  0.59564149  0.22259629  0.80697596
      0.76472104  0.2977581   0.91643906  0.66416681  0.44418776  0.42506278
      0.7573154   0.54371977  0.72938478  0.99162865  0.79683244  0.16245127
      0.68992257  0.51835239  0.33846939  0.42107236  0.51141357  0.8945291
      0.12372172  0.97809029  0.99924302  0.16543174  0.02567363  0.58090985
      0.63010895  0.88147128  0.23765385  0.90792608  0.99494421  0.34083223
      0.86616004  0.45148587  0.7724328   0.95609665  0.66971362  0.43162572
      0.32410049  0.3572365   0.00168681  0.16751516  0.67864203  0.96478236
      0.63830507  0.41147566  0.32525182  0.70839965  0.02201724  0.11298811
      0.0998342   0.19422805  0.91728401  0.11698937  0.01682174  0.73506665
      0.15921915  0.46299911  0.32735717  0.40654266  0.75373149  0.21365762
      0.04018652  0.3907032   0.87003577  0.05618274  0.60967994  0.09324217
      0.19348526  0.27580929  0.70004416  0.14308286  0.8036592   0.84476995
      0.20667505  0.15991032  0.70941746  0.67531002]
```

그렇다고 합니다.

```python
# Wizard of Div 라고 하는데 ㄹㅇ 마법이었다. (반어법)
# Error 나면 텐서가 뻗어버림. 어떻게 고치지 ?

a = tf.constant([2, 2], name='a')
b = tf.constant([[0, 1], [2, 3]], name='b')

with tf.Session() as sess:
	print(sess.run(tf.div(b, a)))             #⇒ [[0 0] [1 1]]
	print(sess.run(tf.divide(b, a)))          #⇒ [[0. 0.5] [1. 1.5]]
	print(sess.run(tf.truediv(b, a)))         #⇒ [[0. 0.5] [1. 1.5]]
	print(sess.run(tf.floordiv(b, a)))        #⇒ [[0 0] [1 1]]
# 	print(sess.run(tf.realdiv(b, a)))         #⇒ # Error: only works for real values
	print(sess.run(tf.truncatediv(b, a)))     #⇒ [[0 0] [1 1]]
	print(sess.run(tf.floor_div(b, a)))       #⇒ [[0 0] [1 1]]

```
`print(sess.run(tf.realdiv(b, a)))`를 실행하면 이상하게 tf.Session() 이 뻗어버린다. tf.Session().close() 를 안해서 그런가? 

> 아니 근데 강의 자료엔 그런 거 없었잖아... ㅠㅠ

```python
# TensorFlow takes Python natives types: boolean, numeric (int, float), strings

# An n-dimensional array
# 0-d tensor: scalar (number) 
# 1-d tensor: vector
# 2-d tensor: matrix
```

위는 깨알 한 줄 노트.


```python
t_1 = ['hey', 'now']

with tf.Session() as sess:
    print(sess.run(tf.zeros_like(t_1)))

[b'' b'']
```

강의에선 문자열 데이터를 tf.zeros_like() 같은 연산을 하면 빈 문자열이 된다라는 걸 보여주고 싶었던 것 같다. 이와 마찬가지로 False, True 에 대한 tf.zeros_like(), tf.ones_like() 연산도 있는데 이건 굳이 할 필요가 없었을 것 같다.

```python
# create variables with tf.Variable
# case 1.
s = tf.Variable(2, name="scalar") 
m = tf.Variable([[0, 1], [2, 3]], name="matrix") 
W = tf.Variable(tf.zeros([784,10]))

# create variables with tf.get_variable
# case 2.
# s = tf.get_variable("scalar", initializer=tf.constant(2)) 
# m = tf.get_variable("matrix", initializer=tf.constant([[0, 1], [2, 3]]))
# W = tf.get_variable("big_matrix", shape=(784, 10), initializer=tf.zeros_initializer())

# case 2 is better than case 1.
```

2번째 케이스가 더 좋다고는 하는데 왜인지는 잘 모르겠다. 근데 결정적으로 2번째 케이스로 하면 에러나서 1번째 케이스로 코드를 작성했다. 강의 자료도 전체적으로 1번째 케이스를 따른다.


```python
var = tf.Variable(2, name='var')
two_times = var.assign(2 * var)

with tf.Session() as sess:
    print(sess.run(tf.global_variables_initializer()))
    print(sess.run(two_times))
    print(sess.run(two_times))
    print(sess.run(var.assign_add(10)))
    print(sess.run(var.assign_sub(10)))

Output:
    None
    4
    8
    18
    8
```
tf.Variable() 은 assign_add(value), assign_sub(value) 등의 연산자가 있다는 것을 알 수 있다. 

```python
W = tf.Variable(10)

sess1 = tf.Session()
sess2 = tf.Session()

sess1.run(W.initializer)
sess2.run(W.initializer)

print(sess1.run(W.assign_add(10))) 		# >> 20
print(sess2.run(W.assign_sub(2))) 		# >> 8

sess1.close()
sess2.close()

Output:
	    20
	    8
```    

Session 1 과 Session 2, 각각의 연산이 다르게 진행됨을 알 수 있다.


```
Definition of Placeholder
# Define the function f(x, y) = 2 * x + y without knowing value of x or y. 
# x, y are placeholders for the actual values.
```

위의 설명, 그대로이다.


```python
a = tf.placeholder(tf.float32, shape=[3])

b = tf.constant([5, 5, 5], dtype=tf.float32)

c = a + b

with tf.Session() as sess:
    print(sess.run(c, feed_dict={a: [1, 2, 3]}))
    sess.close()

Output:
	    [ 6.  7.  8.]
```

위의 코드는 [5, 5, 5] 에 사용자가 각각의 요소에 더하고자 하는 값을 대입하기 위한 코드인데 그게 바로 tf.placeholder 다. tf.placeholder 는 3개의 Vector 를 받도록 되어있고 feed_dict = {a: [1, 2, 3]} 를 통하여 그 값을 대입받았다.

그래서 결과값은 [ 6, 7, 8 ] 이 나온 것이다.

## Reference

http://web.stanford.edu/class/cs20si/syllabus.html



