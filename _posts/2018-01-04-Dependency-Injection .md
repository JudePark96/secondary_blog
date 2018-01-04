---
layout: post
title:  "Dependency Injection - Constructor Injection"
date:   2018-01-04
excerpt: "Dependency Injection 에 대한 간단한 소개"
tag:
- Design Pattern
- Angular
comments: true
---

### Dependency Injection 

일반적으로 어플리케이션을 개발하면서 객체의 구성 등을 설계하고 개발을 한다. 예를 들어, 쇼핑몰 어플리케이션을 개발한다고 가정을 한다면 가장 먼저 떠오르는 객체는 상품 객체를 이야기할 수 있을 것이다.

이러한 상품 객체는 과연 단일 객체만으로 구성이 가능할까?

{% highlight javascript %}
export class Item {
  public type: ItemType;
  public information: ItemInformation;
	
  constructor() {
    this.type = new ItemType();
	 this.information = new ItemInformation();
  }
}
{% endhighlight %}

아마, 아닐 것이다. 위와 같이 하나의 상품을 구성하는 객체의 안을 들어가보면 또 다른 객체가 존재할 것이다.
위의 코드의 문의점은 아래의 코드를 예시로 들며 설명할 수 있을 것 같다.

{% highlight javascript %}
Item.getType().setTypeName('TypeName');
Item.getType().setTypeInformation('TypeInformation');
{% endhighlight %}

간단히 말하자면 유연하지 않고, 테스트하기 어려우며, 객체 간의 결합도를 높인다고 볼 수 있을 것이다. 저대로라면, 상품 객체 내부의 정보를 수정하기 위하여 다음과 같이 코드를 작성해야할 것이다.

어떻게 해야 더욱 더 유연하고 좋은 코드를 작성할 수 있을까?

마틴 파울러의 [글](https://martinfowler.com/articles/injection.html)에 따르면 아래의 3 가지 방법이 있다.

 * Constructor injection
 * Setter injection
 * Interface injection

위의 코드를 첫 번째 방법인 Constructor Injection 을 통하여 수정해보도록 한다.
	
{% highlight javascript %}
export class Item {
  public type: ItemType;
  public information: ItemInformation;
	
  constructor(public type: ItemType, public information: ItemInformation) {
    this.type = type;
    this.information = information;
  }
}
{% endhighlight %}

기존의 코드는 객체의 생성자 내부에서 다른 객체를 생성하고 있었다. 객체와 객체 간의 결합이 발생하였다고 할 수 있다. Constructor Injection 을 통하여 수정하며 달라진 것은 객체와 객체 간의 결합을 피한 것을 꼽을 수 있다. 그리고 생성자를 통하여 객체를 주입함으로서 ItemType, ItemInformation 객체는 Item 객체 내부가 아닌 외부에서 관리가 가능해졌다.

결과적으로 더욱 더 유연하고, 테스트가 쉬우며, 재사용성이 높은 코드가 되었다. 규모가 작은 프로젝트라면 모르겠지만 큰 프로젝트일 수록 이러한 디자인 패턴이 매우 유용함을 느낄 수 있을 것이다.

### In the end

Dependency Injection 은 굉장히 널리 사용되고 있는 디자인 패턴이다. 이번에 Angular 4 를 공부하면서 이를 위한 프레임워크가 자체적으로 존재한다는 것을 알고, 한 번 정리해보았다. 기회가 된다면 Setter Injection, Interface Injection 을 통한 방법도 정리해볼 것이다.