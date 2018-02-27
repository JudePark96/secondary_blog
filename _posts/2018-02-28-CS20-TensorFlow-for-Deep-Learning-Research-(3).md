---
layout: post
title: "CS20 : TensorFlow for Deep Learning Research (3)"
date: 2018-02-28
excerpt: "Summary of Convolutional Neural Network"
tag: [Deep Learning]
comments: true
---

### 1. 소개 

이 글을 통하여 최근에 공부한 Convolutional Neural Network 에 대하여 간단히 작성해놓고자 한다.

### 2. 기존의 신경망에 대하여

![](http://neuralnetworksanddeeplearning.com/images/tikz12.png)

기존의 신경망은 이렇게 모든 뉴런들이 연결되어있는 **Fully Connected Neural Network** 라고 할 수 있다. 이러한 신경망이 이미지를 학습할 때 가지는 문제는 너무 많은 연산량과 이미지 자체의 형상을 유지하면서 학습을 진행할 수 없다는 것이다.

![](https://github.com/ParkJude/parkjude.github.io/blob/master/assets/img/06_Introduction%20to%20Computer%20Vision%20and%20convolutional%20network.png?raw=true)

기존의 신경망을 통하여 이미지를 학습시키기 위해서는 32 * 32 * 3 (가로 * 세로 * RGB) 를 3072 * 1 의 형태로 변환시켜야한다. 이 과정에서 이미지 자체의 정보를 잃을 수 밖에 없으며 연산해야할 뉴런들은 기하급수적으로 증가할 수 밖에 없게 된다.

### 3. Convolution

간단하게 설명하자면 Convolution 이란 입력 값이 Kernel (or filter, or feature map) 에 의하여 연산된 것을 의미한다.

![img](https://lh4.googleusercontent.com/JUekVjgB1RP5iNUj3TIGUN0oqaV8o00cqFS9SskzS_KvLtX5PSfRTrN5bgCJamcrNtLiPakXiCm91PIRp988u6_e-cJXdMHVbJRKvKCVd_bmmFhIKVzYjLkGN3PidNBYri74aQPW)

위의 예시를 보면 3 * 3 의 Kernel 이 Stride = 1 의 규칙을 가지고 옮겨가며 연산을 하고 있다. 

> Stride 는 몇 칸 씩 움직일 것인가? 를 의미한다.

모든 spatial location 에 convolve 함으로서 Convoled Feature 를 가지게 된다. 이게 Key Idea 다.

> 사실 이건 오래된 개념은 아니다. 가우시안 필터를 떠올려보자.

![](https://github.com/ParkJude/parkjude.github.io/blob/master/assets/img/convolution%20layer.png?raw=true)

이 Convoled Feature 는 Activation Map 이라고도 하는데 위의 슬라이드를 보면 이해가 갈 것 같다. 이미지에 5 * 5 * 3 크기의 Kernel 이 모든 Spartial Location 을 연산한 후 Activation Map 을 만드는 모습이다. 

### 4. Convolutional Neural Network

![](https://github.com/ParkJude/parkjude.github.io/blob/master/assets/img/07%20_%20Covnets%20in%20TensorFlow.png?raw=true)

Model 을 차례대로 설명하자면 다음과 같다.

1. 28 * 28 의 Input 을 받는다.
2. Convolution 을 통하여 추출한 Feature 에 Activation Function 인 RELU 를 적용한다.
3. 2번에서 나온 값 Pooling Layer 에 넣는다.
4. 반복 ...
5. Pooling Layer 와 Fully Connected Layer 를 구성 후, Softmax 로 분류한다.

위의 과정을 통하여, Convolutional Neural Network 는 Convolution Layer와 Pooling Layer 를 반복적으로 쌓은 Layer 를 통한 Feature Extraction, 그리고 Fully Connected Layer 를 구성하고 마지막 출력 층에 Softmax 를 적용하여 분류하는 것으로 구성되어있다.

> Pooling Layer 는 파라미터의 개수나 연산량을 줄이기 위해 representation 의 spatial 한 사이즈를 줄이는 것이다.

### 5. 결론

CNN 을 통하여 Object Detection, Object Segmentation, Pose Estimation, Image Captioning, Visual Question Answering 등의 영역에 대한 학습이 가능했다. 

이 글에서는 Padding, Convolve Feature 의 Output 공식 등에 대해서 서술한다기보다는 간략하게 요약하고 싶었다.

> 이번에 느낀 건, GPU 를 사야겠다는거...? 연산량이 줄었다곤 하지만, CPU 만으로는 연산이 너무 느리다.

### 6. Reference

http://neuralnetworksanddeeplearning.com/chap1.html

http://deeplearning.stanford.edu/

http://web.stanford.edu/class/cs20si/