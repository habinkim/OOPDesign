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
판매원은 매표소에서 초대장을 티켓으로 교호나해 주거나 티켓을 판매하는 역할을 수행한다. 
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

소극장을 구현하는 클래스는 Theater

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



