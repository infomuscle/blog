# [Spring] 스프링 의존관계 주입 방법

스프링의 가장 큰 특징이라고 할 수 있는 것이 제어의 역전입니다.

이 제어의 역전은 프레임워크와 라이브러리를 구분하는 기준이 되기도 하는데요.

스프링 프레임워크는 객체의 생성과 세팅 등을 개발자가 직접 관리하지 않고,

스프링에서 관리하도록 설계되어 있습니다.

여기서 스프링이 관리하는 객체를 스프링 빈, 빈을 관리하는 주체를 DI 컨테이너라고 부릅니다.

한 스프링 빈에서 다른 스프링 빈을 사용할 때,

즉 한 스프링 빈이 다른 스프링 빈이 의존할 때 의존하는 객체를 스프링 DI 컨테이너에서 알아서 넣어줍니다.

이것을 **의존성 주입**이라고 부릅니다.

의존성 주입 방법에는 대표적으로 3가지가 있습니다.

<br>

## 주입 방법

### 1. 생성자 주입(Constructor)

```java
@Service
public class AccountService {

    private final AccountRepository accountRepository;
    private final MemberService memberService;

    public AccountService(AccountRepository accountRepository, MemberService memberService) {
        this.accountRepository = accountRepository;
        this.memberService = memberService;
    }
}
```

생성자를 이용해서 주입하는 방법입니다.

의존성을 주입 받을 빈을 파라미터로 지정하면 DI 컨테이너에서 해당 빈을 주입합니다.

생성자에 있기 때문에 **빈이 만들어질 때 딱 한번만 호출**됩니다.

**불변적이고 필수적인 의존관계일 때 사용**합니다.

만약 **생성자가 2개 이상이라면 @Autowired를 지정**해줘야 합니다.

이 방법을 뒤에 나올 두 방법에 비해 권장하는데요.

자세한 이유는 후술하겠습니다.

<br>

### 2. 필드 주입(@Autowired)

```java
@Service
public class AccountService {

    @Autowired
    private AccountRepository accountRepository;
    
    @Autowired
    private MemberService memberService;

}
```

@Autowired 어노테이션을 사용하는 방법입니다.

필드에 어노테이션만 달아주면 DI 컨테이너에서 의존성을 주입해줍니다.

3가지 방법 중 가장 간결해보입니다.

<br>

### 3. 수정자 주입(Setter)

```java
@Service
public class AccountService {

    private AccountRepository accountRepository;
    private MemberService memberService;

    @Autowired
    public void setAccountRepository(AccountRepository accountRepository) {
        this.accountRepository = accountRepository;
    }
  
    @Autowired
    public void setMemberService memberService(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

Setter를 이용해도 의존성을 주입할 수 있습니다.

선택적이고, 변경 가능성이 있는 의존관계일 때 사용합니다.

만약 선택적인 의존관계일 경우, 주입 대상이 없으면 오류가 발생하기 때문에 @Autowired의 required 옵션을 false로 지정해줘야 합니다.

런타임에서도 의존성을 주입하거나 변경할 수 있다는 특징이 있습니다.

<br>

이 외에도 일반 메소드로 의존성을 주입할 수도 있습니다.

사용 빈도도 낮고 중요성도 떨어져서 생략하겠습니다.

실질적으로는 대부분 생성자 주입 방식을 사용하고, 또 권장합니다.

## 생성자 주입을 권장하는 이유

일반적으로 의존관계 설정시 생성자 주입 방법을 권장합니다.

그 이유는 다음과 같습니다.

<br>

### 1. 어플리케이션 구동 시점에서 오류 확인 가능

스프링의 **의존성 주입은 생성자 -> 필드 -> 수정자 순서**로 실행됩니다.

즉 수정자 주입과 필드 주입은 객체를 우선 생성한 후 의존성을 주입하는데요.

이 때문에 **순환참조를 미리 파악할 수 있다**는 장점이 있습니다.

<br>

```java
@Service
public class AService {

    private final BService bService;

    public AService(BService bService) {
        this.bService = bService;
    }
}

@Service
public class BService {

    private final AService aService;

    public BService(AService aService) {
        this.aService = aService;
    }
}
```

예를 들어 위 코드처럼 A Service와 B Service가 서로를 의존하고 있다고 해보겠습니다.

이 때 각각의 객체를 우선 생성한 후, A에 B를, B에 A를 넣어주는 것은 문제가 없습니다.

하지만 생성자에서 넣어주면 어떻게 될까요?

- A를 생성하려는데 B가 필요하네, B를 먼저 생성해야겠군
- B를 생성하려니 A가 필요하네, A를 먼저 생성해야겠군
- A를 생성하려니 B가...

이런 식으로 무한 반복에 빠져버리고 애플리케이션이 구동되지 못합니다.

물론 순환참조 그 자체는 문제가 되지 않을 수 있습니다.

하지마 만약 순환 참조로 인해 순환 호출이 발생한다면 어떻게 될까요?

<br>

```java
@Service
public class AService {

    private final BService bService;

    public AService(BService bService) {
        this.bService = bService;
    }
  
    public void execute() {
        bService.execute();
    }
}

@Service
public class BService {

    private final AService aService;

    public BService(AService aService) {
        this.aService = aService;
    }
  
    public void execute() {
        aService.execute();
    }
}
```

- a.execute()를 호출해야지
- a.execute() 안에 b.execute()가 있네, b.execute()를 호출해야지
- b.execute() 안에 a.execute()가 있네, a.execute()를 호출해야지
- a.execute() 안에..

이런 식으로 메소드의 무한 호출이 일어나고 스택오버플로우가 발생합니다.

즉 순환참조는 안전하지 못한 설계입니다.

**생성자 주입 방식은 어플리케이션을 구동하며 객체 생성과 동시에 의존성을 주입**하기 때문에

순환참조가 발생한다면 스프링이 뜨지 않고 중단되고, 설계 미스를 미리 파악할 수 있다는 장점이 있습니다.

<br>

### 2. 불변성 보장

일반적으로 **의존관계는 어플리케이션 실행 중이 바뀔 일이 없고, 오히려 불변성을 가지는 것이 안전**합니다.

생성자든 Setter든 DI 컨테이너에서 사용하기 위해선 public으로 선언되어야 합니다.

이렇게 **Setter가 열려있다면 누군가가 실수로, 혹은 고의로 의존관계를 수정**할 수 있습니다.

이는 매우 불안한 설계입니다.

생성자 주입은 **어플리케이션 구동 시점에 딱 한번만 호출되기 때문에 안전한 방식**이라고 할 수 있습니다.

<br>

### 3. 스프링 프레임워크에 비의존적

생성자가 1개만 있을 경우 @Autowired를 생략할 수 있습니다.

**@Autowired는 스프링에서 제공**하는 어노테이션입니다.

**특정 프레임워크에 의존적인 코드는 지양**하는 편이 좋습니다.

<br>

### 4. 테스트 코드 작성 편의

앞서 2번 이유에서 이어집니다.

필드 주입은 매우 간결하다는 특징이 있지만, 스프링 DI 컨테이너가 없으면 의존성을 주입할 수 없습니다.

즉 테스트 코드를 작성, 실행하려면 반드시 스프링을 띄워야 합니다.

반면 생성자 주입 방식은 직접 의존 객체를 생성하여 **스프링 비의존적인 테스트 코드를 작성**할 수 있습니다.

<br>

### 5. Lombok과 결합

필드 주입의 간결함은 놓치고 싶지 않은 장점입니다.

하지만 Lombok을 활용하면 생성자 주입에서도 같은 수준의 간결함을 얻을 수 있습니다.

<br>

```java
@Service
@RequiredArgsConstructor
public class AccountService {

    private final AccountRepository accountRepository;
    private final MemberService memberService;

}
```

위 코드처럼, **@RequiredArgsConstructor**를 사용하면

불변 멤버변수를 파라미터로 받는 생성자를 Lombok에서 만들어줍니다.

필드 주입과 비교해서 어떤 것이 더 간결하다 말하기 어려운 수준입니다.

반면 앞서 언급한 생성자 주입의 장점은 모두 취할 수 있습니다.

