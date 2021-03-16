# [Spring Boot] 스프링부트 어노테이션 정리

![Spring Boot Logo](./image.png)
<!-- [##_Image|kage@FEV9m/btq0c1L3LV4/LRrR2cNDTktvtmSXsq8vS0/img.png|alignCenter|width="100%"|_##] -->

- 개인적 학습을 위한 스프링부트 어노테이션 정리
- 어노테이션이 포함된 최상위 패키지를 기준으로 분류


## context

### @Bean

어노테이션이 붙은 클래스에서 리턴하는 객체를 `Bean`으로 등록한다. 사용되는 클래스에 `@Configuration` 어노테이션을 반드시 명시해줘야 한다.

> [MEMO]  
> Bean은 스프링 IoC 컨테이너에 의해 인스턴스화, 관리, 생성되는 객체를 의미한다.


### @Configuration

해당 클래스에서 `Bean`을 생성함을 명시한다. 내부적으로 `@Bean` 어노테이션이 붙은 메소드를 사용한다.

```java
@Configuration
public class SampleConfig {
    @Bean
    public SampleController sampleController() {
        return new SampleController;
    }
}
```


### @ComponentScan
다음 어노테이션이 사용된 클래스를 찾아서 `Bean`으로 등록해준다.
- `@Component`
- `@Controller`
- `@Service`
- `@Repository`
- `@Configuration`


## stereotype

### @Component
어노테이션이 붙은 클래스를 `Bean`으로 등록한다.

> [MEMO]  
> @Bean과 @Component의 차이점은?  
> Bean은 개발자가 직접 제어할 수 없는 외부 라이브러리를 Bean으로 만들어준다.  
> Component는 개발자가 직접 생성한 클래스를 Bean으로 만든다.


### @Controller
`@Component`와 동일한 기능. 해당 `Bean`이 `Controller`임을 명시한다. `@Controller`는 기본적으로 View를 리턴하는 경우 사용한다. API로 사용할 경우 리턴 자료형 앞에 `@ResponseBody`를 추가한다.

```java
@Controller
public class SampleController {

}
```

> [MEMO]  
> @RestController를 사용하면 API 컨트롤러가 기본값이 된다.


### @Service
`@Component`와 동일한 기능. 해당 `Bean`이 `Service`을 명시한다.

```java
@Service
public class SampleService {

}
```


### @Repository
`@Component`와 동일한 기능. 해당 `Bean`이 `Repository`임을 명시한다.

```java
@Repository
public class SampleRepository {

}
```


## bean

### @Autowired
변수에 `Bean`을 자동으로 주입한다.

```java
@Service
public class SampleService {
    @Autowired
    SampleService sampleService;
}
```


## boot

### @EnableAutoConfiguration
`classpath`에 기반해서 스프링 `ApplicationContext`를 자동으로 설정한다.


### @SpringBootApplication
`@Configuration`, `@EnableAutoConfiguration`, `@ComponentScan`를 모두 기능한다. 즉 해당 클래스에서 `Bean`을 생성할 수 있으며, `ApplicationContext`를 설정하고, `Component`를 모두 찾아 등록한다. 



<!-- 
## bind
### @RestController
### @ResponseBody
### @RequestMapping
### @GetMapping
### @PostMapping
### @PutMapping
### @DeleteMapping
### @PatchMapping
### @RequestHeader
### @RequestBody
### @RequestParam
### @PathVariable


## transaction
### @Transactional


## scheduling
### @Async



## javax
### @PostConstruct
### @Entity
### @Table
### @ManyToOne
### @OneToMany
### @Id
### @GeneratedValue

## lombok
### @Data
### @Getter
### @Setter
### @NoArgsConstructor
### @AllArgsConstructor
### @Slf4j
### @ToString
### @Builder
-->