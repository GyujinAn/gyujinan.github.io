---
layout: page
title: About
permalink: /about/
---

# 안규진 | Gyujin An

> 기술 관련 토론을 좋아하는 개발자입니다. <br>
> 토론을 위한 메일은 언제든지 환영입니다.

## CONTACT

* email: agj017@gmail.com

* 코드 저장소: [github](https://github.com/GyujinAn)

<!-- * [linkedin](https://www.linkedin.com/in/gyujin-an-515985192/) -->

## SKILL

### Backend  
* java 
* spring boot
* spring framework 
* spring data
* spring cloud 
* maven
* junit

### Middleware
* keycloak
* vault
* rabbitmq

### Database 
* mariadb 
* postgresql 
* redis

### Infra 
* Linux(ubuntu)
* docker
* kubernetes
* openstack 
* jenkins 
* git
* helm

### 프론트관련기술 
* javascript
* vue.js
* html

<!-- ## Career
오케스트로
클라우드 플랫폼 연구소 CloudOps팀(정규직)
2020.10 - 현재 재직중
 ● 1. Cloud Management Platform의 ​​AS(Authorization Server)개발 2020.10 -


프로젝트 설명
cmp(Cloud Management Platform)를 개발하기 위한 프로젝트이다. cmp란 어플리케이션 개발 및 운영에 필요한 인프라 및 미들웨어를 제공해주고 관리하기 위한 플랫폼이다. 해당 플렛 폼은 mas구조로 되어 있고 spring, vue.js, k8s를 통해 개발 및 운영된다. 이와 같은 퍼블릭 cmp는 AWS, GCP가 존재한다.
개발한 프로그램 설명
cloud내의 oauth 클라이언트들에게 oauth2.0의 인증 인가 서비스를 제공하기 위해 keycloak 를 사용하고 있었으나 유연한 커스텀을 위해 사내 자체적인 AS개발을 진행하였다. 개발은 Spring Security Oauth 프레임워크를 통해 진행하였다.
업무 및 성과
- 사내 자체 AS서버 개발
- cmp의 인증 및 인가 구현
● 2. Cloud Management Platform의 Gateway Api 개발 2020.10 - 프로젝트 설명
1.번과 같은 프로젝트
개발한 프로그램 설명
cmp의 api서버 보안 관리와 통신이력 관리 그리고 향후 트래픽 관리까지 고려하여 Gateway Api를 개발하였다. 개발은 Spring Cloud Gateway 프레임워크를 사용하여 진행하였다.
업무 및 성과
- 설정을 통한 cmp의 api서버 보안 관리 실현(cors, jwt유효성검사, ip 및 네트워크 필터링 등 등)
- cloud 관리자에게 제공할 api 요청 이력 데이터 구축
● 3. Cloud Management Platform의 회원 관리 모듈 개발 2020.10 - 2022.01 프로젝트 설명
1.번과 같은 프로젝트
개발한 프로그램 설명
cmp 회원 관리를 위한 RESTful api서버 및 화면를 개발하였다. 백엔드 쪽 개발은 Spring을 통 해서 진행하였고 프론트엔드 개발은 Vue.js 프레임워크를 이용하여 진행하였다.
업무 및 성과
- 회원에 대한 CURD 기능 구현
- 회원 관련 프론트 엔드 화면 구현(회원가입 화면 등등) - 회원 관련 추가 인증 기능 구현(이메일 인증, 캡쳐 등등)
기억에 남는 트러블슈팅
- 문제 : Spring boot의 설정파일에 의해 특정기능을 추가하거나 삭제하는 요구가 있었다. 그에 따라 특정기능들을 Decorator패턴으로 설계하고 Spring의 application.yml을 통해서 Decorator추가 여부를 결정하였다. 그런데 최초의 decorator를 제외한 두번째부터의 decorator들과 component(component 데코레이터 패턴의 컴포넌트를 의미하며 spring의 컴포넌트를 의미하지 않는다.)는 의존성을 주입을 받지 못 하여 NullPointException이 발생하 였다. (component에 아무런 decorator를 설정하지 않은 경우에는 의존성 주입을 정상으로 받아 NullPointException 발생하지 않았다.)
- 원인 : 최초의 decorator의 참조값은 Bean생성 메소드를 통해 return되므로 Spring Bean 으로 등록이 되어 의존성 주입을 받게 되었다. 하지만 두번째의 decorator들 혹은 component 은 new식(인스턴스 생성식)로 생성 된 후 최초의 decorator에 의해 참조는되지만 Bean생성 메소드 통해 return 되지 않았으므로 Spring Bean으로 등록되지 않아 의존성 주입을 받지 못 하게 된 것이었다.(decorator가 존재하지 않는 경우는 Bean생성 메소드가 component인스 턴스의 참조값을 return하므로 해당 component인스턴스는 의존성 주입을 받았다.)
- 해결 : 해당 Bean생성 메소드 안에 존재하는 new에 대응되는 Bean생성 메소드를 만들어 호 출해줌으로써 Decorator패턴으로 설계 된 모든 decorator와 component를 Spring Bean으 로 등록시켜 의존성 주입을 받도록 했다.
● 4.CloudManagementPlatform인프라구축및관리 2020.10-
프로젝트 설명
1.번에서 설명한 프로젝트는 기존에 VM기반으로 운영 관리 되어왔다. 개발 및 운영 비용 절감과 향후 대용량 트래픽에 대한 대비로 K8S 기반으로 회사 정책이 전환되었고 그에 따라 K8S환경을 구축하고 필요한 미들웨이를 설치 및 관리 하는 프로젝트이다.
사용 프로그램 - 오픈스택
- K8S
- Jenkins
- Helm
- Bitbucket
업무 및 성과
- 오픈스택 기반의 K8S 구축 및 관리
- helm을 이용한 cmp 미들웨어 구축 및 관리
- Jenkins를 이용한 cmp CI/CD 환경 구축 및 관리 - CMP helm 차트 작성 및 관리
- CMP 소스코드 형상 관리
기억에 남는 트러블슈팅
- 문제 : ​​cmp를 배포하는 경우 설정값이 spring application.yml에 존재하므로 빌드이후에는

수정 할 수 없었다. 그러한 상황은 많은 운영비용을 발생시킨다. 배포이후 설정값이 틀렸다거나 수정되는 경우는 빌드부터 다시 시작해야되기 때문이다.
- 원인 : 설정값들이 빌드시점에 결정되어버리기 때문이다. 설정값의 결정 시점을 배포시점으로 미룰 필요가 있다.
- 해결 : K8S의 ConfigMap 리소스를 사용하여 설정값을 외부에서 관리하였다. 그리고 배포 시 점에서는 spring app이 의존하는 os의 환경변수에 주입시켜줌으로써 빌드이후에도 자유롭게 설정값을 변경 할 수 있도록 하였다. -->



[jekyll themes](http://jekyllthemes.org/themes/monos/)