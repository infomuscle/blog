# [Spring] Feign으로 MultiPart 요청 보내기



*예시 코드는 단순화된 코드입니다.*



## 기

시작은 회사 공통 프레임워크의 버전 업그레이드였다. pom.xml에서 1.0.x를 1.0.y로 바꿔주기만 하면 될 줄 알았다. 그런데 메이븐 업데이트를 하고 나니 스프링이 안 뜬다.



```
requestpart.value() was empty on parameter 0
```

로그를 살펴보니 특정 업체 `G`랑 통신하는 FeignClient 객체를 생성하는 과정에서 오류가 발생했고, 위 같은 에러 메시지를 확인했다. 구글링을 해보니 정확히 일치하는 케이스가 없다. 



```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  String order(@RequestPart Map request);
}
```

해당 클래스를 열어보니, `@RequestPart` 어노테이션에서 `value` 파라미터에 값이 없어서 발생하는 걸로 보였다. 원래는 없어도 됐는데? 그래도 비어서 안 된다니 일단 임의로 값을 넣어보았다. 



```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  String order(@RequestPart(value="request") Map request);
}
```
스프링은 잘 떴다!




## 승

근데 테스트 코드 돌려보니깐 실패했다. `400` 응답을 주고 있었다. `value`를 빼면 또 스프링이 안 떴다. 근본 문제가 뭔지 확인이 필요했다. 디펜던시 확인으로 돌아갔다. 외부 라이브러리 목록에서 feign 관련 의존성을 확인해봤다.  `io.github.openfeign` 관련 패키지들을 보니 이전에는 10.2.3 버전이었고, 업데이트 후에는 10.12였다. 이렇게도 저렇게도 해보고 최신 버전인 11.8까지 올려봤다. 안 됐다.



로그를 다시 꼼꼼하게 살펴보니 `RequestPartParamProcessor` 클래스가 눈에 띄었다. 이걸 열어보니 `value`의 유무를 체크하고 있었다. 이 친구는  `spring-cloud-openfeign:2.2.9` 소속이었다. 이전 버전은 2.1대였는데, 거기선 어떻게 되어있던거지 확인이 필요했다.



어떻게 돼있는 게 아니라 그냥 없었다. 2.2.2부터 추가된 클래스다. `spring-cloud-openfeign`을 2.2.1로 맞춰주니 일단 `value`가 없어도 잘 돌아간다. 근데 다른 `spring-cloud` 디펜던시 친구들은 2.2.9인데 얘만 이 버전으로 둬도 될까? 데브옵스팀에 물어볼까 고민하다가 일단은 2.2.9에서 문제를 해결해보기로 했다. 어차피 나중에 또 업데이트가 될텐데, 언젠간 2.2.2 이상으로 맞춰야 하지 않겠나.



```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  String order(@RequestPart Map request);
}
```

다시 기존 소스를 살펴보면 실제 호출시에 파라미터에는 `LinkedMultiValueMap`이 들어간다. DTO를 `LinkedMultiValueMap`으로 변환하고 파라미터에 넣으면 Feign에서 알아서 `multipart/form-data`에 맞게 HTTP Body를 생성해줬다. 근데 그게 2.2.2부터는 안 되는 상황. Feign 로그 레벨을 Full로 해봤다. 2.2.1까지는 제대로 Body가 생성된 게 로그에 찍히는데 새 버전에서는 Binary Data라고 퉁친다. FallbackFactory 생성해서 파라미터 찍어보기는 좀 귀찮아서 이전에는 `@RequestPart` 어노테이션을 붙이면 Map의 키마다 HTTP 메시지를 생성해줬는데, 파라미터 하나당 메시지 한 개로 스펙이 바뀐건 아닌가 추론해봤다. 다른 방법이 있을 것 같아서 열심히 구글링 해봤는데 거의 `MultiPartFile`을 파라미터로 하지, Map을 파라미터로 하는 경우는 잘 없었다.



#### GFeignClient

```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  String order(RequestDto request);
}
```

#### RequestDto

```java
public class GRequestDto extends RequestDto {
	@FormProperty("field_one")
	private String fieldOne;
	
  @FormProperty("field_two")
	private String fieldTwo;
}
```

스프링 공식 문서랑 스택오버플로우를 열심히 뒤져보다가 방법을 찾았다. 따로 어노테이션은 필요없고, consumes를 `multipart/form-data`로 하고 오브젝트를 그대로 집어넣으면 된다. 그리고 오브젝트 필드에 `@FormProperty`로 name을 세팅해주면 된다. 테스트 돌려보니 성공. 버전 업그레이드 완료~



## 전0

일 수도 있겠지만 돌아만 간다고 끝내기엔 아쉬웠다.



```java
public interface MultiPartFeignClient {
    String order(Map request);
    String cancel(Map request);
    String resend(Map request);
    String status(Map request);
}
```

일단 `GFeignClient`를 보면 `MultiPartFeignClient` 인터페이스를 상속하고 있다. 원래 위처럼 생겼었다. 새롭게 찾은 방법을 적용하고 나서는 아래와 같이 바뀌었다.



```java
public interface MultiPartFeignClient {
    String order(RequestDto request);
    String cancel(RequestDto request);
    String resend(RequestDto request);
    String status(Map request);
}
```

4개 메소드 중에 3개만 Dto를 파라미터로 받는다. 왜일까? 업체 스펙이 `POST` 요청할 때만 `multipart/form-data`로 하라고 했기 때문이다. `status()`는 `GET`에다가 쿼리 파라미터로 요청한다. 이전에 전부 `Map`이 었던 건 `@RequestPart` 어노테이션을 붙이면 `multipart/form-data`로 해주고, `@SpringQueryMap` 어노테이션을 붙이면 쿼리 파라미터를 만들어줬다. 근데 DTO에다가 `@SpringQueryMap`을 붙이면 쿼리 파라미터를 만들어주긴 하는데, 네이밍이 자바 기본 컨벤션인 카멜 케이스로 해준다. 나는 스네이크 케이스로 해야하는데, 아무리 찾아도 방법이 안 나올 뿐더러 '방법이 없다'는 답변까지 확인했다. 일단 당장 `MultiPartFeignClient`를 상속하는 건 G 업체 밖에 없기 때문에 문제는 안 되지만, 그렇다고 이대로 둘 순 없다. 뭘로든 통일해야 확장성을 지킬 수 있다. 



근데 왜 굳이 인터페이스를 또 상속했을까? 내가 담당하는 업체는 15개 업체다. 우리 쪽 DB 동일한데, 같은 데이터를 각각 15개 업체가 요구하는 포맷으로 세팅하고 응답도 각각 파싱해서 다시 같은 데이터로 맞춘다. `FeignClient`도 A 업체부터 O 업체까지 `AFeignClient`부터 `OFeignClient`까지 있다. 처음에는 메인 서비스 로직에서 각 업체별 `FeignClient`를 호출했는데, 고도화를 하면서 핸들러 어댑터 패턴을 적용하게 됐다. 구조는 다음과 같다. 



#### FeignService

```java
@Service
public class FeignService {

    @Autowired
    private ApplicationContext applicationContext;

    private Map<Type, Object> map = new HashMap<Type, Object>();

    private List<FeignHandlerAdapter> adapters;

    @PostConstruct
    private void initFactory() {
        map.put(A, applicationContext.getBean(AFeignClient.class));
     		map.put(B, applicationContext.getBean(BFeignClient.class));
      	...
        map.put(O, applicationContext.getBean(OFeignClient.class));

        adapters = new ArrayList<>(applicationContext.getBeansOfType(FeignHandlerAdapter.class).values());
    }

    public String order(RequestDto request) {
        Object handler = map.get(request.getType());

        for (FeignHandlerAdapter adapter : adapters) {
            if (adapter.supports(handler)) {
                return adapter.order(handler, request);
            }
        }

        throw new Exception("오류");
    }
}
```



#### HandlerAdapter 구현체

```java
@Component
public class JsonFeiginHandlerAdpater implements FeignHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof JsonFeignClient);
    }

    @Override
    public String order(Object handler, RequestDto request) {
        return ((JsonFeignClient) handler).order(request.toJsonString());
    }
}

@Component
public class FormFeignHandlerAdapter implements FeignHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof FormFeignClient);
    }

    @Override
    public String order(Object handler, RequestDto request) {
        return ((FormFeignClient) handler).order(request.toQueryParams());
    }
}
```

우선 서비스 로직에서 업체랑 통신할 때는 업체의 `FeignClient`를 직접 호출하지 않는다. 대신 `FeignService`의 메소드를 호출한다. `FeignService`는 스프링이 뜰 때 각 업체를 키로 업체별 `FeignClient`를 가리키는 `Map`을 생성한다. 또 `FeignHandlerAdapter` 인터페이스를 구현하는 모든 객체를 저장하는 리스트를 생성한다. 메소드가 호출되면, 요청 데이터의 업체 타입 값으로 `FeignClient(handler)`를 가져온다. 그리고 리스트에서 각 `HandlerAdapter`의 구현체가 해당 `handler`를 지원하는지 체크하고, 지원하면 `HandlerAdapter` 구현체에서 `handler`의 메소드를 호출한다.  JSON 포맷 문자열로 바꾸든, Form 포맷 문자열로 바꾸든 공통 데이터의 처리는 `HandlerAdapter` 구현체에서 이루어진다. `FeignClient(handler)` 호출까지 하고 결과를 반환한다.





## 전

근데 솔직히 이거 좀 오버엔지니어링 같다. 핸들러 어댑터 패턴을 쓰기에 적합한 것 같긴 한데, 꼭 안 써도 될 상황이긴 했다. 왜냐면 Json이든 Form이든 파라미터는 다 String으로 받아서 HTTP Body에 그대로 넣는다. 근데 MultiPart는 DTO를 그대로 받는다. 그럼 feign에서 알아서 MultiPart로 바꿔서 HTTP 메시지에 박아준다. 이 상태에선 핸들러 어댑터 괜찮긴 하다. 근데 MultiPartFeignClient도 각 메소드에서 String을 받도록 해두면 불필요한 클래스들을 좀 줄일 수 있을 것 같았다.



어쨋든 이거도 저거도 다 String으로 받는데, MultiPart도 String으로 받으면 안 되나 생각했다. 일단 `MultiPartFeignClient` 인터페이스에서 다 String으로 선언해두면, `GFeignClient`에서 업체 요구사항대로 `POST`는 `multipart/form-data`로, `GET`은 쿼리 파라미터로 요청할 수 있도록 어노테이션을 써서 유연하게 구현할 수 있었다. 나중에 쿼리 파라미터를 n개 받는 API가 있는 업체가 추가되면 얘기가 다르지만, 일단은 이 정도 확장성까지만 확보해도 충분할 것으로 결론지었다. 그 정도로 유니크한 업체라면 그냥 업체 전용 인터페이스를 만들거나 해야지.



근데 의외로 자바 오브젝트를 `multipart/form-data` 포맷 문자열로 바꿔주는 레퍼런스를 찾기가 어려웠다. 직접 구현하는 수 밖에 없었다.
