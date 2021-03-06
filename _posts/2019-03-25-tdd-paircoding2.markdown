---
title: "[TDD] TDD 해보니까 어때? (2)"
layout: post
date: 2019-03-25
image:
headerImage: false
tag:
- 프로젝트 회고
category: blog
author: sehunkim
description: 초보 개발자의 TDD 체험기 2편
star: false # true로할 경우 제목에 highlight 처리가 됨
sitemap :
  changefreq : daily
  priority : 1.0
---

***이 글은 TDD를 처음 접한 초보개발자의 개인적인 회고이기에 몰랐던 기초 개념에 대한 설명들이 주를 이룬다.***

***바로 TDD를 통해 무엇을 개선했는지 읽고 싶은 분은 [이곳](#tdd_start)부터 읽는 것을 추천한다.***

<br>
<br>


# 두 가지 질문
내 프로젝트는 두 가지 질문에서 시작되었다.
- 코드의 품질이 무엇인지.
- TDD를 사용하면 무엇이 어떻게 개선되는지.

[앞선 글]({{ site.url }}/tdd-paircoding1)에서 '잘 작동하고', '중복이 없고', '유지보수가 쉽고', '가독성이 좋은 코드'가 **품질 높은 코드** 라는 것과, **페어코딩** 을 통해 '익숙하지 않은 TDD 방식을 의식적으로 유지할 수 있었다'는 것을 알았다.

### 이번엔 TDD로 무엇을 개선할 수 있는지 알아보자.

<div class="breaker"></div>

# 설계의 목표
콘솔 인디언 포커를 페어 코딩과 TDD 방식을 활용하여 개발하기 전, 파트너와 나는 몇 가지 목표를 정했다.
- **모든 로직은 TDD 방식으로 구현할 것.**
- **콘솔 기반으로 개발하되, 웹 프로젝트로 확장할 것을 고려하여 개발할 것.**

첫 번째는 개발 프로세스에 대한 목표였으며, 두 번째는 **설계(원칙)의 목표**였다.

### 프로그래밍 패러다임
```
프로그래밍 패러다임 == 프로그램 설계의 기본 원칙
```
프로그래밍할 때는 설계의 근간이 되는 **프로그래밍 패러다임** 을 갖고 임해야 한다.

![paradigm]({{ site.url }}/assets/images/paradigm.png)
<figcaption class="caption">패러다임이란?</figcaption>

'**Paradigm**'은 "사물에 대한 이론적인 틀"이라는 뜻이 있다. 그리고 패러다임 앞에 프로그래밍을 붙였을 때 "프로그래밍에 대한 이론적인 틀"이란 뜻을 갖게 된다. 즉, 프로그래밍 패러다임이란 **"어떤 관점을 갖고 프로그래밍을 할 것인가"이다.**.

익히 들어온 '절차적', '함수적', '객체지향적' 프로그래밍이 그런 프로그래밍 개발 방법을 뜻하는 단어이다.
> 여기서 구별해야 하는 것은 '**프로그래밍 패러다임**'과 '**프로그래밍 언어**'의 차이이다.

> 흔히 자바를 객체지향적 언어, C를 절차적 언어라 한다. 두 언어는 대표적으로 해당 프로그래밍 패러다임을 지원하기 위해 개발되었다.
> 그렇기에 프로그래밍 패러다임과 언어의 특성이 1:1로 매칭될 수 있다. 하지만 Python 같은 경우 '함수형'과 "객체지향" 두 방식을 지원한다. 그래서 Python을 완전히 객체지향 패러다임만 지원하는 언어라고만 할 수도 없다. 또한, 자바도 1.8버전부터 함수형 프로그래밍을 지원하기 시작했다.

> 그래서 꼭 프로그래밍 언어가 한 패러다임만을 지원하는 게 아니라는 것을 인지하고 있어야 한다.

근데 갑자기 패러다임 이야기는 왜 하는 것일까?


### TDD와 설계원칙
TDD 사이클은 매우 단순하다.
```
1. 원하는 문제를 해결할 테스트 케이스를 만든다.
2. 테스트 케이스를 통과시킬 수 있는 최소한의 코드를 작성한다.
3. 리팩토링하고, 다른 로직에 대해 1번부터 반복한다.
```

테스트 케이스는 임의의 값을 넣었을 때 원하는 결과가 나오는지만 정의하면 되고, 실제 코드는 통과할 수 있게만 작성하면 된다. TDD 사이클을 반복하며 가장 중요한 부분은 어떤 설계목표를 가지고 "**리팩토링**"을 하냐는 것이다.

#### 예제

###### Wine
```java
class Wine {
    int age;

    public Wine(int age) {
        this.age = age;
    }
}
```

###### SampleTest
```java
class SampleTest {
    public static void main(String[] args) {
        ripeningTest();
    }

    static void ripeningTest() {
        Wine whiteWine = new Wine(0);
        ripening(whiteWine, 3);

        System.out.println(whiteWine.age == 3); // true
    }
}
```

예를 들어 상단의 코드를 활용해서 Wine을 숙성시키는 ripening()이라는 로직을 만든다고 했을 때. 같은 로직을 만들더라도 **어떠한 관점으로 프로그래밍할 것인가에 따라 완성되는 코드가 달라진다.**
> jUnit 같은 테스트 프레임워크를 사용해야만 테스트를 작성할 수 있는 것은 아니다. 다만, 사용했을 때 훨씬 편해진다.
> 이번에는 간단하게 콘솔에 찍는 테스트를 해보자.

<br>

- ***절차적***

###### SampleTest
```java
class SampleTest {
    public static void main(String[] args) {
        ripeningTest();
    }

    static void ripeningTest() {
        Wine whiteWine = new Wine(0);
        ripening(whiteWine, 3);

        System.out.println(whiteWine.age == 3); // true
    }

    static void ripening(Wine wine, int agingPeriod) {
        int currentAge = wine.age;
        int ripenedAge = currentAge + agingPeriod;
        wine.age =  ripenedAge;
    }
}
```

###### Wine
```java
class Wine {
    int age;

    public Wine(int age) {
        this.age = age;
    }
}
```

절차적으로 프로그램을 설계한다면 **수행되어야 할 처리를 연속적으로 행한다는 원칙을 갖고있다.** 이때 행위와 상태는 분리되어 있어야 한다.

상단의 코드에서 age라는 상태를 지니는 것은 wine이라는 객체이다. 그리고 숙성시키는 행위는 ripening() 메소드에서 처리한다.

ripening() 메소드는 wine에서 현재 age를 가져와 숙성시킬 기간을 더하고, wine 객체의 age에 총 숙성된 age 값을 넣는 처리를 순차적으로 한다.

<br>

- ***함수형***

###### SampleTest
```java
class SampleTest {
    static void main(String[] args) {
        ripeningTest();
    }

    static void ripeningTest() {
        Wine whiteWine = new Wine(0);
        Ripening ripening = (wine, agingPeriod) -> new Wine(wine.getAge() + agingPeriod);

        System.out.println(ripening.apply(whiteWine, 3).getAge() == 3); // true
    }
}
```

###### Wine
```java
class Wine {
    private int age;

    public Wine(int age) {
        this.age = age;
    }

    public int getAge() {
        return this.age;
    }
}
```

###### Ripening
```java
@FunctionalInterface
public interface Ripening {
    Wine apply(Wine wine, int agingPeriod);
}
```

함수형 프로그래밍은 **부작용을 극단적으로 배제한다는 원칙을 갖고 있다.** 때문에 메소드의 매개변수로 들어가는 객체의 상태 값은 변하면 안 된다. 그래서 Wine은 **Immutable(불변하는)** 인스턴스이어야 한다.

코드의 변경사항을 보면 Wine 클래스의 age 필드에 직접 접근하는 것을 제한하여 상태를 변경하지 못하게 하였다. 그리고 상태 값은 get()메소드를 통해서만 조회할 수 있게 하였다.

만약 숙성된 상태의 와인을 얻고 싶으면, 함수형 인터페이스 Ripening의 apply를 실행하여 인자로 넣었던 wine의 기간보다 숙성된 새로운 와인을 새롭게 생성하여 리턴해야한다.

<br>

- ***객체지향적***

###### SampleTest
```java
public class SampleTest {
    public static void main(String[] args) {
        ripeningTest();
    }


    static void ripeningTest() {
        Wine whiteWine = new Wine(0);
        whiteWine.ripening(3);

        System.out.println(whiteWine.getAge() == 3); // true
    }
}
```

###### Wine
```java
public class Wine {
    private int age;

    public Wine(int age) {
        this.age = age;
    }

    public int getAge() {
        return this.age;
    }

    public void ripening(int agingPeriod) {
        this.age += agingPeriod;
    }
}
```

객체지향적으로 프로그래밍을 할 경우 **상태값을 지니는 객체에 직접 메시지를 보내, 직접 상태를 변화시키는 행위를 해야한다.** 때문에 Wine 클래스의 age 필드의 접근 제어자도 변경하였고, ripening() 메소드를 추가하여 직접 age값을 증가시키게 하였다.


#### 패러다임과 프로세스
내가 앞서 프로그래밍 패러다임에 관한 이야기를 한 이유는 **TDD와 설계원칙은 다른 개념이라는 것** 을 설명하기 위함이다.

프로그래밍 패러다임은 프로그램을 설계할 때의 근간이 되는 기본 원칙이다. 그리고 TDD는 어떤 식으로 개발을 진행할지 같은 개발 프로세스의 한 종류이다. 프로그래밍 패러다임이 여러 가지 인 것처럼, 개발 프로세스도 여러 가지가 있다.
&gt; TDD, Agile, Waterfall Model 등.

서로 종속적인 것이 아닌 분리된 개념이기에 TDD를 사용한다 해도 패러다임을 다르게 적용하여 개발할 수 있다. 결국, 같은 TDD를 사용한다 해도 설계원칙이 다르면 다른 코드를 만들게 된다.

<div class="breaker"></div>


# 확장하기 위한 설계
파트너와 나는 콘솔게임은 순수하게 **Java**만을 이용해 개발하기로 했고, 웹으로 프로젝트를 확장했을 때 **Spring-Boot** 프레임워크를 사용하기로 했다.

### 시작하기 전 고려할 부분
사실 콘솔 기반으로 두 명의 플레이어가 프로그램을 실행하고 딱 한 번 인디언 포커를 할 수 있게 만든다면 고려해야 하는 부분이 별로 없다.

##### [콘솔 게임의 실행 흐름]
```
1. 프로그램 실행시 한번의 게임을 진행할 수 있다.
2. 게임의 입출력은 콘솔창에서 이루어 진다.
3. 승자가 결정되면 게임은 종료된다.
```

일단 입출력은 모두 콘솔 영역에서 이루어진다. 그리고 한 번 프로그램을 실행했을 때 한 번의 게임만 할 수 있으므로 따로 영구적으로 데이터를 저장할 필요가 없다. 그래서 편하게 개발하려면 핵심 도메인이 입출력과 핵심로직, 상태를 모두 갖게 하면 된다.

하지만 웹 프로젝트를 했을 때 사용할 Spring은 핵심로직을 처리하는 부분과 입출력을 제어하는 부분이 분리되어있는 구조이다. 이 때문에 콘솔게임을 저 방식으로 개발하게 되면 많은 수정이 필요할 것이 분명했다.

#### MVC
SpringMVC는 **MVC 디자인패턴** 을 기반으로 설계된 프레임워크이다.
> 디자인 패턴이란 문제 해결법이다. 패러다임과는 다른데, 패러다임이 기본 원칙이라면 디자인 패턴이란 기본원칙을 지키기 위해 생기는 부수적인 문제들을 효과적으로 해결하기 위한 해결 방식이다.

> 패러다임과 동일하게 디자인패턴 또한 어떠한 언어에 종속되는 개념이 아니다. 그래서 여러 패러다임과 언어를 결합해서 사용할 수 있다. 당연히 디자인 패턴끼리도 결합이 가능하다.

![insane]({{ site.url }}/assets/images/mvc_3tier.png)
<figcaption class="caption">MVC + 3 Tier Architecture</figcaption>

MVC란 Model, View, Controller라는 특정 행위를 할 수 있는 영역을 분리한 것이다. MVC기반의 Web Framework를 사용할 때 보통 3-Tier-Architecture와 결합하여 사용한다.
```
- Model
실질적인 비즈니스 로직을 구현하는 역할을 담당함.
비즈니스 로직 처리 결과를 DB에 저장하고 조회하는 역할.
애플리케이션의 엔진.

- Controller
최초 진입 지점. 사용자의 입력 값이 유효한 지를 검증하고,
사용자가 입력한 데이터를 Model에 전달하고,
Model의 처리 결과에 따라 이동할 View를 결정하는 역할

- View
입력과 출력을 담당하는 역할.
```

MVC 패턴을 사용하면, 입출력과 입력 데이터를 실행 부에 전달하는 영역, 실제 데이터를 가공하는 영역이 나눌 수 있다. 그래서 각 부분의 수정을 유연하게 할 수 있다. 이를 통해 수정과 확장이 편해진다. 물론 각 역할에 맞는 클래스가 많아져 복잡도가 높아질 수 있지만, 그만큼 영역별로 확실하게 역할분담을 시킬 수 있다.

### MVC로 가즈아!
우리가 정한 콘솔 프로그램의 설계목표는 "웹 프로젝트로의 확장을 고려한 설계"였다.

나는 가능하다면 핵심로직을 웹 프로젝트로 옮겼을 때 그대로 사용하고 싶었다. 그래서 우린 콘솔 프로그램도 Spring으로 확장하기 좋게 만들기 위해 MVC 디자인패턴을 적용하여 개발하기로 정했다.

<div class="breaker"></div>

# <a id="tdd_start"></a>TDD, 자 이제 시작이야!
개발을 시작하려 할 때 우리는 어디서부터 시작해야 할지 막막했다.
우린 답을 찾기위해 실제 현실의 게임 규칙을 바탕으로 역할과 행위를 정의해 보았다.

![insane]({{ site.url }}/assets/images/game_rule.png)
<figcaption class="caption">Game Rule</figcaption>

그리고 이 규칙을 바탕으로 어떤 테스트를 할지 무작위로 적기 시작했다.

```java
/*
 * 실제 테스트는 아니며, 테스트를 만들기 위한 예시이다.
 * 우린 해당 테스트와 관련된 테스트가 모두 만들어지면 주석처리를 했다.
 */

public class IndianPokerTest {

    @Test
    public void 스무턴이지나면게임은종료() {
    }

    @Test
    public void 하나의플레이어라도칩이모두소진되면게임종료() {
    }

//    @Test
//    public void 플레이어는베팅할수있다() {
//    }

//    @Test
//    public void 플레이어는딜러에게칩을줄수있다() {
//    }

    @Test
    public void 딜러가게임의승패를판단한다() {
    }

    @Test
    public void 딜러가카드의크기를비교한다() {
    }

    @Test
    public void 딜러가게임의승자에게베팅된칩을분배한다() {
    }

    @Test
    public void 딜러가카드를비교했을때비겻을경우플레이어에게균등하게칩배분() {
    }

//    @Test
//    public void 승자는해당턴에배팅된모든칩을얻는다리() {
//    }

    @Test
    public void 하나의플레이어라도칩이모두소진되면턴은종료리() {
    }

    @Test
    public void 베팅이콜오어다이일경우턴종료() {
    }

    @Test
    public void 턴이시작할때양쪽플레이어의칩1개씩걸기() {
    }

    @Test
    public void 턴의첫베팅순서에는콜을할수없다() {
    }

    @Test
    public void 레이즈베팅은상대플레이어가가진칩보다많은칩을걸수없다() {
    }

    @Test
    public void 콜베팅의경우상대가건칩만큼걸어야한다() {
    }

    @Test
    public void 다이베팅을하면상대플레이어가해당턴의승자가된다() {
    }
}
```
일단 대충 적은 테스트 케이스를 기반으로 실제 테스트 케이스를 만들고, 추가로 생기는 로직에 대한 테스트 케이스를 만들면서 개발하였다. 상단의 테스트 코드는 할 수 있는 모든 케이스를 작성한 것은 아니었다. 하지만 **각 테스트가 개발을 시작할 수 있는 포인트가 되어 주었다.**

#### 위 방식으로 파트너와 나는 약 2개월에 걸쳐 콘솔 프로젝트를 완성할 수 있었다.
##### ***"이후의 웹 프로젝트는 나 혼자 진행하였고, 웹 프로젝트를 통해 WebSocket 통신을 경험했던 것은 따로 정리하고자 한다."***

## TDD를 통해 얻은 것
> 내 경험과 책 [TDD 실천법과 도구](https://repo.yona.io/doortts/blog/issue/1)의 내용 일부분을 인용

그래서 이렇게 고생하며 배운 TDD를 통해 무엇을 느낄 수 있었을까?

### 1. 개발 흐름을 쉽게 파악할 수 있다.
아무리 개발을 좋아해도 365일, 24시간 같은 프로젝트를 코딩하지 않을 것이다. 쉬었다 올 수도 있고 여러 프로젝트를 동시에 진행할 수도 있다. 이럴 때 본인이 만든 코드라도 다시 코딩할 때 파악하는 시간이 필요하다.

내가 처음 콘솔 인디언 포커를 만들 때, 약 이틀 정도 시간이 걸렸다. 혼자서 한 프로젝트를 연속해서 만들다 보니 어디까지 개발했고, 무엇을 만들고 있었는지 다시 파악할 필요가 없었다.
> 물론 설계를 개선해야겠다는 생각 없이 오롯이 구현에만 시간을 쏟았기에 가능했다.

새로 개발할 때는 사정이 달랐다. 페어 코딩으로 월요일과 목요일 각 4시간씩 개발하기로 했고, 페어 코딩을 하지 않을 때는 각자 다른 공부를 했다. 같이 코딩하는 날사이엔 평균 3일 정도의 기간이 있었기에, 코딩을 시작할 때마다 "이전에 어떤 부분을 개발하고 있었고, 이 로직이 어떤 일을 하는 것인지" 같은 개발의 흐름을 파악하는 시간이 필요했다.

이때 테스트 케이스가 흐름 파악하는 시간 단축에 큰 도움을 주었다.

#### 오랫동안 자리를 비울 땐 실패하는 테스트를 만들어라.
![DealerTest]({{ site.url }}/assets/images/testCase.png)
<figcaption class="caption">DealerTest의 일부분</figcaption>

위 테스트 케이스는 Turn을 종료할 때 Dealer가 Player의 승패를 판단하는 로직을 테스트하는 케이스이다. 이때 각자 일정이 맞지 않아 1주 정도 페어 코딩을 쉬었었다.

만약 테스트 케이스가 없는 일반적인 상황이었다면 흐름 파악에 더 많은 시간이 필요했을 것이다. 일단 프로그램을 실행해서 어떤 부분이 개발되지 않았는지 찾아보거나, 주석으로 어디까지 만들었고 어떤 부분을 더 만들어야 하는지 표시했어야 했을 것이다(이러한 주석들을 제거하는 것도 불편한 일이다).

그래서 우리는 **테스트 케이스를 일부러 실패하게 만들었다.**

<center><img class="image" src="{{ site.url }}/assets/images/test_fail.png" alt="DealerTest_fail"></center>
<figcaption class="caption">DealerTest Fail</figcaption>

이렇게 테스트 케이스 전체를 실행시키면 한번에 어떤 테스트 케이스가 실패하는지 알 수 있고, 이를 통해 어디까지 개발했었는지 확인할 수 있다.

#### 테스트 케이스는 설명서이다.
어디까지 개발했는지는 실패하는 테스트 케이스로 바로 알 수 있다. 그럼 기존에 있는 로직들이 어떤 로직인지 빠르게 확인하고 싶을 때는 어떻게 할까?

![BettingStateTest]({{ site.url }}/assets/images/bettingStateTest.png)
<figcaption class="caption">BettingStateTest의 일부분</figcaption>

위 테스트 케이스는 Player가 Betting 할 때마다 자신의 BettingState를 잘 변화시키고 있는지 확인하는 부분이다. 우리는 실제 프로덕션 코드의 이름에 대해서는 엄격했지만, 테스트 케이스의 이름은 확실하게 의미파악만 할 수 있다면 뭐든 좋았다.

보통은 한글로 작성하지 않겠지만, **테스트 케이스는 프로덕션 코드가 아니기에 무엇을 뜻하는지 장황하게 적어놔도 상관없다.** 그래서 우리는 최대한 테스트 케이스의 이름을 쉽게 해석할 수 있도록 작성했다.

또, 테스트하기 위해 필요한 요소들(변수, 테스트를 위해 비교하는 대상)도 어떠한 로직인지 파악하는 데 도움을 줬다. 그래서 우린 테스트 케이스를 통해 로직이 어떤 일을 하는지 빠르게 파악할 수 있었다.

### 2. 확장(변경)에 유연한 코드가 만들어 진다!
좋은 품질을 가진 코드에는 '**변경(확장)이 용의**'하다는 조건이 있다. 변경이 쉬운 코드에는 많은 종류가 있겠지만, 개인적으로 변경이 쉬우려면 각 모듈 간 의존성을 낮춘 코드여야 한다고 생각한다.

**의존성**이란 두 모듈 간의 관계이다. 객체지향 개념에서는 클래스 간의 의존관계로 표현할 수 있다.
> 참고 : 스프링의 DI를 설명하며 의존관계에 대한 예시가 설명된 [글]({{ site.url }}/springbean-lifecycle/#2)

예를 들어 A 기능을 실행할 때 B 기능이 필요하다. 이때 A는 B에 의존관계가 있다고 말할 수 있다. 만약 B기능의 사용법이 수정된다면 A기능도 B기능에 맞춰 수정해야 한다. 또, A 기능에서 B 기능이 아닌 새로운 기능을 사용하고 싶은 경우 수정이 필요하다. 의존성이 강할수록 각 기능의 수정이 어려워지고, 유지 보수하기 힘들어지는 것이다.

#### 테스트하기 쉬운 구조를 만들어라
***"우린 테스트 케이스를 먼저 만들면서 자연스럽게 의존관계에 대해 고민을 하며 개선하는 설계를 할 수 있었다."***

![Turn_field]({{ site.url }}/assets/images/Turn_field.png)
<figcaption class="caption">Turn class fields</figcaption>

**Turn** 클래스는 *Player*, *BettingTable*, *Dealer* 를 필드로 갖고 있다. Turn이 행위를 할 때 이 객체들이 필요하기에 해당 객체들과 의존관계를 갖고 있다고 볼 수 있다.
```
- Player
칩, 순서, 덱(카드묶음)을 상태로 가지며, 실제 베팅이라는 행위를 함

- Dealer
각 플레이어의 카드를 비교해 승패를 판단하는 행위를 함

- BettingTable
각 플레이어가 베팅한 칩들이 쌓이는 곳
```

**Turn** 클래스는 Player의 차례에 맞게 베팅하게 하고, 종료조건을 만족했을 때 각 플레이어의 카드를 비교하여 승자에게 베팅 된 칩을 배분하는 과정을 추상화한 것이다.

Turn을 테스트 하기 위해서는 실제 변화되는 부분(Player)과 변화하지 않는 부분(Turn이 가진 행위들)을 분리해야 한다. 만약 Turn에 의존관계가 있는 객체들을 직접 Turn 내부에서 생성해서 사용한다면, 테스트 조건에 맞춰 매번 코드를 수정해야 할 것이다.

그래서 우린 이러한 의존관계를 개선하기 위해 Player를 외부에서 생성하여 Turn 내부로 주입하는 방식(**DI**)을 사용하였다.

![TurnTest]({{ site.url }}/assets/images/turnTest.png)
<figcaption class="caption">TurnTest의 일부분</figcaption>

위 테스트 케이스를 보면 테스트 케이스마다 실제로 변화되는 부분인 Player를 생성해서 addPlayers()라는 메소드를 이용해서 주입하는 것을 볼 수 있다. 또한, Player를 인터페이스로 표준화하여 Player 역할을 할 수 있다면 어떠한 구현체라도 대응할 수 있도록 하였다. 우린 이를 통해 Turn과 Player 사이에 의존관계를 개선할 수 있었다.

***테스트 케이스를 만들 때 무엇을 고려하게 될까?***

테스트는 어떤 값을 넣었을 때 해당 값을 통해 원하는 값이 도출되는지 확인하는 것이다.
> 값을 변경했을 때 처리의 결과가 달라지는 것을 확인하는 것이 테스트의 목적이기 때문이다.

그래서 테스트 케이스를 만들다 보면 어떻게 하면 테스트를 쉽게 할 수 있을지 고민하게 된다. 이 과정에서 변화가 일어나는 부분(어떤 값)과 변화가 일어나지 않는 부분(실제 로직)을 분리하는 설계를 할 것이다.

#### 자신있게 로직을 변경할 수 있다.
<center><img class="image" src="{{ site.url }}/assets/images/input-output.JPG" alt="input-output.JPG"></center>
<figcaption class="caption">input - output</figcaption>

우리가 테스트 케이스를 만들어 통과시켜 놓으면, 그 테스트 케이스 자체가 로직을 실행하는 데 필요한 **입력 타입과 출력 타입의 표준이 된다.**

만약 로직에 대한 수정이 필요할 때 이미 만들어져 있는 테스트 케이스가 있으므로 다른 부분에 영향을 주는지에 대한 고민 없이 수정하고 테스트만 통과시키면 된다.


### 3. 품질이 보장된 코드를 갖게 된다.
나는 인디언 포커 게임을 콘솔 프로그램에서 웹 프로젝트로 확장할 때, 핵심 도메인 로직을 그대로 가져와서 사용할 수 있었다.

콘솔프로그램을 만들 때부터 **MVC**로 역할과 영역을 분리하여 개발하여, 핵심 도메인은 그대로 사용하고 컨트롤러와 뷰 영역만 웹에 맞게 변경할 수 있었기 때문이다.

##### "이때 나는 이미 만들어져있는 도메인 영역에 대해 다시 테스트하지 않았다."


#### 검증은 끝났다.
이미 있는 테스트 케이스를 수정하거나 다시 테스트를 해야하는 경우는 인풋 아웃풋이 변경되거나, 해당 테스트 케이스에 새로운 의존관계가 생겼을 때 뿐이다.

MVC 패턴을 쓸 때 View는 데이터의 입출력만을 담당한다. 그리고 Controller는 맞는 Model의 로직만 실행시키고, 실행 결과를 View 영역에 출력해주면 된다.

이렇게 확실하게 영역을 분리해 놓으니 테스트 케이스를 만들 때부터 최대한 다른 영역(Model, View)에 의존적이지 않게 설계를 하게 되었다. 이렇게 되니 도메인 로직 자체에 대한 코드를 수정할 필요가 없었다.

그래서 나는 이미 테스트를 거친 코드를 수정없이 그대로 사용할 수 있었다.

### 4. 테스트가 통과할 때 마다 느껴지는 성취감
##### 이 부분은 TDD를 통해 개선이 된 부분은 아니지만, 성취감을 느꼈던 부분이라 추가한 장점이다.

프론트 개발자는 실제 사용자가 조작할 인터페이스를 만들기 때문에 개발로 인해 변하는 것들을 즉시 피드백 받을 수 있다. 하지만 백엔드 개발자는 개발할 때 콘솔 창을 제일 많이 보게 된다.

그래서 아무래도 프론트 개발자가 백엔드 개발자보다 개발하면서 흥(?)이 더 오를 수 있다.
> 이 부분은 개인마다 성향이 있을 수 있다. 개인적으로 나는 백엔드가 더 좋다!

TDD를 사용할 경우 테스트 케이스를 매번 통과시켜, 나름 즉각적인 피드백을 받을 수 있다. 이게 나름 쏠쏠한 재미와 성취감을 주는 부분이다.

<center><img class="image" src="{{ site.url }}/assets/images/testAllSuccess.png" alt="testAllSuccess.png"></center>
<figcaption class="caption">Test All Success</figcaption>

특히 위 사진처럼 모든 테스트 케이스를 통과시켰을 때는 **쾌감까지 느껴진다.!**

<div class="breaker"></div>

# TDD 어땠어?
TDD를 사용해서 개발하면 무조건 좋은 것일까?

### Before & After
혼자 만든 콘솔 게임과 TDD를 사용해 개발한 콘솔게임 사이에는 큰 차이가 생겼다.
> 완성한 콘솔 프로젝트 [Github](https://github.com/Sehun-Kim/IndianPoker_JAVA_RE)

<center><img class="image" src="{{ site.url }}/assets/images/pakages.png" alt="pakages.png"></center>
<figcaption class="caption">indianpoker pakage</figcaption>

일단 클래스의 개수부터 차이났다. 전에는 4개의 클래스가 모든 기능을 담당했지만, 이번에는 클래스들을 묶은 패키지의 개수만 해도 6개였다.

패키지를 나눌 때 크게 핵심로직을 처리하는 도메인과 사용자의 입출력을 담당하는 뷰 영역과 필요한 행위를 실행할 수 있도록 하는 컨트롤러 영역을 만들었다.

그리고 다시 각 클래스가 하나의 역할만 가지도록 상태와 행위를 최대한 분리하였다. 또, 역할별로 행위를 표준을 인터페이스로 만들고 중복을 제거하기 위해 추상클래스를 만들었다.

이렇게 만들다 보니 클래스의 양도 전에 비해 기하급수적으로 늘어났다. 그러나 이러한 작업을 통해 코드 간 결합 도를 낮출 수 있었고, 중복을 최소화할 수 있었다.

***하지만 이건 TDD만을 통해 얻은 성과가 아니다.***

### 의식적인 리팩토링
```
TDD는 테스트와 코드작성과 리팩토링 과정 사이에 설계원칙을 지키며 코드를 개선하는 과정이다.
```

'**단위테스트**'와 '**TDD**'는 프로덕션 코드를 검증하기 위한 테스트 케이스가 있다는 점에서 같다. 둘 사이에 차이점이 있다면 테스트 케이스를 만드는 순서, TDD에 리팩토링이라는 설계 개선의 과정이 추가되었다는 것이다.

우리가 아무리 테스트 케이스를 만들고 통과하는 코드를 만든다 해도, 설계에 대한 고민을 하지 않는다면 **그저 단위 테스트만 반복하는 것이지 코드의 개선은 경험하지 못한다.**

![insane]({{ site.url }}/assets/images/insane.png)
<figcaption class="caption">미친짓이란?</figcaption>

내가 생각하는 TDD를 사용함으로써 얻을 수 있는 최고의 장점은 테스트와 설계를 같이 할 수 있다는 것이다. 하지만 TDD를 사용한다고 무조건 설계가 개선되는 것이 아니다. 리팩토링할 때 더 나은 코드가 무엇인지에 대한 고민하는 과정이 필요하다.

그래서 **의식적인 리팩토링** 이 필요하다. 리팩토링을 통해 목적에 맞는 코드를 수정하는 과정을 통해 코드의 개선을 경험할 수 있다.

```
TDD란 프로그래밍 의사결정과 피드백 사이의 간극을 의식하고 이를 제어하는 기술이다.
- 켄트벡, Test Driven Development by Example 중
```

개발할 때, 우리가 현실 세계에서 어떤 문제를 해결하느냐에 따라 적합한 설계원칙이 있을 것이다. 또 그 설계 원칙에 효율적으로 코드를 작성하기 위한 디자인 패턴이 있을 것이다. 우리는 이 모든 것을 리팩토링 과정에서 고민하고 적용해야 한다.

***내가 설계를 개선할 수 있었던 것은 이 리팩토링 과정에서 객체 지향적 원칙이 무엇인지 파트너와 고민하고, 막힌 문제가 생겼을 때 어떤 디자인 패턴이 우리에게 도움을 줄 수 있을지 학습하여 적용하였기 때문이다.***


### 결론

앞글에서 스승님께서 해주신 말씀을 인용했었다.
```
"TDD를 잘 활용하면 설계를 개선하고 '품질 높은 코드'를 만들 수 있다."
```

스승님께서는 하신 말씀 중 내가 미쳐 인지하지 못한 부분이 있다. 해당 내용을 추가하여 문장을 완성해보자.

```
TDD와 의식적인 리팩토링을 반복하면,
설계를 개선하고 '품질 높은 코드'를 만들 수 있다.
```

#### 세상은 넓고 개발방식은 많다. 개발자라면 문제를 해결하기 위해 어떤 것을 사용해야할지 끊임없이 고민하고 공부하자!
