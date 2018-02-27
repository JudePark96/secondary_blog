---
layout: post
title: JDK 10 - Type Inference
date: 2018-02-27
excerpt: "JDK 의 타입 추론에 대한 생각"
tag: [Java]
comments: true
---

### 1. 소개

18년 3월을 기점으로 JDK 10 이 나온다는 소식이 전해지고 있다. 그런 가운데, 가장 큰 변화는 **var** 라는 키워드의 추가이다. var, val, let 과 같은 키워드는 Scala, Swift, JavaScript 와 같은 언어에서 많은 개발자들이 사용하며 익숙해져있는 키워드들이다. 이것이 이번에 새로 추가된 것인데 어떤 의의를 가지고 있을까?

### 2. 기존의 자바 타입 추론에 대한 간략한 소개

자바를 사용할 때, 많은 개발자들이 다음과 같은 코드를 한 번 쯔음은 작성해본 경험이 있을 것이다.

```java
List<String> list = new ArrayList<String>()
```

위의 코드를 보고 이러한 생각을 하였다.

*왜 String 이 2번이나 타이핑되어야하지?*

우리가 처음에 프로그래밍을 배울 때 이렇게 배운다.

```java
int a = 1;
```

위의 코드는 a 라는 변수에 1 이라는 정수형 데이터 타입의 값이 할당됨을 의미한다. 그러므로 나는 위의 코드가 다음과 같이 작성되어져야한다고 생각했다.

```java
List list = new ArrayList<String>();
```

위와 같이 작성해도 동작은 하지만 `unchecked called to 'add(E) as a member of raw type 'java.util.list'` 라는  것이 warning 으로 존재한다. 잠재적 warning 이 존재하는 코드를 작성할 수는 없기에 위와 같은 코드는 지양하기로 했다.

그러던 중, JDK 7 에서 다음과 같이 코드를 작성할 수 있도록 변경되었다.

```java
List<String> list = new ArrayList<>();
```

<> 안의 데이터 타입의 작성을 생략할 수 있게 되었다. 처음의 데이터 타입의 선언을 통하여 할당 값에 대한 데이터를 추론한다는 것이 컨셉인 듯 했다. 

많은 개발자들이 시간이 지남에 따라 Python 과 JavaScript 와 같은 타입 추론에 익숙해져있다. 그렇기 때문에, 이 또한 올바른 방향은 아니라고 생각했다.

### 3. JDK 10 에서의 var 키워드의 추가

이제 JDK 10 에 감에 따라 다음과 같은 코드를 작성할 수 있게 되었다.

```java
var list = new ArrayList<String>();
```

이로써 다른 언어의 타입 추론 스타일을 수용하는 듯 하지만 시기가 너무 늦었다는 것이 개인적인 생각이다. C++ 의 auto 키워드는 C++11 에 추가되었는데 자바는 16년에서야 JEP 286 이라는 Proposal 이 커뮤니티에 올라왔고 18년에 최종적을 추가되었다.

### 4. 결론

이전에도 그랬지만 앞으로도 자바는 트렌드를 이끄는 언어가 아닐 것 같다. 트렌드를 따라가는 언어일 것이라고 생각한다.

### 5. Reference

https://blog.takipi.com/how-java-10-will-change-the-way-you-code/