# [Spring] 스프링 컨트롤러 로그 남기기



## [1]
우리 팀은 업무 특성상 들어오는 모든 요청과 응답 인터페이스 내용을 DB에 저장하도록 되어있다. 인터페이스 테이블은 `인터페이스 ID`, `요청 전문`, `응답 전문`, 그리고 그 외 `HTTP 헤더 정보` 중 필요한 것들을 저장한다. 혹시 모를 유실을 방지하기 위해 일단 들어오자마자 요청 정보를 인서트 하고, 모든 처리가 완료된 후 클라이언트로 주는 응답 결과를 업데이트 하는 방식이다.

처음 개발할 때는 컨트롤러에서 인터페이스 인서트/업데이트를 구현했다. 대략 아래와 같은 로직이었다.

```java
@Controller
public class Controller {

    public ApiWrapper order(String orderNo, OrderRequest orderRequest, HttpServletRequest httpRequest) {
        try {
            //요청 정보 기반으로 Interface 엔티티 생성하여 저장
            Interface interface = repository.save(Interface.of(orderNo, orderRequest, httpRequest));
            
            // 주문번호로 유효성 검증 후 주문 데이터 생성
            Order order = saveValidOrder(orderNo, orderRequest);
            order.init(interface.getInterfaceId());

            // 주문상품별 연동 로직 수행
            Result result = service.order(order);

            // Interface에 응답 전문 업데이트 -> 위에서 예외가 던져지면 수행되지 않는다.
            interface.updateResult(result);
            repositroy.save(interface);

            // 서비스 수행 결과 반환
            return new ApiWrapper<>(result);
        } catch (Exception e) {
            // 예외 발생시 로그 출력 후 예외 기반 응답 결과 반환
            log.error(e.getMessage(), e);
            return new ApiWrapper<>(Result(e));
        }
    }
}
```

지금 생각하면 너무 초보적인 실수다. 서비스단에서 예외가 던져지면 인터페이스 결과 업데이트는 수행되지 않는다. 오픈 직후 문제를 알았지만 다행히도 운영상 문제될 점은 전혀 없었다. 이슈가 생길 경우 인터페이스 데이터보다는 도메인 데이터를 먼저 보고, 대부분 여기서 해결이 됐기 때문이다. 보다 중요하고 바쁜 일들이 넘쳐나서 개선은 미뤄졌다.

## [2]
그리고 최근 API를 V2로 업그레이드 하게 되었다. 그 동안 쌓인 기술부채들을 개선할 기회였다. 인터페이스 저장 관련해서 가장 큰 문제점은 핸들링하지 못하는 얘외가 발생할 경우, 클라이언트한테는 제대로 응답이 갔음에도 DB에는 남지 않았다는 점이었다. 가장 간단한 해결책은 아래처럼 `finally`를 추가하는 것이었다.

```java
@Controller
public class Controller {

    public ApiWrapper order(String orderNo, OrderRequest orderRequest, HttpServletRequest httpRequest) {
        Interface interface = null;
        Result result = null;
        try {
            //요청 정보 기반으로 Interface 엔티티 생성하여 저장
            interface = repository.save(Interface.of(orderNo, orderRequest, httpRequest));
            
            // 주문번호로 유효성 검증 후 주문 데이터 생성
            Order order = saveValidOrder(orderNo, orderRequest);
            order.init(interface.getInterfaceId());

            // 주문상품별 연동 로직 수행
            result = service.order(order)
        } catch (Exception e) {
            // 예외 발생시 로그 출력 후 예외 기반 응답 결과 생성
            log.error(e.getMessage(), e);
            result = Result(e);
        } finally {
            // Interface에 응답 전문 업데이트
            interface.updateResult(result);
            repositroy.save(interface);
        }

        // 결과 반환
        return new ApiWrapper<>(result);
    }
}
```

이렇게 할 경우 예외가 발생해도 `catch`에서 `Result`를 생성한다. 그리고 `finally`에서 `Result`를 인터페이스에 업데이트 할 수 있다. 크게 나쁘다고 할 수는 없을 것 같다. 하지만 좋은 구조는 아니라고 생각했다. 이유는 다음과 같다.

1. **인터페이스 저장은 메소드의 주요 관심사가 아니다.**
컨트롤러 메소드의 목적은 요청을 받아서 검증하고, 서비스 로직을 수행하는 것이다. 특히 DB와 관련해선 주문번호를 검증해서 신규 주문 데이터를 생성하는 역할이 있다. 인터페이스 관련 처리는, 물론 할 수도 있지만, 가능하면 한 가지 DB 작업에 집중했으면 했다. 그러자면 주문 관련 작업이 주 기능이고 요청/응답 관련 작업은 부가 기능임이 명백했다.

2. **코드의 중복**
예제 코드에선 `order()` 메소드만 있지만, 같은 구조로 `cancel()`, `resend()`, `status()` 라는 메소드가 더 있다. 그리고 앞으로 'exchange()', 'extend()' 같은 메소드까지 추가될 수 있다. 사실 컨트롤러에서는 구조가 거의 같다. 인터페이스를 저장하고, 주문번호로 유효성을 검증해서 주문을 생성하거나 기존 주문을 가져오고, 서비스 로직을 호출한다. 위처럼 코드를 짤 경우 변경 사항이 생길 때마다 모든 메소드를 똑같이 작업해줘야 한다. 예를 들어 예외 발생시 `Sentry`로 추적하기로 했다고 해보자. 4개, 어쩌면 6개의 메소드의 `catch` 안에 `Sentry.captureException(e)` 코드를 추가해줘야 한다.


## [3]
두 문제를 해결하기 위해 스프링 AOP를 사용하는 것이 좋겠다고 생각했다. 아래와 같이 소스코드를 개선했다.

```java
@Controller
public class Controller {

    @ExceptionHandler
    public ApiWrapper handleException(Exception e) {
        log.error(e.getMessage(), e);
        return new ApiWrapper<>(Result(e));
    }

    public ApiWrapper order(String orderNo, OrderRequest orderRequest, HttpServletRequest httpRequest) {
        // 주문번호로 유효성 검증 후 주문 데이터 생성
        Order order = saveValidOrder(orderNo, orderRequest);
        order.init((String) httpRequest.getAttribute("interfaceId"));

        // 주문상품별 연동 로직 수행
        Result result = service.order(order);

        return new ApiWrapper<>(result);
    }
}

@Aspect
@Component
public class Aspect {

    @Around(value = "execution(orderPointCut()) && args(orderNo, orderRequest, httpRequest)")
    public Object aroundOrder(ProceedingJoinPoint pjp, String orderNo, OrderRequest orderRequest, HttpServletRequest httpRequest) throws Throwable {
        //요청 정보 기반으로 Interface 엔티티 생성하여 저장
        Interface interface = repository.save(Interface.of(orderNo, orderRequest, httpRequest));

        // 주문 로직에 필요한 인터페이스 ID를 컨트롤러에 전달
        httpRequest.setAttribute("interfaceId", interface.getInterfaceId());

        // 실제 컨트롤러 메소드 수행
        Object proceed = pjp.proceed();

        // Interface에 응답 전문 업데이트
        interface.updateResult((Result) proceed);
        repository.save(interface);

        return proceed;
    }
}
```

스프링 AOP로 간편하게 컨트롤러 전/후 처리를 조작할 수 있었다. **주 기능과 부가 기능의 분리 목적은 달성했다.** 비즈니스 로직과 연관된 주문 처리는 `Controller.order()`에서, 인터페이스 관련 처리는 `Aspect.aroundOrder()`에서 각각 나눠서 담당하게 됐다. 하는 김에 `@ExceptionHandler`로 예외 처리 로직도 공통화 했다. 4개 메소드에서 `try ~ catch ~ finally`가 사라지며 컨트롤러 로직이 굉장히 간소화 되었다. 중복의 문제도 해결된걸까?

아쉽게도, **컨트롤러에서 중복의 문제는 사라졌지만 AOP로 이관되었을 뿐이다.** 위 `Aspect` 코드를 보면 `aroundOrder()`에서 `order()` 메소드의 인자를 받고 있다. 안타깝게도 다른 메소드들은 다른 인자를 받는다. 인자는 `ProceedingJoinPoint`로 통일하고, 여기서 메소드 인자를 받아올까 고민도 했다. 이럴 경우 가독성도 해칠 뿐더러 단일 책임의 원칙도 위배되고, 나중에 수정도 더 어려울 게 명백했다. 인자를 다르게 받는 `aroundCance()`, `aroundResend()`, `aroundStatus()`로 분리하고 같은 로직을 처리하는 편이 낫다고 판단했다. 중복이 존재하지만, 수정이 잦은 코드는 아니니 어느 정도 타협점이 될 수 있다고 생각했기 때문이다.


## [4]

컨트롤러 V2를 만드려니 문제가 생겼다. 기존 API는 수행 결과를 `ApiWrapper` 클래스로 한 번 감싸서 보내는 구조였다. `ApiWrapper`에는 `data`와 `error` 필드가 있다. `data` 필드에는 정상 수행이나 핸들링한 예외 발생의 처리 결과가 들어간다. 핸들링 하지 못한 예외 발생시에는 `error` 필드에 예외 정보를 넣는다. 그 때는 그게 좋다고 생각했다.

막상 운영을 해보니 API 사용하기에 너무 불편했고, 기대한만큼의 효용도 없었다. 현재는 `Sentry`도 적용한만큼 V2에서는 `ApiWrapper`를 없애기로 했다. V2 컨트롤러를 새로 만들었다. 근데 주문의 경우 V1과 V2가 명백히 큰 차이가 있었지만, 나머지 기능들은 Wrapper 유무 차이였고 로직은 똑같이 돌아야 했다. 그러기 위해서 V1과 V2 코드를 똑같이 유지시켜야 했다. 차라리 V1 컨트롤러가 V2 컨트롤러를 의존하고, 대응하는 메소드를 그대로 호출하는 게 낫겠다 생각했다.

```java
@Controller
public class Controller {

    private final ControllerV2 controllerV2;

    @ExceptionHandler
    public ApiWrapper handleException(Exception e) {
        log.error(e.getMessage(), e);
        return new ApiWrapper<>(Result(e));
    }

    public ApiWrapper cancel(String orderNo, CancelRequest cancelRequest, HttpServletRequest httpRequest) {
        return controllerV2.cancel(orderNo, cancelRequest, httpRequest);
    }
}

public class ControllerV2 {

    @ExceptionHandler
    public Result handleException(Exception e) {
        log.error(e.getMessage(), e);
        return Result(e);
    }

    public ApiWrapper cancel(String orderNo, CancelRequest cancelRequest, HttpServletRequest httpRequest) {
        // 주문번호로 유효성 검증 후 주문 데이터 생성
        Order order = saveIfClaimValid(orderNo, cancelRequest);
        order.init((String) httpRequest.getAttribute("interfaceId"));

        // 주문상품별 연동 로직 수행
        return service.cancel(order);
    }
}
```

컨트롤러에서 컨트롤러를 호출하는 게 좋은가, 는 아직 답은 못 내렸다. 어쩌면 리다이렉트가 나을 수도 있겠다. 다만 구글링을 좀 해본 결과 그렇게 하면 안 된다는 얘기도 찾지 못했다. 우선은 해보았다.

근데 이 경우는 또 문제가 있었다.

1. **V1 호출시 인터페이스가 두 개가 남는다.**
스프링 AOP는 프록시를 사용한다. V1 컨트롤러의 프록시에서 일단 인터페이스를 저장하고, 실제 컨트롤러에서 V2 컨트롤러를 호출한다. 근데 그 V2 컨트롤러도 사실 프록시다. 그럼 프록시가 또 인터페이스를 저장해서 결국 한 요청에서 인터페이스 두 개가 저장된다. 대략 아래 그림과 같은 구조다. 이건 내가 원하던 게 아니다.


그림그림

## [5]
일단 1번 문제만 해결한다고 할 때, 간단결 방법이 있긴 했다. `Aspect`도 V1과 V2로 분리한다. 그리고 V2 `Aspect`에서는 `HttpServletRequest`에서 `URI`를 가져와, `v2` 키워드를 포함하고 있지 않으면 바로 `proceed()` 하는 것이다. V1에서 호출한 거라면, V2 프록시는 인터페이스 저장을 하지 않고 바로 로직을 수행할 것이다.

```java

```

당연히 많은 문제가 생긴다. 일단 V3, V4가 추가될 때는 안 좋은 영향이 있을 거라는 건 생각 안 해봐도 느낌이 팍 온다. 그리고 아까 `aroundOrder()`, `aroundCancel()`, `aroundResend()`, `aroundStatus()`의 중복도 겨우 타협했었는데, 이젠 `aroundOrderV2()`, `aroundCancelV2()`, `aroundResendV2()`, `aroundStatusV2()`까지 필요하다. 8개 메소드의 로직은 거의 중복이다.

## [6]
결국 AOP를 버리고 그보다 앞단으로 가기로 했다. `Interceptor`를 구현했다. 사실 `Config` 추가하고 하는 게 귀찮아서 AOP로 해보려고 했는데 돌아왔다. 그래도 무의미한 건 아니었던 게, 프록시에 대해 다시 한 번 생각해볼 수 있었다.