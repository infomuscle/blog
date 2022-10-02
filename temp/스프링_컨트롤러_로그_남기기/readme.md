# [Spring] 스프링 컨트롤러 로그 남기기



우리 팀은 업무 특성상 들어오는 모든 요청과 응답 인터페이스 내용을 DB에 저장하도록 되어있다. 인터페이스 테이블은 인터페이스 ID, 요청 전문, 응답 전문, 그리고 기타 HTTP 헤더에서 읽을 수 있는 정보 중 필요한 것들을 저장한다. 유실을 방지하기 위해 일단 들어오자마자 요청 정보를 인서트 하고, 모든 처리가 완료된 후 클라이언트로 주는 응답 결과를 업데이트 하는 방식이다.

처음 개발할 때는 컨트롤러에서 인터페이스 인서트/업데이트를 맡았었다.

대략 아래와 같은 로직이었다.

```java
public ApiWrapper<Result> order(String orderNo, OrderRequest orderRequest, HttpServletRequest httpRequest) {
        try {
            //요청 정보 기반으로 Interface 엔티티 생성하여 저장
            Interface interface = repository.save(Interface.of(orderNo, orderRequest, httpRequest));
            
            // 주문번호로 유효성 검증 후 주문 데이터 생성
            Order order = saveValidOrder(orderNo, orderRequest);
            order.init(interface.getInterfaceId());

            // 주문상품별 연동 로직 수행
            Result result = service.order(order);

            // Interface에 응답 전문 업데이트
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
```

지금 생각하면 너무 초보적인 실수다. 서비스단에서 예외가 던져지면 인터페이스 결과 업데이트는 수행되지 않는다.
오픈 직후 문제를 알았지만 다행히도 운영상 문제될 점은 전혀 없었고, 보다 중요하고 바쁜 일들이 넘쳐나서 개선은 미뤄졌다.

그리고 최근 주문 API을 V2로 업그레이드 하게 되어, 개선할 기회다 싶었다. 가장 큰 문제점은 응답이 갔음에도 DB에는 기록이 남지 않았다는 점. 가장 간단한 해결책은 finally를 추가하는 것이었을 것이다. 그럼 아래와 같은 코드가 되겠지.

```java
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

이렇게 할 경우 예외가 발생해도 catch에서 Result를 생성해주고, finally에서 인터페이스 업데이트를 할 수 있다. 크게 나쁘다고 할 수는 없을 것 같다. 하지만 좋은 구조는 아니라고 생각했다.

1. 인터페이스 저장은 메소드의 주요 관심사가 아니다.
첫번째로 인터페이스 로직은 위 메소드에서 주요 관심사가 아니다. 컨트롤러의 order() 메소드의 주요 로직은 주문 데이터 생성 및 주문상품별 처리를 해주는 서비스 로직 수행이 주 목적이다.

2. 코드의 중복(주문 말고 같은 구조의 API가 3개 더 있다)
두번째로는 예제 코드에선 주문 메소드만 있지만, 같은 구조로 취소 등 다른 메소드가 3개 더 있다. 그리고 앞으로 더 늘어날 수 있다. 저기서 주문에 해당하는 Order 키워드만 다른 단어로 바꾸면될 정도로 똑같은 구존데, 변경 사항이 생길 때마다 모든 메소드를 똑같이 작업해줘야 한다.


일단 1번 문제를 해결하기 위해, 스프링 AOP를 사용하는 것이 좋겠다고 생각했다.

```java
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

// 메소드 전/후로 인터페이스 인서트/업데이트 처리
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

스프링 AOP로 간편하게 컨트롤러 전/후 처리를 조작할 수 있었다. 덕분에 실제 비즈니스 로직과 연관된 주문 처리는 Controller.order()에서, 인터페이스 관련 처리는 Aspect.aroundOrder()에서 각각 나눠서 담당하게 됐다. 하는 김에 @ExceptionHandler로 예외 처리 로직도 공통화 했다. 컨트롤러 로직은 굉장히 간소화 되었다. 여전히 문제가 있었다.

위 Aspect 코드를 보면 aroundOrder()에서 order() 메소드의 인자를 받고 있다. 근데 다른 메소드들은 인자를 또 다르게 받는다. 공통화 하기에는 코드가 명료한 느낌이 아니었다. 메소드별로 나누자니, 중복이 생긴다. 그래도 수정이 잦을 코드는 아니니 이 상태로 마무리할까 했다.

컨트롤러 V2를 만드려니 문제가 생겼다. 기존 API는 수행 결과를 ApiWrapper 클래스로 한 번 감싸서 보내는 구조였다. ApiWrapper는 data와 error 필드가 있는데, 정상 처리나 핸들링한 예외 발생시에는 data 필드에 결과를 넣고, 핸들링 하지 못한 예외 발생시에는 error 필드에 예외 정보를 넣어 응답을 주려고 했다. 그 때는 그게 좋다고 생각했다.

막상 운영을 해보니 API 사용하기에 너무 불편했고, 별로 효용성도 없었다. 그래서 V2에서는 없애기로 했다. V2 컨트롤러를 새로 만들었다. 근데 V1과 V2의 차이는 결과의 Wrapper 유무 차이만 있었고, 로직은 똑같이 돌아야 했다. 그걸 위해서 V1과 V2 코드를 똑같이 유지시켜야 하는데, 그럴 바에 V1 컨트롤러가 V2 컨트롤러를 의존하고, 그대로 호출하는 게 낫겠다 생각했다.

```java
public class Controller {
    @ExceptionHandler
    public ApiWrapper handleException(Exception e) {
        log.error(e.getMessage(), e);
        return new ApiWrapper<>(Result(e));
    }

    private final ControllerV2 controllerV2;

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