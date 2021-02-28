# 객체지향 설계 Writed by Habin Kim

***OBJECTS : 코드로 이해하는 객체지향 설계 -  조영호 지음*** 를 읽고 정리한 내용입니다.

Write Since 2019. 08.02. ~ 2020.11.03. *copied Markdown*.

[TOC]

------



## 0. 프로그래밍 패러다임

현대를 살아가는 우리는 다양한 패터다임의 홍수 속에 살고 있다. 새로운 사상이나 개념을 선전하기 위해 사용되는 '패러다임 전환'이라는 말을 예로 들지 않더라도 패러다임이라는 단어는 다양한 곳에서 우리의 눈과 귀를 사로잡고 있다.

프로그래밍 패러다임`(programming paradigm)`이라는 용어를 처음 사용한 사람은 1974년에 전산학의 노벨상이라 불리는 튜링상을 수상한 로버트 플로이드(Robert W. Floyd)다. 
튜링상을 수상한 사람은 관례적으로 "ACM 튜링상 강연(ACM Turing Award lecture)"이라는 이름의 강의를 하게 되는데 로버트 플로이드의 강의 제목이 바로 `<<The Paradigms of Programming>>` 이었다.



**프로그래밍 패러다임**

1. 특정 시대의 어느 성숙한 개발자 공동체에 의해 수용된 프로그래밍 방법과 문제 해결 방법, 프로그래밍 스타일
2. 간단히 말해서 우리가 어떤 프로그래밍 패러다임을 사용하느냐에 따라 우리가 해결할 문제를 바라보는 방식과 프로그램을 작성하는 방법이 달라짐
3. 개발자 공동체가 동일한 프로그래밍 스타일과 모델을 공유할 수 있게 함으로써 불필요한 부분에 대한 의견 충돌을 방지
4. 이것을 교육시킴으로써 동일한 규칙과 방법을 공유하는 개발자로 성장할 수 있도록 준비시킬 수 있다.

**`이 책이 쓰여진 이유! -> 이 책은 객체지향 패러다임에 관한 책이다.`**

## 01. 객체, 설계

> **"이론이 먼저일까, 실무가 먼저일까?"** - Robert L. Glass <Software Creativity 2.0>

대부분의 사람들은 이론이 먼저 정립된 후에 실무가 그 뒤를 따라 발전한다고 생각한다. **하지만 글래스는 어떤 분야를 막론하고 이론을 정립할 수 없는 초기에는 실무가 먼저 급속한 발전을 이룬다고 한다.**

실무가 어느 정도 발전하고 난 다음에야 비로소 실무의 실용성을 입증할 수 있는 이론이 서서히 그 모습 을 갖춰가기 시작하고, **해당 분야가 충분히 성숙해지는 시점에 이르러서야 이론이 실무를 추월하게 된다는 것이다.**

### 01. 티켓 판매 애플리케이션 구현하기

> 연극이나 음악회를 공연할 수 있는 작은 소극장을 경영하고 있다고 상상해 보자. 여러분은 소극장의 홍보도 겸할 겸 관람객들의 발길이 이어지도록 작은 이벤트를 기획하기로 했다. 이벤트의 내용은 간단한데 추첨을 통해 선정된 관람객에게 공연을 무료로 관람할 수 있는 초대장을 발송하는 것이다.
>
> 염두에 둬야할 점이 있다.
>
> - 이벤트에 당첨된 관람객과 그렇지 못한 관람객은 다른 방식으로 입장시켜야 함
> - 이벤트에 당첨된 관람객은 초대장을 티켓으로 교환한 후에 입장할 수 있음
> - 이벤트에 당첨되지 않은 관람객은 티켓을 구매해야ㅁ나 입장할 수 있음
> - 관람객을 입장시키 전에 이벤트 당첨 여부를 확인해야 하고 당첨자가 아닌 경우에는 티켓을 판매한 후에 입장시켜야 함



먼저 이벤트 당첨자에게 발송되는 초대장을 구현하는 것으로 시작하자. 

초대장이라는 개념을 구현한 `Invitation`은 공연을 관람할 수 있는 초대일자(when)를 인스턴스 변수로 포함하는 간단한 클래스

```java
public class Invitation {
	private LocalDateTime when;
}
```
공연을 관람하기 원하는 모든 사람들은 티켓을 소지하고 있어야만 한다. `Ticket` 클래스를 추가하자.

```java
public class Ticket {
    private Long fee;

    public Long getFee() {
        return fee;
    }
}
```

관람객이 가지고 올 수 있는 소지품
- 초대장
- 현금
- 티켓

관람객이 소지품을 보관할 Bag 클래스

Bag 인스턴스의 상태는 현금과 초대장을 함께 보관하거나 초대장 없이 현금만 보관하는 두 가지 중 하나일 것이다. 
Bag의 인스턴스를 생성하는 시점에 이 제약을 강제할 수 있도록 생성자를 추가하자.

```java
public class Bag {

    private Long amount;
    private Invitation invitation;
    private Ticket ticket;

    public boolean hasInvitation() {
        return invitation != null;
    }

    public boolean hasTicket() {
        return ticket != null;
    }

    public void setTicket(Ticket ticket) {
        this.ticket = ticket;
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }

	public Bag(Long amount) {
		this(null, amount);
	}

	public Bag(Invitation invation, Long amount) {
		this.invitation = invitation;
		this.amount = amount;
	}

}
```

관람객을 표현하는 `Audience` 클래스

``` java
public class Audience {
    
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Bag getBag() {
        return bag;
    }

}
```

관람객이 소극장에 입장하기 위해서는 매표소에 초대장을 티켓으로 교환하거나 구매해야 함.
따라서 매표소에는 관람객에게 판매할 티켓과 티켓의 판매 금액이 보관돼 있어야 한다.
매표소를 표현하는 `TicketOffice` 클래스

``` java
public class TicketOffice {

    private Long amount;
    private List<Ticket> tickets = new ArrayList<>();

    public TicketOffice(long amount, Ticket... tickets) {
        this.amount = amount;
        this.tickets.addAll(Arrays.asList(tickets));
    }

    public Ticket getTicket() {
        return tickets.remove(0);
    }

    public void minusAmount(Long amount) {
        this.amount -= amount;
    }

    public void plusAmount(Long amount) {
        this.amount += amount;
    }

}
```
판매원은 매표소에서 초대장을 티켓으로 교환해 주거나 티켓을 판매하는 역할을 수행한다. 
판매원을 구현한 `TicketSeller` 클래스는 자신이 일하는 매표소를 알고 있어야 한다.

``` java
public class TicketSeller {

    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public TicketOffice getTicketOffice() {
        return ticketOffice;
    }

}
```



![](https://i.ibb.co/KzYf4wL/class.png")



소극장을 구현하는 클래스는 Theater. Theater 클래스가 관람객을 맞이할 수 있도록 `enter` 메서드를 구현한다.

``` java
public class Theater {

    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);1
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
    
}
```



- 소극장은 먼저 관람객의 가방 안에 초대장이 들어 있는지 확인한다.
- 만약 초대장이 들어 있다면 이벤트에 당첨된 관람객이므로 판매원에게서 받은 티켓을 관람객의 가방 안에 넣어준다.
- 가방 안에 초대장이 없다면 티켓을 판매해야 한다.
- 이 경우 소극장은 관람객의 가방에서 티켓 금액만큼을 차감한 후 매표소에 금액을 증가시킨다.
- 마지막으로 소극장은 관람객의 가방 안에 티켓을 넣어줌으로써 관람객의 입장 절차를 끝낸다.

하지만 이 프로그램은 몇 가지 문제점을 가지고 있다.



### 02. 무엇이 문제인가

<클린 소프트웨어 : 애자일 원칙과 패턴, 그리고 실천방법; 로버트 마틴 저>에서 소프트웨어 모듈이 가져야 하는 세 가지 기능

1. 실행 중에 제대로 동작하는 것이다.
2. 변경을 위해 존재하는 것이다.
3. 코드를 읽는 사람과 의사소통하는 것이다.

> 모든 소프트웨어 모듈에는 세 가지 목적이 있다.  
> 첫 번째 목적은 **실행 중에 제대로 동작하는 것이다**. 이것은 모듈의 존재 이유라고 할 수 있다. 
> 두 번째 목적은 **변경을 위해 존재하는 것이다**. 대부분의 모듈은 생명주기 동안 변경되기 때문에 간단한 작업만으로도 변경이 가능해야 한다. 변경하기 어려운 모듈은 제대로 동작하더라도 개선해야 한다. 
> 모듈의 세 번째 목적은 **코드를 읽는 사람과 의사소통하는 것이다**. 모듈은 특별한 훈련 없이도 개발자가 쉽게 읽고 이해할 수 있어야 한다. 읽는 사람과 의사소통할 수 없는 모듈은 개선해야 한다.



앞에서 작성한 프로그램이 만족하는 것

- [x] 실행 중에 제대로 동작하는 것이다.
- [ ] 변경을 위해 존재하는 것이다.
- [ ] 코드를 읽는 사람과 의사소통하는 것이다.



#### 예상을 빗나가는 코드

`Theater`클래스와 `enter`메서드가 수행하는 일

> 소극장은 관람객의 가방을 열어 그 안에 초대장이 들어 있는지 살펴본다. 가방 안에 초대장이 들어 있으면 판매원은 매표소에 보관돼 있는 티켓을 관람객의 가방 안으로 옮긴다. 가방 안에 초대장이 들어 있지 않다면 관람객의 가방에서 티켓 금액만큼의 현금을 꺼내 매표소에 적립한 후에 매표소에 보관돼 있는 티켓을 관람객의 가방 안으로 옮긴다.



**문제점 1 : 관람객과 판매원이 소극장의 통제를 받는 수동적인 존재**

- 관람객 시점의 문제 : 소극장이라는 제3자가 초대장을 관람객의 가방을 마음대로 열어 봄
- 판매원 시점의 문제 : 소극장이 허락도 없이 매표소에 보관 중인 티켓과 현금에 마음대로 접근할 수 있음

이해 가능한 코드란 그 동작은 우리의 예상에서 크게 벗어나지 않는 코드이지만 **예제는 우리의 예상을 벗어난다.**



**문제점 2 : 이 코드를 이해하기 위해서는 여러 가지 세부적인 내용들을 한꺼번에 기억하고 있어야 함**

`Theater`의 `enter` 메서드를 이해하기 위해서 기억해야 할 사실

-  `Audience`가  `Bag`을 가지고 있음
- `Bag` 안에는 현금과 티켓이 들어 있음
-  `TicketSeller`가 `TicketOffice`에서 티켓을 판매
- `TicketOffice` 안에 돈과 티켓이 보관되어 있음



이 코드는 하나의 클래스나 메소드에서 너무 많은 세부사항을 다루기 때문에 코드를 작성하는 사람뿐만 아니라 코드를 읽고 이해해야 하는 사람 모두에게 큰 부담을 준다.



**문제점 3 : `Audience`와 `TicketSellet`를 변경할 경우 `Theater`도 함께 변경해야 함 (가장 큰 문제점!)**



#### 변경에 취약한 코드



- 관람객이 가방을 들고 있지 않다면?
- 관람객이 현금이 아니라 신용카드를 이용해서 결제한다면?
- 판매원이 매표소 밖에서 티켓을 판매해야 한다면?

다른 클래스가 `Audience`의 내부에 대해 더 많이 알면 알수록 `Audience`를 변경하기 어려워진다.

이것은 객체 사이의 **의존성(Dependency)**과 관련된 문제다. 문제는 의존성이 변경과 관련돼 있다는 점. 의존성이라는 말 속에는 어떤 객체가 변경될 때 그 객체에게 의존하는 다른 객체도 함께 변경될 수 있다는 사실이 내포돼 있다.

객체 사이의 의존성을 완전히 없애는 것이 정답은 아님. 객체지향 설계는 서로 의존하면서 협력하는 객체들의 공동체를 구축하는 것.

**따라서 개발자의 목표는 애플리케이션의 기능을 구현하는 데 필요한 최소한 의존성만 유지하고 불필요한 의존성을 제거하는 것!**

![](https://i.ibb.co/n7xyBrM/ex2.png)



- 객체 사이의 의존성이 과한 경우를 가리켜 **결합도(coupling)이 높다**라고 한다.
- 객체들이 합리적인 수준으로 의존할 경우에는 **결합도가 낮다**고 한다.
- 결합도는 의존성과 관련돼 있기 때문에 결합도 역시 변경과 관련이 있음
- 두 객체 사이의 결합도가 높을수록 함께 변경될 확률도 높아지기 때문에 변경하기 어려워짐

**설계의 목표는 객체 사이의 결합도를 낮춰 변경이 용이한 설계를 만드는 것!**



### 03. 설계 개선하기

- 주목해야할 점 : 변경과 의사소통이라는 문제가 서로 엮여 있다는 것
- 코드를 이해하기 어려운 이유 : `Theater`가 관람객의 가방과 판매원의 매표소에 직접 접근하기 때문



해결 방법

- `Theater`가 `Audience`와 `TicketSeller`에 관해 너무 세세한 부분까지 알지 못하도록 정보를 차단
- `Theater`가 원하는 것은 관람객이 소극장에 입장하는 것뿐
- 관람객과 판매원을 **자율적인 존재**로 만들기



#### 자율성을 높이자

- 설계를 변경하기 어려운 이유 : `Theater`가 `Audience`와 `TicketSeller` 뿐만 아니라 `Audience` 소유의 `Bag`과 `TicketSeller`가 근무하는 `TicketOffice`까지 마음대로 접근할 수 있기 때문
- **`Audience`와 `TicketSeller`가 직접 `Bag`과 `TicketOffice`를 처리하는 자율적인 존재가 되도록 설계를 변경**



1. `Theater`의 `enter` 메소드에서 `TicketOffice`에 접근하는 모든 코드를 `TicketSeller` 내부로 숨기기

```java
public class Theater {

    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        ticketSeller.sellTo(audience);
    }
    
}
```

```java
public class TicketSeller {

    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            Ticket ticket = ticketOffice.getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketOffice.getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketOffice.plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }

}
```

- `TicketSeller`에서 `getTicketOffice` 메소드가 제거되었다. 
- `ticketOffice`의 가시성이 `private`이고 접근 가능한 퍼블릭 메소드가 더 이상 존재하지 않기 때문에 외부에서는 `ticketOffice`에 직접 접근할 수 없다. 결과적으로 `ticketOffice`에 대한 접근은 오직 `TicketSeller` 안에만 존재하게 된다.
- 이처럼 개념적이나 물리적으로 객체 내부의 세부적인 사항을 감추는 것을 **캡슐화(encapsultation)**라고 한다.
- 캡슐화의 목적 : 변경하기 쉬운 객체를 만드는 것
- `Theater`는 오직 `TicketSeller`의 **인터페이스(interface)**에만 의존함. 
- `TicketSeller`가 내부에 `TicketOffice` 인스턴스를 포함하고 있다는 사실은 **구현(implementation)**의 영역에 속함.
- 객체를 인터페이스와 구현으로 나누고 인터페이스만을 공개하는 것은 객체 사이의 결합도를 낮추고 변경하기 쉬운 코드를 작성하기 위해 따라야 하는 가장 기본적인 설계 원칙이다.

![](https://i.ibb.co/YZYf1wP/ex3.png)
수정 후의 클래스 사이의 의존성을 나타낸 것

 - `TicketOffice`와 협력하는 `TicketSeller`의 내부 구현이 성공적으로 캡슐화됨
 - `TicketSeller`는 `Audience`의 `getBag` 메소드를 호출해서 `Audience` 내부의 `Bag` 인스턴스에 직접 접근
 - `Bag` 인스턴스에 접근하는 객체가 `Theater`에서 `TicketSeller`로 바뀌었을 뿐 `Audience`는 여전히 자율적인 존재가 아님


`Bag`에 접근하는 모든 로직을 `Audience` 내부로 감추기 위해 `Audience`에 `buy` 메소드를 추가하고 
`TicketSeller`의 `sellTo` 메소드에서 `getBag` 메소드에 접근하는 부분을 `buy` 메소드로 옮기자.

```java
public class Audience {
    
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Long buy(Ticket ticket) {
        if (bag.hasInvitation()) {
            bag.setTicket(ticket);
            return 0L;
        } else {
            bag.minusAmount(ticket.getFee());
            bag.setTicket(ticket);
            return ticket.getFee();
        }
    }

}
```

```java
public class TicketSeller {

    private TicketOffice ticketOffice;
    
    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void sellTo(Audience audience) {
        ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }

}
```
  


## 02. 객체지향 프로그래밍

## 03. 역할, 책임, 협력

## 04. 설계 품질과 트레이드오프

## 05. 책임 할당하기

## 06. 메시지와 인터페이스

## 07. 객체 분해

## 08. 의존성 관리하기

## 09. 유연한 설계

## 10. 상속과 코드 재사용

## 11. 합성과 유연한 설계

## 12. 다형성

## 13. 서브클래싱과 서브타이핑

## 14. 일관성 있는 협력

## 15. 디자인 패턴과 프레임워크

## 부록 A. 계약에 의한 설계

## 부록 B. 타입 계층의 구현

## 부록 C. 동적인 협력, 정적인 코드



