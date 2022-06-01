---
layout: post
title: "SOLID란 무엇인가?"
date: 2022-03-13
categories: DesignPattern
---

# 1.SOLID란 무엇인가?

개발 되어지는 프로그램의 규모가 점점 커짐에 따라 언어 또한 절차지향언어에서 객체지향언어로 전환되게 되었다. 객체지향 언어를 잘 사용하기 위한 원칙으로 Agile Manifesto의 창립자 로버트 마틴은 설계 및 프로그래밍에 대해서 5가지 원칙을 명명하였다. 그 5가지 원칙의 앞글자를 따서 SOLID라고 한다. SOLID에는 SRP, OCP, LSP, ISP, DIP 이렇게 다섯가지가 존재한다. 이번 포스팅에서는 해당 다섯가지 윈칙에 대해서 설명해본다.

# 2. 단일 책임 원칙 (SRP Single Responsibility Principle)

# 2.1 

# 3. 개방 폐쇄 원칙 (OCP Open Close Principle)

해당 원칙은 OCP(Open close principal)원칙이라고도 한다. 4.1에서 설명한 바와 같이 어플리케이션을 개발하면서 항상 변화가 발생한다. 이러한 변화에 대해서 소스코드를 수정하지 않고 소스코드를 확장(추가)하면서 변화에 대응해 나가는 것을 OCP원칙이라고 한다. 즉, 수정에 대해서는 닫혀 있고(close) 확장에 대해서는 열려있음(open)을 의미한다. 수정 된 코드는 다른 코드에 영향을 줄 수 있으므로 개발 할 때 비용을 많이 발생시킨다. 하지만 확장(추가)는 다른 코드에 영향을 줄 가능성이 적어 수정보다 훨씬 적은 비용을 개발을 진행시킬수 있다. 


# 4. 리스코프 치환 원칙 (LSP Liskov Substitution Principle)

# 5. 인터페이스 분리 원칙 (ISP Interface Segregation Principle)

# 6. 의존관계 역전 원칙 (DIP Dependency Inversion Principle)
