---
layout: post
title: "옵저버 패턴(observer pattern)"
date: 2022-03-31
categories: DesignPattern
---

# 옵저버패턴(observer pattern)


## 1. 왜 옵저버패턴을 사용하는가?

### 1.1 문제 상황 가정 

하나의 객체가 가지고 있는 상태값(데이터)를 다른 여러 객체가 의존하는 상황이 존재 할 수 있다. 상태값을 가지고 있는 객체를 A객체라고 하고 A객체에 의존하는 객체를 a,b,c 객체라고 하면 상태값에 대한 이벤트는 A객체에서 발생하므로 A객체로부터 a,b,c객체들에게 이벤트 발생을 알려줘야 할 것이다. 이를 아래와 같이 코딩해 볼 수 있다.

```java
class ProblemNewsStation{

    String title;

    String contents;

    ProblemMailSender problemMailSender = new ProblemMailSender();

    ProblemMessageSender problemMessageSender = new ProblemMessageSender();

    ProblemFaxSender problemFaxSender = new ProblemFaxSender();

    public void setNewsflash(String title, String contents) {
        this.title = title;
        this.contents = contents;
        problemMailSender.update(title, contents);
        problemMessageSender.update(title, contents);
        problemFaxSender.update(title, contents);
    }
}

```
ProblemNewsStation는 title, contents라는 속보 데이터를 가지고 있고 속보 데이터에 업데이트가 발생하면 각 Sender들에게 데이터를 보내줘야 한다. 이러한 코딩은 문제점이 있다. ProblemNewsStation가 Sender들에게 데이터를 보내는 시점이 컴파일 시점에 결정되므로 새로운 Sender가 추가된다면 동적으로 대응이 불가능하고 컴파일부터 다시 시작해야 된다는 것이다.

### 1.2 문제에 대한 원인 

이러한 문제의 원인은 ProblemNewsStation은 Sender들의 특정 구현에 의존하고 있다는데 있다. 아래에 Sender클래스의 구현을 볼 수 있다.

``` java

class ProblemMailSender {
    void update(String title, String contents){
        System.out.println("---메전송---");
        System.out.println("제목: " + title);
        System.out.println("내용: " + contents);
    }

}

class ProblemMessageSender {
    void update(String title, String contents){
        System.out.println("---문자전송---");
        System.out.println("제목: " + title);
        System.out.println("내용: " + contents);
    }
}

class ProblemFaxSender {
    void update(String title, String contents){
        System.out.println("---팩스전송---");
        System.out.println("제목: " + title);
        System.out.println("내용: " + contents);
    }
}

```
세 클래스들은 구현체 클래스들이다. 결국 ProblemNewsStation가 update메소드를 호출하기 위해서는 각 구현 된 클래스의 타입에 의존 할 수 밖에 없고 그 의미는 구현클래스들의 update메소드는 컴파일시점에서 호출이 결정되어 버린다는 것이다. 그렇다면 ProblemNewsStation는 각 구현클래스에 의존하지 않는채 각 Sender들에게 데이터의 업데이트 사실를 알려야한다. 이러한 문제를 해결 할 수 있는 설계 방법이 옵저버 패턴이다.


## 2. 옵저버패턴이란 무엇인가?

### 2.1 옵저버패턴의 정의

옵저버 패턴은 한 객체의 상태가 변경되면 그 객체에 의존하는 객체들에게 변경 상태를 느슨한 결합으로 알려줌으로써 상태값을 가지고 있는 객체와 의존하는 객체의 유연성과 확장성을 보장하는 디자인 패턴이다. 

### 2.2 옵저버패턴의 구조

![observer-pattern-01](/public/images/observer-pattern-01.png)

상태값에 의존하는 객체는 Observer인터페이스를 implements하여 상태값을 가지는 객체에게 추상화 된 인터페이스를 제공한다. 상태값을 가지는 객체는 Subject인터페이스를 implements함으로써 옵저버 관리를 위한 메소드를 구현한다. 


### 2.3 옵저버패턴의 구현

구현클래스의 update메소드의 시그니처는 void update(String title, String contents)로서 통일되어 있으므로 인터페이스로 추상화가 가능하고 아래와 같이 코딩 할 수 있다.

``` java
public interface Observer {

    void update(String title, String contents);

}

```

``` java
public class FaxSender implements Observer {

    @Override
    public void update(String title, String contents) {
        System.out.println("---팩스전송---");
        System.out.println("제목: " + title);
        System.out.println("내용: " + contents);

    }
}
```

``` java
public class MailSender implements Observer {

    @Override
    public void update(String title, String contents) {
        System.out.println("---메일전송---");
        System.out.println("제목: " + title);
        System.out.println("내용: " + contents);

    }
}
```

``` java
public class MessageSender implements Observer {

    @Override
    public void update(String title, String contents) {
        System.out.println("---문자전송---");
        System.out.println("제목: " + title);
        System.out.println("내용: " + contents);

    }
}
```


Observer 인터페이스를 각 Sender들이 implements 한다면 ProblemNewsStation클래스는 추상화 된 인터페이스로 Sender의 update메소드에 접근이 가능하다. 그리고 update메소드의 클래스 타입과 메소드 시그니처가 통일되었으므로 for을 사용 할 수도 있다. 이러한 기능을 나타내기 위해 아래와 같은 Interface를 만들 수 있다.

``` java
public interface Subject {

    void registeObserver(Observer observer);

    void removeObserver(Observer observer);

    void notifyObservers();
}
```

* void registeObserver(Observer observer): Subject 인터페이스를 implements한 클래스는 위에서 말한 for문을 사용하기 위한 List<Observer>를 참조 해야한다. registeObserver메소드는 List<Observer>에 옵저버의 참조를 저장하는 기능을 한다.

* void removeObserver(Observer observer): removeObserver메소드는 반대로 List<Observer>에 옵저버의 참조를 제거하는 기능을 한다. 

* void notifyObservers(): notifyObservers메소드는 상태값의 변경을 for문을 통해서 옵저버들에게 알리는 기능을 한다.


Subject인터페이스를 구현하면 아래와 같이 코딩 할 수 있다.

``` java
public class NewsStation implements Subject {

    List<Observer> observers;

    String title;

    String contents;

    boolean isSending;

    public NewsStation() {
        this.observers = new ArrayList<>();
        this.isSending = false;
    }

    public void breakNews(String title, String contents) {
        this.title = title;
        this.contents = contents;
        this.notifyObservers();

    }

    public String getTitle() {
        return title;
    }

    public String getContents() {
        return contents;
    }

    @Override
    public void registeObserver(Observer observer) {
        observers.add(observer);
    }

    @Override
    public void removeObserver(Observer observer) {
        observers.remove(observer);
    }

    @Override
    public void notifyObservers() {
        if(!isSending) {
            isSending = true;
            for(Observer observer: observers){
                observer.update(title, contents);
            }
            isSending = false;
        }

    }
}
```

여기서 중요한 것은 NewsStation클래스에는 더 이상 Sender구현클래스 대한 타입이 없어지므로써 구현클래스에 대한 의존도가 사라지게 되었다는 것이다. 전체 코드는 [여기서](https://github.com/GyujinAn/blog-sample-code/tree/main/src/designpatterns/observer01) 확인 할 수 있다.

### 2.4 push와 pull

옵저버 패턴은 두가지의 구현방식이 존재한다. 첫번째는 푸쉬방식이다. 푸쉬방식은 Subject가 데이터를 직접 옵저버에게 넘겨주는 방식이다. 위의 코드는 Subject를 implements한 NewsStation클래스에서 observer.update(title, contents)를 통해서 tilte과 contents를 직접 넘겨준다.

두번째 pull방식이다. pull 방식은 Subject는 옵저버에게 업데이트 사실만 알리고 자신의 참조를 넘겨줌으로써 옵저버에서 데이터를 가져갈 수 있도록 한다. pull 방식일때 notifyObservers()를 아래와 같이 코딩할 수 있다.

``` java
    public void notifyObservers() {
        if(!isSending) {
            isSending = true;
            for(Observer observer: observers){
                observer.update(this);
            }
            isSending = false;
        }

    }
```

코드를 보면 observer.update(title, contents)가 observer.update(this)로 변경된 것을 확인 할 수 있다. pull 방식의 전체코드는 [여기서](https://github.com/GyujinAn/blog-sample-code/tree/main/src/designpatterns/observer02) 확인 할 수 있다.

## 3. 옵저버패턴의 주의점

옵저버패턴은 for문이 사용되므로써 루핑 도중에 상태값이 변경되면 동시성이 확보되지 않을 수도 있다는 주의점이 있다. 그러므로 위 코드의 boolean isSending 와 같은 플러그 변수를 많이 사용한다. 


<!-- ## 4. 스프링에서의 옵저버패턴 사용 ApplicationListener -->