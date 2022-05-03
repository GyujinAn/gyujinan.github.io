---
layout: post
title: "데코레이터 패턴(decorator pattern)"
date: 2022-04-01
categories: DesignPattern
---

# 데코레이터 패턴(decorator pattern)

## 1. 왜 데코레이터 패턴을 사용하는가? 

### 1.1 객체의 Optional 로직의 특성

개발을 진행하다보면 Optional하게 주요로직 전후에 실행되는 로직들이 존재한다. buffer를 사용한다거나 인증처리를 하는 로직들이 이와 같은 로직이라고 말할 수 있다. 이러한 로직들은 런타임시 상황에 따라 필요 할 수 있고 필요하지 않을 수도 있다. 그러므로 Optional한 로직과 주요로직은 분리되어 개발되어져야한다. 주요로직과 Optional한 로직은 하나의 함수나 메소드에 넣어서 개발한다면 컴파일 후에는 두 로직은 항상 함께 사용되어 질 것이다. 그로 인해 두 로직이 유연하지 못 하고 단단하게 결합되는 결과를 초래한다.

## 2. 데코레이터 패턴이란 무엇인가?

### 2.1 데코레이터 패턴의 정의

데코레이터 패턴이란 주요로직과 Optional한 로직을 서로 다른 클래스로 분리하여 런타임시 첨가하는 방법으로 유연성과 확장성을 보장해주는 디자인 패턴이다. 

### 2.2 데코레이터 패턴의 구조

![decorator-pattern-01](/public/images/decorator-pattern-01.png)

데코레이터 패턴은 Component와 Decorator 두 종류의 클래스로 크게 나눌 수 있고 Decorator클래스는 Component를 상속하고 있다. Component에는 클라이언트에게 제공할 메소드를 선언해야한다. 해당 메소드는 하나의 시그니처로 클라이언트에 제공되지만 런타임시에는 Optional한 로직과 주요로직을 순차적으로 실행하게 된다. 주요로직은 Component를 상속한 ConcreteComponent에 구현되고 Optional한 로직은 Decorator를 상속한 ConcreteDecorator에 구현된다.

### 2.3 데코레이터 패턴의 구현

위 내용을 바탕으로 직접 데이코레이터 패턴을 구현해본다. 사용자 정보를 저장하는 어플리케이션을 개발하는 경우 사용자 정보를 변환하는 데코레이터나 사용자 정보의 validation체크 기능을 하는 데코레이터가 필요 할 수 있다. 이러한 경우를 가정하여 데코레이터로 해당 기능을 수행하는 어플리케이션을 구현해본다. 아래의 코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/java/src/designpatterns/decorator01)에서 저장되어있다.

위 데코레이터 패턴의 다이어그램에 맞게 클래스를 정의한다.

* Componet: MemberService

* ConcreteComponent: MemberServiceImpl

* Decorator: DecorateMemberService

* ConcreteDecorator: StringLenthMemberService, ConvertMemberService

#### 2.3.1 Interface MemberService

클라이언트가 사용 할 인터페이스를 정의한다. 클라이언트는 save 메소드를 통해서 회원정보를 저장 시킬 수 있다.
```java
public interface MemberService {
    String save(Member member);
}
```

#### 2.3.2 Class MemberServiceImpl

실제로 회원 정보를 저장하는 구현체를 정의한다. 
```java
public class MemberServiceImpl implements MemberService{

    Map<String, Member> memberStorage = new HashMap();

    @Override
    public String save(Member member) {
        memberStorage.put(member.getMemberName(), member);
        return "Save successfully";
    }
}
```

#### 2.3.3 Interface DecorateMemberService

MemberService 인터페이스를 상속하여 데코레이터들에게 Componet를 구현하도록 한다. 그러므로 실제로 추가되는 메소드는 존재하지 않는다.

```java
public interface DecorateMemberService extends MemberService{
}
```

#### 2.3.4 Class StringLenthMemberService, ConvertMemberService
데코레이터 인터페이스를 구현체로서 MemberServiceImpl에 대한 Optional한 로직을 수행한다. 생성자를 통해서 MemberService에 대한 의존성을 주입받는 걸 확인 할 수 있다. MemberService 타입으로 save 메소드를 호출하면 다음 데코레이터를 호출하거나 주요로직을 호출하거나 할 수 있다. 

```java

public class StringLenthMemberService implements DecorateMemberService{

    MemberService memberService;

    private final int MEMBER_NAME_MAX_LENGTH = 10;

    public StringLenthMemberService(MemberService memberService) {
        this.memberService = memberService;
    }

    @Override
    public String save(Member member) {
        if(member.memberName.length() > MEMBER_NAME_MAX_LENGTH){
            return "Too long member name";
        }
        //다음 데코레이터 혹은 컴포넌트의 메소드 호출
        return memberService.save(member);
    }
}

public class ConvertMemberService implements DecorateMemberService {

    MemberService memberService;

    public ConvertMemberService(MemberService memberService) {
        this.memberService = memberService;
    }

    @Override
    public String save(Member member) {
        if(member.gender.equals("남")){
            member.setGender("M");
        } else if(member.gender.equals("여")){
            member.setGender("W");
        } else {
            return "Invalid member gender";
        }
        //다음 데코레이터 혹은 컴포넌트의 메소드 호출
        return memberService.save(member);
    }

}

```

### 2.4 데코레이터 패턴의 사용

데코레이터 패턴으로 구현한 모듈들은 클라이언트 측에서 인스턴스를 생성해야한다. 인스턴스를 생성 할 때 클라이언트 측에서는 어떤 데코레이터를 추가하고 어떤 순서로 추가 할지 결정 할 수 있다. 

#### 2.4.1 실행순서
main 메소드에서 두개의 데코레이터를 생성하였다. 그리고 컴포넌트 인스턴스를 생성 한 후, 두개의 데코레이터를 추가하였다. 데코레이터의 순서는 StringLenthMemberService -> ConvertMemberService 이다.

```java
 */
public class Main {

    public static void main(String[] args) {
        Member member = new Member();
        member.setMemberName("tom");
        member.setGender("남");
        MemberService memberService =
                new StringLenthMemberService(new ConvertMemberService(new MemberServiceImpl()));
        System.out.println(memberService.save(member));
    }

}
```

1. StringLenthMemberService에서는 사용자 이름의 길이를 체크한다. 길이가 너무 길면 다음 데코레이터를 호출하지 않고 리턴한다.

2. ConvertMemberService에서는 성별에 대한 한글데이터를 알파벳으로 변환한다. vaild한 데이터가 아니라면 컴포넌트를 호출하지 않고 리턴한다.

3. MemberServiceImpl에서는 주요로직이 실행되어 데이터가 저장된다. 해당 로직이 실행 되었다는 것은 validation체크와 데이터 변환이 완료되었음을 의미하기도 한다.

## 3. 데코레이터 패턴의 한계

객체지향적 개발은 최대한 클라이언트 측에서 구상클래스에 의존하지 않도록 해야한다. 데코레이터 패턴은 인스턴스를 생성시점 이후로 구상클래스에 의존하지 않도록 개발되지만 인스턴스를 생성하는 시점에 component와 decorator의 구상클래스에 의존 할 수 밖에 없다는 한계를 가지고 있다. 이러한 한계를 극복하기 위해서는 팩토리 패턴을 사용하여 인스턴스 생성을 클라이언트 측 코드로 부터 분리해야 한다.

## 4. Spring에서 데코레이터 패턴

스프링에서 회원가입 로직에 데코레이터를 적용해본다. 자바에서 구현한 데코레이터와 거의 흡사하지만 다른 점이 있다면 스프링에서는 @Configuration을 통해서 데코레이터와 컴포넌트를 빈으로 생성하여 사용한다는 것이다. 추가적으로 api를 작성하여 런타임시에 동적으로 데코레이터를 바꾸는 방법도 구현해본다. 코드는 [여기](https://github.com/GyujinAn/blog-sample-code/tree/main/spring/src/main/java/spring/decorator01)에서 저장되어 있다. 

위 데코레이터 패턴의 다이어그램에 맞게 클래스를 정의한다.

* Componet: MemberService

* ConcreteComponent: MemberServiceImpl

* Decorator: DecorateMemberService

* ConcreteDecorator: IdConversionMemberService, ConsistencyMemberService


### 4.1 MemberServiceConfiguration 

MemberServiceConfiguration를 통해서 데코레이터의 추가된 MemberService 타입의 빈을 생성 할 수 있다. 해당 코드에서는 IdConversionMemberService와 ConsistencyMemberService가 추가 된 빈객체인 idConversionConsistencyMemberServiceImpl를 생성하는 빈 메소드와 ConsistencyMemberService만 추가 된 빈객체인 consistencyMemberServiceImpl를 생성하는 빈 메소드를 볼 수 있다.

```java
@Configuration
public class MemberServiceConfiguration {

    @Autowired
    CertificationEmailRepository certificationEmailRepository;

    @Autowired
    MemberRepository memberRepository;

    @Autowired
    TeamRepository teamRepository;

    @Autowired
    MemberService memberServiceImpl;

    @Bean
    MemberService idConversionConsistencyMemberServiceImpl(){
        return new IdConversionMemberService(consistencyMemberServiceImpl());
    }

    @Bean
    MemberService consistencyMemberServiceImpl(){
        return new ConsistencyMemberService(memberServiceImpl, certificationEmailRepository, memberRepository, teamRepository);
    }
}
```

### 4.2 MemberController

MemberController를 통해서 회원가입에 대한 api요청을 정의한다. 그리고 데코레이터를 교체하는 api를 통해서 다른 데코레이터를 런타임시 동적으로 교체 할 수 있다. 아래 코드에서 MemberService에 대한 디폴트는 데코레이터가 없는 MemberServiceImpl 컴포너트의 빈객체가 주입되지만 /config/idconversion-consistenc, /config/consistency라는 엔드포인트를 통해서 memberService에 데코레이터가 추가된 빈객체를 교체할 수 있다.

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/member")
public class MemberController {

    private MemberService memberService;

    private final MemberService idConversionConsistencyMemberServiceImpl;

    private final MemberService consistencyMemberServiceImpl;


    @PostMapping
    public String singUp(@RequestBody Member member, HttpServletResponse httpServletResponse){

        try {
            memberService.save(member);
            return "회원가입 성공";
        } catch (Exception e) {
            e.printStackTrace();
            httpServletResponse.setStatus(400);
            return "회원가입 실패: " + e.getMessage();
        }
    }

    @PutMapping("/config/idconversion-consistency")
    public void setIdConversionConsistency(){
        this.setMemberService(idConversionConsistencyMemberServiceImpl);
    }

    @PutMapping("/config/consistency")
    public void setConsistency(){
        this.setMemberService(consistencyMemberServiceImpl);
    }

    @Autowired
    @Qualifier("memberServiceImpl")
    public void setMemberService(MemberService memberService) {
        this.memberService = memberService;
    }

}

```