---
layout: post
title:  "Linear & Non-Linear as a Activation Function"
date:   2018-01-04
excerpt: "Activation Function 으로서 선형/비선형 함수 중 무엇을 선택해야할까?"
tag:
- Machine Learning
- Neural Network
comments: true
---

### 1. Introduction

휴식 기간 동안 다양한 것을 접해보고 있다. 그 중 하나는 인공 지능 분야이다. 이 글에선 Activation Function (이하, 활성화 함수) 의 선형/비선형일 경우를 정리해보도록 한다.

### 2. Perceptron

기본적으로 퍼셉트론(1957, 프랑크 로젠블라트)에 대하여 이야기해볼 필요가 있다. 

![](https://cdn-images-1.medium.com/max/1600/0*No3vRruq7Dd4sxdn.png)

~~~
\\[ y = {h((x_1 * w_1) + (x_2 * w_2) + (x_3 * w_3))} \\]
~~~

퍼셉트론은 다수의 신호를 입력으로 받고 계산을 한 후, 그 결과 값을 활성화 함수에 대입한다. 위의 그림은 그에 대한 예이며, 3개의 신호를 받는 퍼셉트론의 예이다.

\\( h(x) \\) 가 활성화 함수라고 한다면, 모든 입력 신호와 가중치를 곱한 값을 모두 더하고 활성화 함수에 그 값을 대입하여 최종 결과 값을 구한다.

제안된 퍼셉트론에선 정해진 한계, 즉 임계값을 넘지 못한다면 0, 넘는다면 1을 출력하도록 되어있다.

{% highlight python %}
def step_function(x):
	if x> 0:
		return 1
	else:
		return 0
{% endhighlight %}

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d9/Dirac_distribution_CDF.svg/600px-Dirac_distribution_CDF.svg.png)

시각적으로 위와 같이 보이는데 Step Function 은 0 과 1 중 하나의 값만을 돌려준다. 이러한 문제로 인하여 대안으로서 Sigmoid Function 이 존재한다.

{% highlight python %}
def sigmoid(x):
	return 1 / (1 + np.exp(-x))
{% endhighlight %}

![](https://upload.wikimedia.org/wikipedia/commons/thumb/8/88/Logistic-curve.svg/600px-Logistic-curve.svg.png)

Step Function 과 달리 Sigmoid Function 은 부드러운 곡선이고 출력에 따라 연속적으로 값이 변한다. 출력값 또한 0 과 1로 고정되어 있지 않다.

둘 사이의 중요한 공통점은 비선형 함수라는 점과 0 ~ 1 라는 범위라는 것이다.

### 3. 비선형 / 선형

공부하면서 '만약 활성화 함수가 비선형 함수가 아닌 선형 함수라면?' 이라는 생각을 했다. 

우선 비선형 / 선형 함수의 정의는 다음과 같다.

- 선형 함수 
	-  \\( f(x) = { a*x + b} \\)
- 비선형 함수
	- 직선 1개로 그릴 수 없다.

![](https://www.learnopencv.com/wp-content/uploads/2017/10/xor.png)

위의 사진은 XOR 을 그래프로 나타낸 것이다. AND 와 다르게 XOR 은 간단한 함수임에도 불구하고 우리는 선형적으로 분리할 수 없었다. 하지만 비선형적인 경계선을 그린다면 분리할 수 있다.

그리고, 선형 함수를 \\( h(x) = { n*x } \\) 라고 하였을 때, n 층의 네트워크를 구축한다고 가정한다면 결국 이는 n 층 만큼의 선형 함수의 합성 함수일 뿐이다. 즉, 층을 지나칠 수록 값이 커지기만 할 뿐이다. 네트워크를 구축하는 이점이 없어진다. 

그렇기 때문에, 활성화 함수로 비선형 함수를 사용해야한다.


