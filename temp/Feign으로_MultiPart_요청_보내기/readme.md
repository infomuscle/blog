# [Spring] Feign으로 MultiPart 요청 보내기



*예시 코드는 단순화된 코드입니다.*



## 기

시작은 회사 공통 프레임워크의 버전 업그레이드였다. pom.xml에서 1.0.x를 1.0.y로 바꿔주기만 하면 될 줄 알았다. 그런데 메이븐 업데이트를 하고 나니 스프링이 안 뜬다.



```
requestpart.value() was empty on parameter 0
```

로그를 살펴보니 특정 업체 `G`랑 통신하는 `FeignClient` 객체를 생성하는 과정에서 오류가 발생했고, 위 같은 에러 메시지를 확인했다. 구글링을 해보니 정확히 일치하는 케이스가 없다. 



```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  String order(@RequestPart Map request);
}
```

해당 클래스를 열어보니, `@RequestPart` 어노테이션에서 `value` 파라미터에 값이 없어서 발생하는 걸로 보였다. 원래는 없어도 됐는데? 



```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  String order(@RequestPart(value="request") Map request);
}
```
그래도 비어서 안 된다니깐 임의로 값을 넣어보았다. 스프링은 잘 떴다!




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

다시 기존 소스를 살펴보면 실제 호출시에 파라미터에는 `LinkedMultiValueMap`이 들어간다. DTO를 `LinkedMultiValueMap`으로 변환하고 파라미터에 넣으면 `feign`에서 알아서 `multipart/form-data`에 맞게 HTTP Body를 생성해줬다. 근데 그게 2.2.2부터는 안 되는 상황. `feign` 로그 레벨을 Full로 해봤다. 2.2.1까지는 제대로 Body가 생성된 게 로그에 찍히는데 새 버전에서는 Binary Data라고 퉁친다. FallbackFactory 생성해서 파라미터 찍어보기는 좀 귀찮아서 이전에는 `@RequestPart` 어노테이션을 붙이면 `Map`의 키마다 HTTP 메시지를 생성해줬는데, 파라미터 하나당 메시지 한 개로 스펙이 바뀐건 아닌가 추론해봤다. 다른 방법이 있을 것 같아서 열심히 구글링 해봤는데 거의 `MultiPartFile`을 파라미터로 하지, `Map`을 파라미터로 하는 경우는 잘 없었다.



```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
  String order(RequestDto request);
}

public class GRequestDto extends RequestDto {
	@FormProperty("field_one")
	private String fieldOne;
	
  @FormProperty("field_two")
	private String fieldTwo;
}
```

스프링 공식 문서랑 스택오버플로우를 열심히 뒤져보다가 방법을 찾았다. 따로 어노테이션은 필요없고, `consumes`를 `multipart/form-data`로 하고 오브젝트를 그대로 집어넣으면 된다. 그리고 오브젝트 필드에 `@FormProperty`로 name을 세팅해주면 된다. 테스트 돌려보니 성공. 버전 업그레이드 완료~



## 전

일 수도 있겠지만 돌아만 간다고 끝내기엔 아쉬웠다. 여기엔 나름의 이유가 있었다.



```java
public interface MultiPartFeignClient {
    String order(Map request);
    String cancel(Map request);
    String resend(Map request);
    String status(Map request);
}
```

일단 `GFeignClient`를 보면 `MultiPartFeignClient` 인터페이스를 상속하고 있다. `MultiPartFeignClient`는 원래 위 코드처럼 생겼었다. 새롭게 찾은 방법을 적용하고 나서는 아래와 같이 바뀌었다.



```java
public interface MultiPartFeignClient {
    String order(RequestDto request);
    String cancel(RequestDto request);
    String resend(RequestDto request);
    String status(Map request); // 지금은 GFeignClient만 상속해서 문제없지만 나중에 다른 업체가 추가될 때 문제 발생 소지가 높다.
}
```

4개 메소드 중에 3개만 DTO를 파라미터로 받는다. 왜일까? 업체 스펙이 `POST` 요청할 때만 `multipart/form-data`로 하라고 했기 때문이다. `status()`는 `GET`에다가 쿼리 파라미터로 요청한다. 이전에 전부 `Map`이 었던 건 `@RequestPart` 어노테이션을 붙이면 `multipart/form-data`로 해주고, `@SpringQueryMap` 어노테이션을 붙이면 쿼리 파라미터를 만들어줬다. 근데 DTO에다가 `@SpringQueryMap`을 붙이면 쿼리 파라미터를 만들어주긴 하는데, 네이밍이 자바 기본 컨벤션인 카멜 케이스로 해준다. 나는 스네이크 케이스로 해야하는데 아무리 찾아도 방법이 안 나올 뿐더러 '방법이 없다'는 답변까지 확인했다. 일단 당장 `MultiPartFeignClient`를 상속하는 건 `G` 업체 밖에 없기 때문에 문제는 안 되지만, 그렇다고 이대로 둘 순 없다. 뭘로든 통일해야 확장성을 지킬 수 있을 걸로 보였다.



근데 왜 굳이 인터페이스를 또 상속했을까? 내가 담당하는 업체는 15개 업체다. 우리 쪽 DB 동일한데, 같은 데이터를 각각 15개 업체가 요구하는 포맷으로 세팅하고 응답도 각각 파싱해서 다시 같은 데이터로 맞춘다. `FeignClient`도 `A` 업체부터 `O` 업체까지 `AFeignClient`부터 `OFeignClient`까지 있다. 처음에는 메인 서비스 로직에서 각 업체별 `FeignClient`를 호출했는데, 고도화를 하면서 핸들러 어댑터 패턴을 적용하게 됐다. 구조는 다음과 같다. 



#### FeignService

```java
// FeignClient 호출을 대행하는 서비스. 메인 로직 처리 중에 업체 통신시 업체별 FeignClient를 직접 호출하지 않고, 이 친구를 호출한다.
@Service
public class FeignService {

    @Autowired
    private ApplicationContext applicationContext;

    private Map<Type, Object> map = new HashMap<Type, Object>();

    private List<FeignHandlerAdapter> adapters;

    @PostConstruct
    private void initFactory() {
      	// FeignClient Map을 초기화한다.
        map.put(A, applicationContext.getBean(AFeignClient.class));
     		map.put(B, applicationContext.getBean(BFeignClient.class));
      	...
        map.put(O, applicationContext.getBean(OFeignClient.class));
				
      	// HanderAdpater 리스트를 초기화한다.
        adapters = new ArrayList<>(applicationContext.getBeansOfType(FeignHandlerAdapter.class).values());
    }

    public String order(RequestDto request) {
      	// 요청 데이터의 업체 유형으로 FeignClient를 가져온다.
        Object handler = map.get(request.getType());

      	// HandlerAdapter 리스트에서 지원하는 Adapter를 찾는다.
        for (FeignHandlerAdapter adapter : adapters) {
            if (adapter.supports(handler)) {
                return adapter.order(handler, request);
            }
        }

        throw new Exception("오류");
    }
}

// JSON HandlerAdapter 구현체
@Component
public class JsonFeiginHandlerAdpater implements FeignHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof JsonFeignClient);
    }

    @Override
    public String order(Object handler, RequestDto request) {
        return ((JsonFeignClient) handler).order(request.toJsonString()); // DTO를 JSON String으로 변환하여 호출
    }
}

// Form HandlerAdapter 구현체
@Component
public class FormFeignHandlerAdapter implements FeignHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof FormFeignClient);
    }

    @Override
    public String order(Object handler, RequestDto request) {
        return ((FormFeignClient) handler).order(request.toQueryParams()); // DTO를 Form String으로 변환하여 호출
    }
}
```

우선 서비스 로직에서 업체랑 통신할 때는 업체의 `FeignClient`를 직접 호출하지 않는다. 대신 `FeignService`의 메소드를 호출한다. `FeignService`는 스프링이 뜰 때 각 업체를 키로 업체별 `FeignClient`를 가리키는 `Map`을 생성한다. 또 `FeignHandlerAdapter` 인터페이스를 구현하는 모든 객체를 저장하는 리스트를 생성한다. 메소드가 호출되면, 요청 데이터의 업체 타입 값으로 `FeignClient(handler)`를 가져온다. 그리고 리스트에서 각 `HandlerAdapter`의 구현체가 해당 `handler`를 지원하는지 체크하고, 지원하면 `HandlerAdapter` 구현체에서 `handler`의 메소드를 호출한다.  JSON 포맷 문자열로 바꾸든, Form 포맷 문자열로 바꾸든 공통 데이터의 처리는 `HandlerAdapter` 구현체에서 이루어진다. `FeignClient(handler)` 호출까지 하고 결과를 반환한다.



#### MultiPartHandlerAdapter

```java
@Component
public class MultiPartFeignHandlerAdapter implements FeignHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof MultiPartFeignClient);
    }

    @Override
    public String order(Object handler, RequestDto request) {
        return ((MultiPartFeignClient) handler).order(request.toMultiPartForm()); // multipart/form-data String으로 변환하여 호출
    }
}
```

그래서 `FeignClient` 의 메소드가 받는 파라미터는 모두 String으로 통일하고, 각 `HandlerAdapter` 구현체에서 JSON String, Form String, MultiPart String 등으로 변환해서 호출하려는 방식으로 수정하려고 했다. 그러면 포맷만 다를 뿐 String으로 바꾸는 건 같으니깐 나중에 불필요한 클래스를 줄이는 등 리팩토링할 여지가 생길 수 있을 거라 생각했다. 그래서 `toMultiPartForm()`을 구현했다.



또 다른 문제가 생겼다. `boundary`였다. 일단 String 직접 만들어서 잘 통신되나 보려고 고정값으로 뒀었다., 이전에 `feign`에서 알아서 변환해줄 때는 매 요청마다 `boundary` 값이 랜덤으로 생성됐다. 랜덤 생성하는 건 괜찮은데, 또 `FeignClient`에 전달해줘야 되고 `toMultiPartForm()`에서 반환하는 자료형은 어떻게 해야하며 이래저래 모양이 좋지 않았다. 그렇다고 고정값으로 박아버리는 게 문제가 없을지 장담할 수 없었다.



#### GFeignClient

```java
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
  @RequestMapping(method = RequestMethod.POST, value = "/order", produces = "MediaType.APPLICATION_JSON_VALUE", consumes = "multipart/form-data;charset=utf-8;boundary=abcderfghijklmnop")
  String order(Map request);
}
```

그러다가 얻어걸렸다. `FeignClient `에서 만들어주는 `boundary`는 어떤 모양인가 보려고 위와 같이 `RequestDto` 자료형만 `Map`으로 수정하고 테스트를 돌려봤는데 성공했다. 갑자기?



일단 성공했다. 내가 `Map`으로 받을 때 파라미터에 어노테이션 안 붙이고는 안 돌려봤었나? 돌려봤었던 거 같은데 착각인가? 일단 다시 돌려도 되는 걸 확인하고, 여기서 다시 만들어가려고 고정 `boundary`를 박아뒀던 `consumes`의 값을 다시 `MediaType.MULTIPART_FORM_DATA_VALUE`로 수정했다. 예시 코드에서야 메소드 하나지만, 실제로는 3개가 더 있으니깐 리터럴 보다는 static 변수 불러오는 게 나으니깐. 다시 테스트 코드 돌렸는데 실패. 뭐지? 다시 `"multipart/form-data;charset=utf-8;boundary=abcderfghijklmnop"` 리터럴로 바꾸니깐 성공. `bodundary` 빼고 `"multipart/form-data;charset=utf-8"`로 바꿔도 성공.



이제 HTTP 통신 로그를 볼 때다. `MediaType.MULTIPART_FORM_DATA_VALUE`에서 가져올 때랑 리터럴로 넣을 때랑 비교해봤다.



```
# MediaType.MULTIPART_FORM_DATA_VALUE를 쓸 때
Content-Length: 17
Content-Type: multipart/form-data; charset=UTF-8; boundary=18143a1dc3e

# 리터럴로 넣을 때
Content-Length: 1323
Content-Type: multipart/form-data;charset=UTF-8;boundary=-XQCWfCCMRpsMfgS8-4ebYASCvQvso
```

일단 `Content-Type`에서는 특이사항이 없다. 혹시 띄어쓰기 허용이 안 되나 싶긴 했는데 스펙상 OWS다. `Content-Length`가 현저히 차이난다. Body도 위에는 그냥 Binary Data로 퉁치는 반면, 아래는 내용을 정확히 보여준다. 결국 Body에 뭔가 다른 게 들어가있구나 추론할 수 있었다.  `Content-Type`의  `charset`의 이슈였다. `application/json`의 기본 인코딩이 `UTF-8`이라서 다른 것도 그러지 않을까 싶었다. HTTP 1.1의 기본 인코딩은 `ISO-8859-1`이라는 것 같다. 근데 결국 `Content-Type`에는 `charset=UTF-8` 이게 들어가는데? `HttpMessageConverter`를 까봐야 되나? 일단 이슈 해결부터 마무리 짓기로 했다.



## 결

#### MultiPartFeignClient

```java
public interface MultiPartFeignClient {
    String order(Map request);
    String cancel(Map request);
    String resend(Map request);
    String status(Map request);
}


@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
    @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = "multipart/form-data;charset=utf-8")
    String order(Map request);

    @RequestMapping(method = RequestMethod.GET, value = "/status?{request}", produces = MediaType.APPLICATION_JSON_VALUE)
    String status(@SpringQueryMap Map request);
}

@Component
public class MultiPartFeignHandlerAdapter implements FeignHandlerAdapter {

    @Override
    public boolean supports(Object handler) {
        return (handler instanceof MultiPartFeignClient);
    }

    @Override
    public String order(Object handler, RequestDto request) {
        return ((MultiPartFeignClient) handler).order(request.toMultiValueMap());
    }
}
```

그리하여 `MultiPartFeignClient`는 다시 위와 같은 형태를 띄게 되었다. 비록 `G` 업체는 `GET` 요청시에는 쿼리 파라미터로 데이터를 전달해줘야 하지만, `@SpringQueryMap` 어노테이션 덕분에 유연하게 대응할 수 있었다. `consumes` 파라미터에 값을 리터럴로 넣어줘야 한다는 안 예쁨이 남아있지만 별로 중요하진 않다. 다음에 `multipart/form-data`를 주로 사용하는 업체가 추가되어도 어느 정도 이 틀 안에서 유연하게 처리되리라 기대해본다.



```
@FeignClient
public interface GFeignClient extends MultiPartFeignClient {
    @RequestMapping(method = RequestMethod.POST, value = "/order", produces = MediaType.APPLICATION_JSON_VALUE, consumes = "multipart/form-data;charset=utf-8")
    String order(@RequestPart(value="request") Map request);
}
```

아 혹시 그럼 `@RequestPart`에 `value` 값은 아무거나 넣고, `consumes`에 charset만 넣어주면 됐던 거 아냐? 싶어서 위처럼 수정하고도 돌려봤다.



```
Could not write request: no suitable HttpMessageConverter found for request type [java.util.LinkedHashMap] and content type [multipart/form-data;charset=UTF-8]
```

위와 같은 메시지가 뜬다. 적절한 `HttpMessageConverter`를 찾을 수 없단다. 역시 저 친구를 까봐야겠다. 까보는 건 또 다른 포스팅 주제가 되어야 할 것 같다. 저 많은 구현체 중에 어떤 걸 선택했는지 모르겠다. 사실 어떤 구현체를 쓰는지, 그 구현체의 기본 인코딩이 뭔지만 확인하고 완결지어도 될 것 같긴 하다. `FormHttpMessageConverter`가 아닐까 싶은데, 여기는 그냥 DEFAUT CHARSET이라고 선언만 되어있다. 컨버터에서 쓰는 기본 인코딩이라는데 그게 뭔지 어디서 찾을까. 이건 좀 더 확인해보고 업데이트 해야겠다.
