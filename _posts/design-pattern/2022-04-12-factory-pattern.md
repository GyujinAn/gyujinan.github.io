---
layout: post
title: "팩토리 패턴(factory pattern)"
date: 2022-04-12
categories: DesignPattern
---

# 팩토리 패턴(factory pattern)

이번 포스팅에서는 팩토리 메소드 패턴과 추상 팩토리 패턴에 대해서 포스팅한다.

## 1. 왜 팩토리 패턴을 사용하는가? 

### 1.1 구상클래스에 대한 의존의 문제점과 원인

앞 포스팅에서 전략 패턴, 옵저버 패턴, 데코레이터 패턴에 대해서 알아보았다. 해당 패턴으로 개발된 모듈을 사용하기 위해서는 클라이언트는 new를 통해서 인스턴스를 생성해야한다. new를 통해서 인스턴스를 생성 할 수 있는 클래스를 구상클래스라고 하는데 구상클래스에 의존하면 여러가지 문제점이 생길 수 있다.

* 첫번째, 인터페이스 보다 구상클래스의 수가 많으므로 클라이언트가 구상클래스에 의존하다는 것은 그 만큼 많은 수의 클래스를 숙지하고 있어야한다. 객체지향의 목적은 객체의 내부 동작을 외부에 감추고 인터페이스를 통해서만 클라이언트들이 사용할 수 있도록 하는 것인데 구상클래스에 대한 숙지가 필요하다는 것은 내부동작에 대한 이해를 요구한다.

* 두번째, 서버측에 대한 변화에서 클라이언트가 유연하게 대체하기가 힘들다. 내부동작에 대해서 숙지를 한다하더라도 서버측의 변화에 대해서 클라이언트가 다시 학습하여 적용해야한다는 문제가 있다. 

이러한 문제들 때문에 서버 측에서는 클라이언트 측에 인스턴스를 생성하는 서비스를 제공해야한다. 그리고 클라이언트는 구상클래스를 통해서 직접 인스턴스를 생성하지 않고 서버측에서 제공하는 인스턴스 생성 서비스를 이용하여 인스턴스를 생성해야한다. 이러한 인스턴스를 제공하는 디자인 패턴에는 팩토리메소드 패턴, 추상팩토리 패턴, 빌드패턴이 존재한다.

## 2. 팩토리 패턴이란 무엇인가?

클라이언트에서 구상클래스의 인스턴스를 생성하면서 생기는 구상클래스 의존성의 문제를 해결하기 위해서 팩토리 패턴을 사용한다. 팩토리 패턴은 팩토리 메소도 패턴과 추상 팩토리 패턴 두가지가 존재한다.

## 3. 팩토리 메소드 패턴

### 3.1 왜 팩토리 메소드 패턴을 사용하는가?

추상메소드에서 구상클래스 별로 특정 인스턴스가 필요하다면 어떻게 생성해야될까? 추상메소드에서 구상클래스 별로 특정 인스턴스를 생성한다면 추상메소드가 OCP와 DIP를 위반하게 된다. 이러한 상황에서 팩토리 메소드 패턴을 이용하여 OCP와 DIP를 지키도록 할 수 있다.

### 3.2 병렬 클래스 계층 구조

보통 팩토리 메소드 패턴은 병렬 클래스 계층 구조에서 많이 사용된다. 병렬 클래스 계층 구조란 상위클래스를 확장한 서브클래스 별로 특정 인터페이스 구현체가 존재하는 구조이다. 아래의 팩토리 메소드 패턴의 UML를 살펴보면 Creator를 상속한 ConcreteCreator 별로 ConcreateProduct가 존재 할 수 있다. 이렇게 ConcreateCreator가 확장되면 될수록 ConcreteProduct도 병렬적으로 확장되는 구조를 병렬 클래스 계층 구조라고 한다.

### 3.3 팩토리 메소드 패턴 정의

상위클래스에서 사용하는 특정 인스턴스를 하위클래스에서 생성하도록 함으로써 상위클래스의 OCP와 DIP를 지키도록 하는 디자인 패턴이다.

### 3.4 팩토리 메소드 패턴의 구조

![factory-method-pattern-01](/public/images/factory-method-pattern-01.png)

Creator는 ConcreateCreator의 상위클래스이다. Creator에서 OPC와 DIP를 지키기 위해서 ConcreteProduct는 의존하지 않은 채 Product에만 의존하여 ConcreteProduct를 사용해야한다. 그러므로 Creator는 팩토리 메소드를 추상메소드로 선언하고 실제 인스턴스 생성은 서브클래스에서 추상메소드를 구현하여 한다. ConcreteCreator와 ConcreteProduct는 병렬 구조를 이루고 있기때문에 ConcreteCreator는 자신이 어떤 ConcreteProduct 인스턴스를 생성해야 하는지 알아야한다.

### 3.5 팩토리 메소드 패턴의 구현

팩토리 메소드 패턴을 직접 구현해본다. Creator는 어떤 데이터를 파일로 출력하는 로직을 가지고 있는 모듈이라고 가정한다. Product는 파일의 메타데이터를 저장하는 모듈이라고 가정한다. 아래의 소스코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/java/src/designpatterns/fectorymethod01)에 저장되어있다.

<br>

일단 Creator와 그에 해당하는 구현클래스를 구현해본다. Creator는 FilePrinter라는 추상클래스로 정의하고 구현클래스는 파일의 특성에 따라 ImgFilePrinter, MusicFilePrinter, VideoFilePrinter와 같이 구현한다.

```java
public abstract class FilePrinter {


    void printToFile(){
        ContentFile contentFile = createContentFile("~","my","data","filename");
        contentFile.getPath();
        System.out.println(contentFile.getFileName() +"를 "+contentFile.getPath() +"에 파일로 출력한다.");
    }

    abstract ContentFile createContentFile(String basePath, String owner, String data, String fileName);


}

class ImgFilePrinter extends FilePrinter{
    @Override
    ContentFile createContentFile(String basePath, String owner, String data, String fileName) {
        return new ImgFile(basePath, owner, data, fileName);
    }
}

class MusicFilePrinter extends FilePrinter{
    @Override
    ContentFile createContentFile(String basePath, String owner, String data, String fileName) {
        return new MusicFile(basePath, owner, data, fileName);
    }
}

class VideoFilePrinter extends FilePrinter{
    @Override
    ContentFile createContentFile(String basePath, String owner, String data, String fileName) {
        return new VideoFile(basePath, owner, data, fileName);
    }
}

```

FilePrinter는 printToFile메소드에서 Product인 ContentFile에 의존하고 그에 따른 구현체는 서브클래스의 createContentFile메소드에서 생성한다.

<br>

Product와 그에 해당하는 구현클래스를 구현해본다. Product는 ContentFile라는 추상클래스로 정의하고 구현클래스는 파일의 특성에 따라 ImgFile, MusicFile, VideoFile와 같이 구현한다.

```java
public abstract class ContentFile {
    protected final String basePath;

    protected final String userId;

    protected final String fileName;

    protected final String data;

    public ContentFile(String basePath, String userId, String fileName, String data) {
        this.basePath = basePath;
        this.userId = userId;
        this.fileName = fileName;
        this.data = data;
    }

    public String getFileName() {
        return fileName;
    }

    abstract String getPath();


    protected String getBasePath(){
        return basePath + "/" + userId + "/";
    }
}

class ImgFile extends ContentFile{

    private final String CONTENT_TYPE = "img";

    public ImgFile(String basePath, String userId, String fileName, String data) {
        super(basePath, userId, fileName, data);
    }

    @Override
    String getPath() {
        return getBasePath() + CONTENT_TYPE + "/" + fileName;
    }
}

class MusicFile extends ContentFile{
    private final String CONTENT_TYPE = "music";

    public MusicFile(String basePath, String userId, String fileName, String data) {
        super(basePath, userId, fileName, data);
    }

    @Override
    String getPath() {
        return getBasePath() + CONTENT_TYPE + "/" + fileName;
    }
}

class VideoFile extends ContentFile{
    private final String CONTENT_TYPE = "video";

    public VideoFile(String basePath, String userId, String fileName, String data) {
        super(basePath, userId, fileName, data);
    }
    @Override
    String getPath() {
        return getBasePath() + CONTENT_TYPE + "/" + fileName;
    }


}
```

Product의 구현클래스에서는 Creator클래스에서 필요로 하는 getPath()메소드를 구현하여 제공한다.

## 4. 추상팩토리 패턴

### 4.1 왜 추상팩토리 패턴을 사용하는가?

팩토리 메소드 패턴은 하나의 메소드가 하나의 객체 생성을 담당하기 때문에 하나의 객체를 생성할 때 사용한다. 여러객체를 생성하고 싶은때는 다수 메소드가 필요하다. 관련 있는 다수의 객체를 다수의 메소드로 생성하고 이러한 메소드들을 추상화 된 인터페이스로 제공하는 패턴이 추상팩토리 패턴이다.

### 4.2 추상팩토리 패턴 정의

추상팩토리 패턴은 여러개의 메소드가 선언되어 있는 인터페이스와 그에 따른 구현클래스를 통해서 객체 집단군에 대한 팩토리를 제공하는 패턴이다. 

### 4.3 추상팩토리 패턴 구조

![abstract-factory-pattern-01](/public/images/abstract-factory-pattern-01.png)

추상팩토리 패턴이 구현되었다면 크게 다섯가지 구성원으로 구현된다. 해당구성원은 Client, AbstractFactory, ConcreteFactory, AbstractProduct, ConcreteProduct이다. 

* Client: Client는 관련 객체군에 의존하고 객체군을 생성하기 위해 팩토리에 의존한다. 그리고 Client는 OCP와 DIP를 지키기 위해서 객체군과 팩토리에 대해서 구상클래스 타입으로 의존하지 않고 추상화 된 인터페이스를 통해서 의존한다.

* AbstractProduct: Client가 의존하는 객체군에 대한 추상화 된 인터페이스이다. 해당 인터페이스는 객체군에서 객체의 수만큼 존재 할 수 있다. 위의 UML에서는 AbstarctProductA, AbstractProductB와 같이 두개가 존재한다. Client가 의존하는 객체군에서 객체의 수는 2개라고 예상할 수 있다.

* ConreteProduct: AbstractProduct의 구현체이다. 객체군의 관련성에 의해 구현클래스의 수가 늘어난다. 위 UML에서는 두가지의 관련성에 의해서 객체군이 두개 존재한다고 할 수 있다. 첫번째 객체군은 ProductA1, ProductB1이고 두번째 객체군은 ProductA2와 ProductB2이다. Client에서 구동되는 객체군들은 첫번째 객체군일수도 있고 두번째 객체군일 수도 있다.

* AbstractFactory: Client가 객체군을 생성하기 위해 의존하는 추상화된 팩토리 인터페이스이다. 

* ConcreteFactory: 실제 객체군을 생성해주는 팩토리 구현클래스이다. ConreteProduct와 마찬가지로 객체군의 관련성에 의해서 팩토리 구현클래스의 수가 늘어난다. 위 UML에서는 2개의 관련성에 의한 2개의 객체군이 존재함으로 2개의 팩토리 구현 클래스가 존재한다. 첫번째 팩토리 구현클래스 ConcreteFactory1는 ProductA1과 ProductB1에 대한 객체생성을 담당하고 두번째 팩토리 구현클래스 ConcreteFactory2는 ProductA2와 ProductB2에 대한 객체생성을 담당한다.

### 4.4 추상팩토리 패턴 구현

추상팩토리가 사용되는 상황과 추상팩토리 패턴을 구현해본다. 해당 소스코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/java/src/designpatterns/abstractfactory01)에 저장되어 있다.

* Client
```java
public class Client {

    AbstractProductA abstractProductA;
    AbstractProductB abstractProductB;
    AbstractFactory abstractFactory;
    public Client(AbstractFactory abstractFactory) {
        this.abstractFactory = abstractFactory;
    }

    void process(){
        createProduct();
        abstractProductA.firstMethodInProductA();
        abstractProductA.secondMethodInProductA();
        abstractProductB.firstMethodInProductB();
        abstractProductB.secondMethodInProductB();
    }

    private void createProduct(){
        abstractProductA = abstractFactory.createProductA();
        abstractProductB = abstractFactory.createProductB();
    }
}
```
Client는 두개의 AbstractProduct에 의존한다. 두개의 AbstractProduct 의해 참조되는 객체들은 관련성에 의한 객체군이라고 할 수 있다. 그리고 해당 객체군을 생성하기 위해서 AbstractFactory에 의존한다. createProduct()를 보면 객체군에 존재하는 두개의 객체를 생성한다. Product든 Factory든 객체군의 관련성에 의한 OCP와 DIP를 지키기 위해 추상화 된 인터페이스로 표현되어있다.

* AbstractProduct, AbstractFactory
```java
interface AbstractProductA {
    void firstMethodInProductA();
    void secondMethodInProductA();
}

interface AbstractProductB {
    void firstMethodInProductB();
    void secondMethodInProductB();
}

interface AbstractFactory {
    AbstractProductA createProductA();
    AbstractProductB createProductB();
}
```

Client에서 의존하는 인터페이스 AbstractProduct, AbstractFactory의 정의이다. 

* ConreteProduct, ConcreteFactory
```java
public class ConcreteProductA1 implements AbstractProductA{

    @Override
    public void firstMethodInProductA() {
        System.out.println("here is firstMethodInProductA1");
    }

    @Override
    public void secondMethodInProductA() {
        System.out.println("here is secondMethodInProductA1");
    }
}

public class ConcreteProductB1 implements AbstractProductB{

    @Override
    public void firstMethodInProductB() {
        System.out.println("here is firstMethodInProductB1");
    }

    @Override
    public void secondMethodInProductB() {
        System.out.println("here is secondMethodInProductB1");
    }
}

public class ConcreteFactory1 implements AbstractFactory {
    @Override
    public AbstractProductA createProductA() {
        return new ConcreteProductA1();
    }

    @Override
    public AbstractProductB createProductB() {
        return new ConcreteProductB1();
    }
}
```

첫번째 객체군의 구현클래스와 해당 객체군을 생성해주는 팩토리 구현클래스이다. ConcreteFactory1의 createProductA()는 ConcreteProductA1 객체를 생성하고 createProductB()는 ConcreteProductB1 객체를 생성하고 있는 것을 확인 할 수 있다. 두번째 객체군의 구현클래스와 팩토리 구현클래스는 같은 구조를 하고 있으므로 생략한다.

## 5. 팩토리 패턴 정리

구상클래스에 의존하지 않은 채로 객체를 생성하기 위한 목적으로 팩토리 패턴를 사용한다. 팩토리 패턴는 두가지 사용목적에 따라 두가지가 존재한다. 하나의 객체를 생성하기 위해서는 팩토리 메소드 패턴을 사용한다. client와 팩토리를 분리시키기 위해서 상속을 사용한다. 그리고 여러개의 객체를 생성하기 위해서는 추상팩토리를 사용한다. 추상팩토리는 인터페이스를 통해서 client와 분리되므로 구상을 사용한다. 