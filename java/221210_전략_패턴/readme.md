# [디자인 패턴] 전략 패턴





## 전략 패턴이란?

위키피디아에 따르면 **전략 패턴은 실행 중에 알고리즘을 선택할 수 있게 하는 행위 디자인 패턴**이라고 한다. 객체가 선택할 수 있는 행위, 즉 전략을 객체로 간주하여 캡슐화 해놓고 동적으로 변경할 수 있게 한다고 풀어쓸 수 있을 것 같다. 더 직관적으로 줄이면 **행동을 객체로 만든다**고 할 수 있을 것 같다. 사실 풀어썼다고 해도 이런 식으로는 잘 와닿지 않는다. 전략 패턴의 생겨나게 된 배경부터, 구체적인 사례까지 쭉 살펴보겠다.



## 전략 패턴은 왜 생겼을까?

### 미션: 게임의 직업 클래스를 만들자

전략 패턴의 가장 직관적인 예시는 역시 게임 캐릭터라고 생각된다. 새로 오픈하는 RPG 게임에서 캐릭터의 각 직업 클래스를 구현하게 됐다고 해보자. 구현해야 될 직업은 총 5개, 캐릭터의 행동은 3개다.

- 직업
  - 전사
  - 마법사
  - 도적
  - 궁수
  - 해적
- 행동
  - 움직이기
  - 공격하기
  - 앉기

게임 플레이어는 모두 움직이고, 공격하고, 앉을 수 있다. 그렇지만 선택한 직업의 특성에 따라 각 행동은 조금씩 달라야 한다. 구현할 객체를 `Player`란 인터페이스로 정의하면 다음과 같다.

```java
public interface Player {
		void move();
    void attack();
    void sit();
}
```

이 인터페이스의 문제는 무엇일까? 게임 기획서를 자세히 보니, 앉는 행동은 모든 직업이 똑같은 동작을 수행해야 한다. 자리에 그대로 주저 앉아서 10초에 10만큼 체력을 회복하는 동작이다. 물론 각 직업의 구현체에 같은 코드를 복사+붙여넣기 해도 된다. 좋은 방식은 아니지만, 가능한 선택지다. 그런데 오늘 임원회의에서 직업을 100개까지 늘리자는 말이 나왔단다. 언젠가 100개 직업이 다 구현되면 `sit()` 메소드의 수정이 생길 때마다 100번을 붙여넣어야 한다.

### 상속: 객체의 행동을 재사용

`sit()` 메소드를 여러 객체에서 재사용하기 위해 상속을 하기로 했다. 부모 클래스에 메소드를 구현하면 자식 객체에서 그대로 사용할 수 있다. 만약 자식 클래스들 간에 공통적인 행동과 개별적인 행동이 존재한다면? 추상 부모 클래스에서 개별 행동만 추상 메소드로 선언할 수 있다. 그럼 같은 행동은 물려받으면서, 또 다른 행동을 가진 자식 클래스들을 여러 개 가질 수 있다. 아래와 같이 `Player`를 인터페이스에서 추상 클래스로 수정했다.

```Java
// 추상 부모 클래스
public abstract class Player {

    // 추상 메소드는 각 직업 클래스에서 구현한다.
    public abstract void move();
    public abstract void attack();

    // 모든 직업 클래스는 똑같은 앉는 행동을 한다.
    public void sit() {
        System.out.println("자리에 앉아 체력을 10씩 회복합니다.");
    }
  
}

// 구체 자식 클래스(전사)
public class Warrior extends Player {

  public void move() {
    System.out.println("쿵쾅쿵쾅 움직입니다.");
  }

  public void attack() {
    System.out.println("슬래시 블러스트!");
  }

}

// 구체 자식 클래스(마법사)
public class Magician extends Player {

  public void move() {
    System.out.println("스르르륵 움직입니다.");
  }

  public void attack() {
    System.out.println("매직 클로!");
  }

}

// 구체 자식 클래스(도적)
public class Thief extends Player {

  public void move() {
    System.out.println("슉슉슈슉 움직입니다.");
  }

  public void attack() {
    System.out.println("더블 스탭!");
  }

}
```

이제 `Player`를 상속하는 전사, 마법사, 도적, 궁수, 해적 클래스는 똑같은 앉는 동작을 한다. 그리고 `move()`와 `attack()`은 각 자식 클래스에서 구현한다.

문득 그런 생각이 들었다. 나중에 체력을 20씩 회복하는 스페셜 직업을 기획해서 던지면 어떡하지? 자식 클래스에서 메소드를 재정의할 수도 있긴 한데. 한두개면 괜찮아도 20개를 스페셜 직업이라고 하는 거 아냐? 이게 맞는 방식일까?

### 상속의 문제점 1: 관리가 어렵다

이런 생각을 하던 중에 기획이 바뀌었다. 100개의 전혀 다른 직업을 만들어낼 아이디어가 없단다. 다음과 같이 5개의 직업군이 각각 20개의 세부 직업을 갖도록 하라고 한다.

- 전사
  - 파이터
  - 스피어맨
  - ...
- 마법사
  - 불/독 위자드
  - 얼음번개 위자드
  - ...
- 도적
  - 어쌔신
  - 시프
  - ...
- ...

세부 직업에 따라 `attack()`은 모두 다르지만, `move()`는 직업군 안에서 똑같다고 한다. 그럼 `move()`는 5개의 코드를 각각 20번씩 붙여넣기 해야한다. 물론 그것보단 추상 클래스를 상속하는 추상 클래스를 직업군별로 만드는 편이 좋아보인다. 아래처럼 직업군 추상 클래스를 만들었다. `sit()`은 공통으로 상속받고, `attack()`은 여전히 추상이다.

```java
// 전사 직업군의 추상 클래스
public abstract class Warrior extends Player {
	  public void move() {
        System.out.println("쿵쾅쿵쾅 움직입니다.");
    }
}

// 전사 직업의 구체 클래스(파이터)
public class Fighter extends Warrior {
    public void attack() {
      System.out.println("칼로 벱니다.");
    }
}

// 전사 직업의 구체 클래스(스피어맨)
public class Spearman extends Warrior {
    public void attack() {
      System.out.println("창으로 찌릅니다.");
    }
}

// 마법사 직업군의 추상 클래스
public abstract class Magician extends Player {
    public void move() {
      System.out.println("스르르륵 움직입니다.");
    }
}

// [생략] 마법사 직업의 구체 클래스들
// ...

// 도적 직업군의 추상 클래스
public abstract class Thief extends Player {
    public void move() {
      System.out.println("슉슉슈슉 움직입니다.");
    }
}

// [생략] 도적 직업의 구체 클래스들
// ...
```

그럼 이제 각 직업군을 상속하는 구체 직업 구현체에서 `attack()`만 구현하면 될까?

### 상속의 문제점 2: 동작을 동적으로 바꿀 순 없다

아이디어가 넘치는 기획자는 전사와 도적의 퓨전 직업을 생각해냈다. 이 직업은 플레이어의 조작에 따라 파이터의 움직임과 어쌔신의 움직임을 왔다갔다 할 수 있다. 어떻게 해야될까? flag에 따라 파이터와 어쌔신의 움직임을 하도록 분기 처리해야 할까? 이 경우 flag에 따른 각 움직임의 코드는 파이터, 어쌔신의 코드와 동일해야 한다. 그러면서도 전체 `move()`코드는 같지 않다. 중복되면서도 단순히 붙여넣기도 어려운 구조다.

또 만약에 같은 전사 직업군에서도 움직임을 다르게 해달라는 요구사항이 들어오면 어떻게 해야할까? 쿵쾅쿵쾅 전사와 뚜벅뚜벅 전사 같은 특성을 나눠서 추상 클래스를 정의해야 할까? 그 다음에 기존에 만들어진 모든 구체 클래스들이 쿵쾅쿵쾅이나 뚜벅뚜벅을 상속하도록 수정해야 할까?

이제 전략 패턴이 등장할 때다.



## 전략 패턴: 행동의 캡슐화

### 추상 클래스와 전략의 인터페이스 정의

```java
public abstract class Player {

    MoveBehavior moveBehavior;
    AttackBehavior attackBehavior;

  	// 구체적인 행동은 모른 채로 움직임을 수행한다.
    public void move() {
    		moveBehavior.performMove();
    }

    // 구체적인 행동은 모른 채로 공격을 수행한다.
    public void attack() {
    		attackBehavior.performAttack();
    }

    public void sit() {
    		System.out.println("자리에 앉아 체력을 회복합니다.");
    }
}

// 움직이는 행동을 캡슐화 하는 인터페이스
public interface MoveBehavior {
  	void performMove();
}

// 공격하는 행동을 캡슐화 하는 인터페이스
public interface AttackBehavior {
 		void performAttack();
}
```

아주 간단한 전략 패턴 구조다. `move()`에서 구현될 각 알고리즘을 `MoveBehavior`로 캡슐화 했다. `attack()`도 마찬가지다. 이제 각 `Behavior`의 구현체를 정의하고, `Player` 구체 클래스에서 세팅만 해주면 된다. 

### 행동 인터페이스의 구현

먼저 각 직업군의 움직임, 그리고 각 직업의 공격을 구현해보겠다.

```java

// 전사 직업군의 움직임
public class WarriorMoveBehavior implements MoveBehavior {
    public void performMove() {
      	System.out.println("쿵쾅쿵쾅 움직입니다.");
    }
}

// 마법사 직업군의 움직임
public class MagicianMoveBehavior implements MoveBehavior {
    public void performMove() {
      	System.out.println("스르르륵 움직입니다.");
    }
}

// 도적 직업군의 움직임
public class ThieifMoveBehavior implements MoveBehavior {
    public void performMove() {
      	System.out.println("슉슉슈슉 움직입니다.");
    }
}

// 파이터 직업의 공격
public class FighterAttackBehavior implements AttackBehavior {
    public void performAttack() {
      	System.out.println("칼로 벱니다.");
    }
}

// 스피어맨 직업의 공격
public class SpearmanAttackBehavior implements AttackBehavior {
    public void performAttack() {
      	System.out.println("창으로 찌릅니다.");
    }
}

// 어쌔신 직업의 공격
public class AssassinAttackBehavior implements AttackBehavior {
    public void performAttack() {
      	System.out.println("표창을 던집니다.");
    }
}
```

위의 코드가 바로 각 직업군, 또는 직업의 전략이 된다. 직업 클래스의 메소드에 들어갈 내용을 인터페이스와 그 구체 클래스로 캡슐화한 것이다. 그리고 각 직업은 자신의 상황에 맞춰서 알맞은 전략을 선택하면 된다.

### 구체 클래스의 구현

지금까지 만든 걸 기반으로 직업 클래스를 만들어보겠다.

```java
public class Fighter extends Player {
    public Fighter() {
        // 전사의 움직임 전략을 세팅한다.
        this.moveBehavior = new WarriorMoveBehavior();
      
        // 파이터의 공격 전략을 세팅한다.
        this.attackBehavior = new FighterAttackBehavior();
    }
}


public class AssassinFighter extends Player {
  
    // 기본 세팅은 파이터로 한다.
    public AssassinFighter() {
        this.moveBehavior = new WarriorMoveBehavior();
        this.attackBehavior = new FighterAttackBehavior();
    }

    // 사용자 조작에 따라 어쌔신 모드로 전환한다.
    public void switchMode() {
        System.out.println("모드를 전환합니다.");
        this.moveBehavior = (this.moveBehavior instanceof WarriorMoveBehavior)
          ? new ThieifMoveBehavior() : new WarriorMoveBehavior();
        this.attackBehavior = (this.attackBehavior instanceof FighterAttackBehavior)
          ? new AssassinAttackBehavior() : new FighterAttackBehavior();
    }
}
```

보는 바와 같이 각 `Behavior`의 필드에 구현체를 넣어주면 끝이다. 여기선 단순하게 새 객체를 사용했지만 외부에서 주입받아도 된다. `AssassinFighter`에는 전략을 바꾸는 `switchMode()` 도 추가했다. 아니면 세터를 통해 다른 전략으로 바꿔도 좋다. 중요한 것은 객체의 행동을 보다 유연하게 정의할 수 있다는 것이다.

### 직업 클래스 테스트

```java
public class StrategyTest {
    @Test
    void test2() {
        System.out.println("Fighter를 생성합니다.");
        Player player1 = new Fighter();
        player1.move();
        player1.attack();
        player1.sit();

        System.out.println();

        System.out.println("AssassinFighter를 생성합니다.");
        Player player2 = new AssassinFighter();
        player2.move();
        player2.attack();
        ((AssassinFighter) player2).switchMode();
        player2.move();
        player2.attack();
    }
}
```

간단하게 파이터와 어쌔신파이터 인스턴스를 생성하고, 동작을 수행해보았다. 수행 결과는 아래와 같다. 

 ![image1](/Users/infomuscle/Desktop/bokeun/blog/temp/전략_패턴/image1.png)

다른 직업이지만 두 클래스는 같은 움직임과 공격을 하는 것을 알 수 있다. 하지만 각 움직임과 공격은 `WarriorMoveBehavior`와 `FighterMoveBehavior`에만 정의되어 있다. 나중에 수정할 일이 생기면 해당 클래스에서만 수정하면 된다. 어쌔신파이터의 경우 모드를 스위칭할 수 있는데, 이 때 어쌔신의 움직임과 공격을 똑같이 수행한다. 이 행동은 어쌔신에서도 똑같이 사용되지만, 그렇다고 같은 코드가 여러 곳에 붙여넣어져 있지는 않다. 게다가 동작을 전환하는 건 단순히 전략을 바꿔주는 것만으로 해결되어 이해하기에도 쉽다.



## 정리

전략 패턴의 장점과 주의사항은 아래와 같이 정리할 수 있을 것 같다.

- 장점
  - 코드의 중복을 줄일 수 있다.
    - 여러 곳에서 동일한 동작을 할 때 하나의 전략 클래스에서만 알고리즘을 정의하면 된다. 상속에 비해서 관리도 용이하다.
  - 확장에 유리하다.
    - 메소드를 수정하는 게 아닌, 새로운 전략을 추가하는 방식으로 프로그램을 확장시킬 수 있다.
  - 행동을 동적으로 변경할 수 있다.
    - 한 인스턴스가 케이스에 따라 다른 행동을 해야할 때 전략을 바꾸는 방식으로 대응할 수 있다.
- 주의사항
  - 항상 유용한 것은 아니다.
    - 모든 패턴이 다 마찬가지다. 구현해야 될 내용이 단순한 경우 분기처리하는 것이 더 이해도 쉽고 빠를 수 있다. 하지만 프로그램이 커질수록 유용해질 가능성이 높아보인다.
  - 각 전략의 알고리즘을 이해하고 있어야 한다.
    - 전략 패턴이 아니라도 당연한 내용이긴 하다. 어떤 전략을 어디에 어떻게 사용할지를 판단하는 건 결국 개발자의 몫이다. 전사에게 마법 공격 전략을 사용하지 않게 하려면 그만큼 방어 로직의 추가나 알고리즘의 이해 같은 높은 관심이 필요하다.



