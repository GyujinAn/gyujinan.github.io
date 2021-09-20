---
layout: post
title: "2020년 12월 15일 Github 뉴스"
date: 2021-08-18
categories: news
---

최근에 git push를 실행했었다. 하지만 아래 같은 에러가 발생하였다. 

```
remote: Support for password authentication was removed on August 13, 2021. Please use a personal access token instead.
remote: Please see https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/ for more information.
fatal: unable to access 'https://github.com/GyujinAn/gyujinan.github.io.git/': The requested URL returned error: 403
```

에러의 내용은 8월 13일부터 패스워드 인증 방식이 폐기되었고 개인 접근 토근을 사용해야된다는 내용이다. 

그에 대한 [깃허브 블로그](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/)의 포스팅이 있어 번역하였다.


# 깃 작업을 위한 토큰 인증 요구사항

2020년 7월 GitHub의 Git 인증과 관련하여 토큰 인증 사용을 요구 할 것에 대한 우리의 생각을 알려왔다. (예를 들어, a personal access, OAuth, GitHub App installation 토큰) 다가오는 2021년 8월 13일부터 GitHub의 Git인증에 대해 우리는 더 이상 패스워드 인증을 허용하지 않을 것이다.

### 영향을 받는 작업들

* 명령어를 통한 Git 접근
* Git을 사용하는 데스크톱 애플리케이션 (GitHub 데스크톱은 제외)
* 패스워드를 사용하여 직접적으로 GitHub의 Git 저장소에 접근하는 애플리케이션

아래의 사용자들은 위와 같은 변화에 대해서 어떠한 영향도 받지 않는다.

* 만약 당신의 계정이 이중 인증 방식을 통해서 인증 받고 있다면, 당신은 이미 토큰 인증 방식 혹은 SSH 기반의 인증방식을 사용한다.
* 만약 당신이 GitHub 기업용 서버를 사용하고 있다면, 우리의 온프레미스 제품에 대해서는 기존의 방식을 유지 할 것이다.
* 만약 당신이 GitHub App을 사용하고 있다면, 패스워드 인증이 지원되지 않으므로 기존 방식에 영향을 받지 않을 것이다.

### 배경

우리는 아래와 같은 유사한 API인증 변경에 대하여 알리면서, 변경에 대한 이유를 설명하였다.

```
 최근 몇년 간 GitHub의 몇몇 보안 기술을 향상시키므로써 GitHub 사용자들의 보안에 대한 안전성을 높일 수 있었다. 보안 기술에 대한 예를 들면 이중 인증 방식, 로그인 알람, 검증 된 기기를 통한 인증 방식, 손상 된 패스워드 방지, 웹 인증 지원이 있었다. 이러한 기술들은 어떠한 공격자가 여러 사이트에 사용 된 패스워드를 가져와 당신의 GitHub계정에 대한 접근 권한을 가져오지 못 하도록 한다. 이러한 기술 향상에도 불구하고, 역사적인 이유로 인해 이중 인증을 사용하지 않고 오직 GitHub의 username 과 패스워드 통해 인증하는 사용자들의 인증 방식을 계속 허용해왔다.
```

다가오는 2021년 8월 13일 부터, 우리는 더 이상 Git 작업에 대한 계정 비밀번호 방식의 인증을 허용하지 않을 것이다. 그리고 사용자들에게 토큰 기반 인증 사용을 요구 할 것이다. 예를 들면 GitHub의 모든 Git작업에 대한 인증을 위해 개인 접근 토큰 (개발자용) 혹은 OAuth 혹은 GitHub 앱 설치 토큰 (통합자용)이 있다. 또 당신이 원하는 곳에서 SSH 키는 계속 사용 할 수 있다.

토큰 인증 방식은 기존의 패스위드 인증 방식보다 몇가지 보안적 이점이 있다.

```
유일성 - 토큰은 GitHub에만 해당하며 사용자 혹인 장치 별로 생성되어 질 수 있다.

취소 가능 - 다른 인증에 대한 업데이트 없이 토큰은 개인적으로 언제든 취소 될 수 있다.

제한성 - 토큰을 통한 접근가능한 GitHub의 기능을 제한 할 수 있다.

무작위성 - 토큰은 사전형식의 인증 정보 혹은 우리가 기억하고 정기적으로 입력하는 패스워드 형식 즉, 다수의 시도를 강요함으로써 예측 가능한 인증 정보가 아니다.
```

### 사용자가 해야 할 사항

* 개발자의 경우, 현재 만약 당신이 GitHub.com에서 Git 작업을 위해 패스워드 인증을 사용하고 있다면 작업의 중단을 피하기 위해 당신은 2021년 8월 14일까지 HTTPS(권장) SSH key를 통한 개인 접근 토근을 사용해야한다. 만약 당신이 "you are using an outdated third-party integration"와 같은 경고 메세지를 받는다면 당신은 당신의 클라이언트 어플리케이션을 최신 버전으로 업데이트 해야됩니다.

* 통합업체의 경우, 작업의 중단을 피하기 위해 당신은 2021년 8월 13일까지 웹 혹은 장치 인가 방식을 사용하여 통합업체들을 인증해야된다. 자세한 내용은 OAuth 앱 승인 혹은 개발자 블로그 공지를 참조하세요.


