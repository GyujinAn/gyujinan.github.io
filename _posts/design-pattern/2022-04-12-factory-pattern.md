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

### 2.1 왜 팩토리 메소드 패턴을 사용하는가?

추상메소드에서 구상클래스 별로 특정 인스턴스가 필요하다면 어떻게 생성해야될까? 추상메소드에서 구상클래스 별로 특정 인스턴스를 생성한다면 추상메소드가 OCP와 DIP를 위반하게 된다. 이러한 상황에서 팩토리 메소드 패턴을 이용하여 OCP와 DIP를 지키도록 할 수 있다.

### 2.2 병렬 클래스 계층 구조

보통 팩토리 메소드 패턴은 병렬 클래스 계층 구조에서 많이 사용된다. 병렬 클래스 계층 구조란 상위클래스를 확장한 서브클래스 별로 특정 인터페이스 구현체가 존재하는 구조이다. 아래의 팩토리 메소드 패턴의 UML를 살펴보면 Creator를 상속한 ConcreteCreator 별로 ConcreateProduct가 존재 할 수 있다. 이렇게 ConcreateCreator가 확장되면 될수록 ConcreteProduct도 병렬적으로 확장되는 구조를 병렬 클래스 계층 구조라고 한다.

### 2.3 팩토리 메소드 패턴 정의

상위클래스에서 사용하는 특정 인스턴스를 하위클래스에서 생성하도록 함으로써 상위클래스의 OCP와 DIP를 지키도록 하는 디자인 패턴이다.

### 2.4 팩토리 메소드 패턴의 구조

![factory-method-pattern-01](/public/images/factory-method-pattern-01.png)

Creator는 ConcreateCreator의 상위클래스이다. Creator에서 OPC와 DIP를 지키기 위해서 ConcreteProduct는 의존하지 않은 채 Product에만 의존하여 ConcreteProduct를 사용해야한다. 그러므로 Creator는 팩토리 메소드를 추상메소드로 선언하고 실제 인스턴스 생성은 서브클래스에서 추상메소드를 구현하여 한다. ConcreteCreator와 ConcreteProduct는 병렬 구조를 이루고 있기때문에 ConcreteCreator는 자신이 어떤 ConcreteProduct 인스턴스를 생성해야 하는지 알아야한다.

### 2.5 팩토리 메소드 패턴의 구현

팩토리 메소드 패턴을 직접 구현해본다. Creator는 어떤 데이터를 파일로 출력하는 로직을 가지고 있는 모듈이라고 가정한다. Product는 파일의 메타데이터를 저장하는 모듈이라고 가정한다.

<br>

일단 Creator와 그에 해당하는 구상클래스를 구현해본다. Creator는 FilePrinter라는 추상클래스로 구현하고 구상클래스는 파일의 특성에 따라 ImgFilePrinter, MusicFilePrinter, VideoFilePrinter와 같이 구현한다.

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

Product와 그에 해당하는 구상클래스를 구현해본다. Product는 ContentFile라는 추상클래스로 구현하고 구상클래스는 파일의 특성에 따라 ImgFile, MusicFile, VideoFile와 같이 구현한다.

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

Product의 구상클래스에서는 Creator클래스에서 필요로 하는 getPath()메소드를 구현하여 제공한다.