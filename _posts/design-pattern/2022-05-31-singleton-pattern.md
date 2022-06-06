---
layout: post
title: "싱글톤 패턴(singleton pattern)"
date: 2022-05-31
categories: DesignPattern
---

# 싱글톤 패턴(Singleton pattern)

## 1. 왜 싱글톤 패턴을 사용하는가?

### 1.1 배경

개발자들이 생성하는 객체 중에는 하나만 있어도 되는것들이 존재한다. 오히려 객체가 두개이상이 되면 쓸데없는 메모리를 사용하게 되어 어플리케이션이 사용하고 있는 리소스를 낭비하는 결과를 초래한다. 예를 들어 쓰레드풀, 사용자설정, 레지스트리설정, 로그기록에 대한 객체들은 두개 이상이 되어야 할 이유가 없으며 두개가 생성되었으면 리소스 낭비뿐만 아니라 다른 여러 문제를 야기 할 수 있다.

### 1.2 싱글턴 패턴의 목적

객체를 두개 이상 생성함으로써 발생 할 수 있는 어플리케이션 리소스 낭비와 같은 문제를 방지한다. 

## 2. 싱글턴 패턴의 정의

클래스의 객체가 하나만 생성되도록 통제하고 생성 된 객체를 어디서든 접근 할 수 있도록 하는 디자인 패턴

## 3. 싱글턴 패턴의 구조

### 3.1 싱글턴 패턴의 UML

![singleton-pattern-01](/public/images/singleton-pattern-01.jpeg)

싱글톤패턴은 private 생성자, static 변수, static 메소드를 통해서 구현된다.

*  private 생성자

싱글톤 패턴을 구현한 클래스의 생성자는 private이다. 생성자를 private로 구현함으로써 클래스 외부에서 해당 객체를 생성하지 못하도록 통제 할 수 있다. 외부에서 생성되지 못하는 객체는 내부에서 한번만 생성하도록 통제된다.

* static 변수와 static 메소드

싱글톤은 private 생성자로 구성되어 있으므로 외부에서 객체를 생성하지 못 한다. 그러므로 static을 사용하여 내부에서 생성된 싱글톤 객체를 접근 할 수 있도록 해준다.

### 3.2 싱글턴의 구현

DB의 접근 정보인 DataSource객체를 싱글턴으로 구현해본다. 아래의 소스코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/java/src/designpatterns/singleton01)에 저장되어있다.

```java
public class SingletonDataSource {

    private static SingletonDataSource uniqueInstance;

    static {
        try {
            uniqueInstance = new SingletonDataSource();
        } catch (FileNotFoundException e) {
            System.out.println("check your datasource.txt");
            e.printStackTrace();
        }
    }

    private BufferedReader reader;

    private Map<String, String> map;

    private SingletonDataSource() throws FileNotFoundException {
        reader = new BufferedReader(new FileReader("java/src/designpatterns/singleton01/datasource.txt"));
        map = new HashMap<>();
        init();
    }

    private void init(){
        String str;
        while (true) {
            try {
                if (!((str = reader.readLine()) != null)) break;
                System.out.println(str);
                String[] split = str.split("=");
                map.put(split[0],split[1]);

            } catch (IOException e) {
                e.printStackTrace();
            }
        }
        System.out.println(map);

    }


    public static SingletonDataSource getInstance(){

        return uniqueInstance;
    }

    public String getUrl(){
        return map.get("url");
    }

    public String getUsername(){
        return map.get("username");
    }

    public String getPassword(){
        return map.get("password");
    }

}
```

해당 코드를 보면 SingletonDataSource는 클래스파일 로딩 시점에 SingletonDataSource객체가 생성되어 uniqueInstance라는 static 변수에 대입된다는 것을 알 수 있다. 그리고 SingletonDataSource객체의 생성자는 private로 되어 외부에서 객체를 생성 할 수 없고 static 메소드 getInstance()를 통해서만 제공된다는 것을 알 수 있다.

## 4. 싱글턴의 lazy instantiation

### 4.1 싱글턴의 lazy instantiation의 구현

위 코드는 클래스파일 로딩 시점에 객체를 생성하여 어플리케이션이 시작하고 끝날 때까지 메모리에 상주하게 된다. 그러므로 해당 객체가 사용되어지는 시점까지는 메모리를 낭비하고 있는 것이다. 이러한 한계를 해결하기 위해서 lazy instantiation(지연 인스턴스화)로 구현 할 수 있다.

```java
package designpatterns.singleton01;

public class SingletonLazyInstantiationSample01 {
    private static SingletonLazyInstantiationSample01 obj;

    private SingletonLazyInstantiationSample01() {
    }

    public static SingletonLazyInstantiationSample01 getInstance(){
        if(obj == null){
            obj = new SingletonLazyInstantiationSample01();
        }
        return obj;
    }

}
```

위 코드는 클래스 로딩 시점에 객체를 생성하지 않고 getInstance()가 최초 호출되는 시점에 객체를 생성한다. 위와 같은 구현을 통해서 메모리를 더욱 효율적으로 사용 할 수 있다.

### 4.2 싱글턴의 lazy instantiation의 주의점

lazy instantiation는 멀티스레드 환경에 문제가 될 수 있다. getInstance()에서 객체가 생성되기 전에 여러 쓰레드가 if문을 통과해버리면 여러개의 객체가 생성 될 수 있다. 이러한 문제를 해결하기 위해서는 getInstance()메소드를 synchronized 키워드를 붙여서 여러 쓰레드가 해당 메소드에 동시에 접근하지 못 하도록 해야된다.

```java
public class SingletonLazyInstantiationSample01 {
    private static SingletonLazyInstantiationSample01 obj;

    private SingletonLazyInstantiationSample01() {
    }

    public static synchronized SingletonLazyInstantiationSample01 getInstance(){
        if(obj == null){
            obj = new SingletonLazyInstantiationSample01();
        }
        return obj;
    }

}
```

위 코드의 getInstance()에 synchronized를 붙여 객체를 생성할때 동기화를 했지만 해당객체를 접근 할때도 동기화로 인하여 성능이 저하된다는 것을 예상 할 수 있다. 그러므로 객체를 생성할때는 동기화를 하지만 객체를 접근 할때는 동기화가 되지 않도록 구현하는 것이 성능면에서 효울적이라고 할 수 있다. 이러한 문제는 DCL(Double checking locking)을 통해서 해결 할 수 있다.

```java
public class SingletonLazyInstantiationSample02 {

    private volatile static SingletonLazyInstantiationSample02 obj;

    private SingletonLazyInstantiationSample02() {
    }

    public static SingletonLazyInstantiationSample02 getInstance(){
        if(obj == null){
            synchronized (SingletonLazyInstantiationSample02.class){
                if (obj == null){
                    obj = new SingletonLazyInstantiationSample02();
                }
            }
        }
        return obj;
    }
}
```

위 코드는 객체를 생성 할때만 동기화를 하므로 접근시에서 동기화가 이루어지지 않느다. 추가적으로 멀티코어의 cpu 캐시에 여러객체가 캐싱될 가능성이 존재하므로 해당 싱글턴 객체는 무조건 메모리에서 접근하도록 volatile 키워드를 붙였다.

## 5. 싱글턴 정리

프로그래밍에서 싱글턴을 사용해야 되는 경우는 많이 있다. 그리고 싱글턴의 구현은 상황에 따라 많은 구현방법이 있으므로 상황에 적잘한 싱글톤을 구현하여 사용 할 수 있도록 한다. 