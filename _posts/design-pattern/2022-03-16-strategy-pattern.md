---
layout: post
title: "전략패턴(strategy pattern)"
date: 2022-03-16
categories: DesignPattern
---

# 전략패턴(strategy pattern)

전략패턴은 가장 쉽게 접할 수 있는 디자인 패턴 중 하나이다. 전략패턴이 무엇이고 개발자들이 어떤 이점을 얻을 수 있는지 대해서 포스팅한다.

## 1. 전략패턴이란 무엇인가?

### 1.1 전략패턴의 정의

전략패턴(strategy pattern)은 선언 된 알고리즘을 인터페이스로 캡슐화하고 클래스로 구현하여 클라이언트 측에 느슨한 결합으로 제공하는 디자인 패턴이다. 

### 1.2 전략패턴의 구조

![strategy-pattern-01](/public/images/strategy-pattern-01.png)

위의 그림은 전략패턴의 구조를 uml로 나타낸 것이다. 전략패턴은 선언 된 알고리즘을 나타내는 인터페이스가 존재한다. 위의 그림의 Strategy 인터페이스가 이에 해당한다. 그리고 선언 된 알고리즘은 구현클래스로 나타나게 된다. 위의 그림에서는 ConcreateStrategy1, ConcreateStrategy2, ConcreateStrategy3이 이에 해당한다. 런타임 상황에서 클라이언트측 코드(Context클래스)에서는 Strategy 인터페이스를 통해서 각 구현체들을 전략적으로 교체하고 호출 할 수 있다.

### 1.2 전략패턴의 구현

자바로 전략패턴을 구현해본다. 상황은 필자가 좋아하는 농구선수 케빈 듀란트를 가정해서 한다. 
<br>
먼저 케빈 듀란트는 세가지 공격전략을 가지고 있다. 삼점숫, 레이업, 미들슛이다. 이에 대해 매개체인 Attack이라는 인터페이스에 doAttack()이라는 메소드를 선언하고 클래스를 implements 함으로써 세가지 공격전략에 대한 구현체를 각각 만든다.

```java
public interface Attack {

    void doAttack();
}

public class ThreePointShot implements Attack{
    @Override
    public void doAttack() {
        System.out.println("**try a three-point shot**");
    }
}

public class Layup implements Attack{
    @Override
    public void doAttack() {
        System.out.println("**try a layup shot**");
    }
}

public class MiddleShot implements Attack{
    @Override
    public void doAttack() {
        System.out.println("**try a middle shot**");
    }
}

```

다음으로 케빈 듀란트를 나타낼 BasketballPlayer클래스를 구현한다. 해당 클래스는 Attack을 구성으로 가지고 있어야한다. 그리고 동적으로 공격전략을 교체 할 수 있도록 setAttack()를 정의해야한다.

```java
public class BasketballPlayer {
    public Attack attack;

    public void doAttack(){
        attack.doAttack();
    }

    public void setAttack(Attack attack) {
        this.attack = attack;
    }
}
```

마지막으로 main메소드에서 상황을 가정하여 실행해본다.

```java

public class Main {

    public static void main(String[] args) {
        BasketballPlayer kevinDurant = new BasketballPlayer();
        System.out.println("농구 경기 시작");
        System.out.println("듀란트 공격 턴");
        System.out.println("수비가 골대에 모인 상황");
        kevinDurant.setAttack(new ThreePointShot());
        kevinDurant.doAttack();
        System.out.println("듀란트 공격 완료");

        System.out.println("듀란트 공격 턴");
        System.out.println("수비가 듀란트에게 가까이 붙은 상황");
        kevinDurant.setAttack(new Layup());
        kevinDurant.doAttack();
        System.out.println("듀란트 공격 완료");


    }
}

```
위의 실행 과정을 보면 케빈듀란트는 상황에 따라 동적으로 적절한 공격전략을 선택하여 실행하는 것을 알 수 있다.
위의 모든 코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/src/designpatterns/strategy)에 저장되어있다.

## 2. 전략패턴의 이점

### 2.1 서버측 이점

전략패턴에서는 서버측에서는 용이한 확장성이 보장된다. 선언 된 알고리즘에 대해서 추가적인 요구사항이 발생한 경우 기존의 구현클래스들은 수정하지 않은체 새로운 구현클래스를 만들어 대응 할 수 있다. 즉 ConcreateStrategy1, ConcreateStrategy2, ConcreateStrategy3은 그대로 둔 채 ConcreateStrategy4를 구현하여 대응 할 수 있다. 그리고 서버측에서는 구현클래스를 클라이언트 측에 제공할 방법만 제시하면 끝이다.(구현클래스를 제공하는 방법에는 팩토리 메소드 패턴 등이 있을 수 있지만 전략패턴과 관련 없는 내용이므로 생략한다.)

### 2.2 클라이언트측 이점

클라이언트 측에서의 이점은 Context와 Stategy가 느슨하게 결합되어 클라이언트 측 개발자는 구현메소드(ConcreateStrategy클래스의 strategyMethod()를 의미한다.)의 내용을 알 필요 없이 각 ConcreateStrategy클래스가 어떤 기능을 하는지만 숙지한채 상황에 따라 구현클래스를 교체하여 사용 할 수 있다.


<!-- ## 3. Spring에서의 전략패턴 -->


