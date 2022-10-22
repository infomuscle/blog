# 블로그

> 모든 포스팅은 [티스토리 블로그](https://bortfolio.tistory.com/)에서도 확인하실 수 있습니다.


## 목차

- [알고리즘](./algorithm)
    - [카카오 2019 블라인드 - 오픈채팅방](./algorithm/210303_카카오_2019_블라인드_문제풀이_오픈채팅방)
    - [카카오 2019 블라인드 - 실패율](./algorithm/210307_카카오_2019_블라인드_문제풀이_실패율)
    - [카카오 2019 블라인드 - 후보키](./algorithm/210416_카카오_2019_블라인드_문제풀이_후보키)
    - [카카오 2019 블라인드 - 무지의 먹방 라이브](./algorithm/210422_카카오_2019_블라인드_문제풀이_무지의_먹방_라이브)
- [자바](./java)
    - [JVM Garbage Collector](./java/210501_JVM_Garbage_Collector)
    - [String의 equals()와 hashCode()](./java/210513_String의_equals()와_hashCode())
    - [MyBatis #과 $의 차이](./java/210914_MyBatis_%23과_%24의_차이)
    - [객체지향과 SOLID](./java/211011_객체_지향과_SOLID)
    - [JPA 영속성 컨텍스트](./java/211016_JPA_영속성_컨텍스트)
    - [JPA N+1 문제 해결](./java/221023_JPA_N+1_문제_해결)
- [네트워크](./network)
    - [URI vs URL vs URN](./network/211012_URI_vs_URL_vs_URN)
- [스프링](./spring)
    - [스프링부트 어노테이션 정리](./spring/210315_스프링부트_어노테이션_정리)
    - [IntelliJ IDEA에서 Spring Boot 프로젝트 생성](./spring/210504_IntelliJ_IDEA에서_Spring_Boot_프로젝트_생성)
    - [@Transactional 어노테이션의 롤백 테스트](./spring/210512_@Transactional_어노테이션의_롤백_테스트)
    - [Feign으로 Multipart 요청 보내기](./spring/220611_Feign으로_MultiPart_요청_보내기)
    - [스프링 컨트롤러 인터페이스 저장 방식 개선](./spring/221007_스프링_컨트롤러_인터페이스_저장_방식_개선)
- [파이썬](./python)
    - [파이썬 문자열 함수 정리](./python/210316_파이썬_문자열_함수_정리)
    - [파이썬 버전 및 가상환경 관리 - pyenv](./python/201127_파이썬_버전_및_가상환경_관리_pyenv)
- [데이터베이스](./database)
    - [데이터베이스란](./database/210212_데이터베이스란)
    - [데이터 모델과 관계형 데이터베이스](./database/210304_데이터_모델과_관계형_데이터베이스)
    - [mac OS에서 도커로 오라클 EE 12c 설치](./database/210318_mac_OS에서_도커로_오라클_EE_12c_설치)
    - [mac OS에서 도커로 오라클 XE 11g 설치](./database/210319_mac_OS에서_도커로_오라클_XE_11g_설치)
    - [SELECT로 데이터 조회하기](./database/210325_SELECT로_데이터_조회하기)
    - [트랜잭션의 특성과 격리 수준](./database/211025_트랜잭션의_특성과_격리_수준)
- [기타](./etcl)
    - [마크다운 문법 정리](./etc/210305_마크다운_문법_정리)
    - [IntelliJ IDEA 플러그인 추천](./etc/210326_InetlliJ_IDEA_플러그인_추천)
    - [398일간의 1일 1커밋 회고](https://github.com/infomuscle/blog/tree/master/etc/210711_365%EC%9D%BC%EA%B0%84%EC%9D%98_1%EC%9D%BC_1%EC%BB%A4%EB%B0%8B_%ED%9A%8C%EA%B3%A0)




## 작성 예정
- 컨트롤러, AOP, ExceptionHandler
- JPA, Hibernate, Spring Data JPA
- OSI 7 Layer
- IoC, DI
- REST API
- 통합 테스트와 단위 테스트
- Oracle 자료형 정리
- 자바 스트림
- Vim 사용법
- 스프링부트 배너 변경
- xor 스왑
- 자료형과 2의 보수
- 로드밸런서
- GC 튜닝
- MyBatis @Alias
- 동시성 이슈와 ConcurrentHashMap
- Jenkins, Docker, 빌드 및 배포 자동화
- Java Static
- 스프링 의존관계 주입 방법 + 롬복
- request 스코프
- 메모리 단편화
- 로드밸런서
- 컴팩션
- 세션/쿠키
- JWT 토큰 로그인 구현
- 스프링 시큐리티
- Servlet
- 디자인 패턴
  - 팩토리 메서드/스태틱 팩토리 메서드/어댑터/싱글톤/빌더
- Sentry
- Mockito
- MSA
  - 트랜잭션/단점/Pinpoint

- 디버깅
- feign compression
- 아마존




## 내가 보기 위한 CS 초간단 요약

- 적당히 정리 되면 파일 분리하여 링크 예정

### 자바

#### [객체지향과 SOLID](./java/211011_객체_지향과_SOLID)
- 객체지향
    - `추상화`: 현실세계의 개념을 반영하는 것. 객체들의 공통적인 특징, 데이터와 기능을 도출한다.
    - `캡슐화`: 객체의 역할을 수행하기 위한 하나의 목적을 위해 데이터와 기능들을 묶는 것
    - `상속성`: 새로운 클래스가 기존의 클래스의 자료와 연산을 이용하게 해주는 것
    - `다형성`: 하나의 클래스나 메소드를 다양한 방식으로 이용할 수 있게 한다. (오버라이딩,오버로딩)
- SOLID
    - SRP: 단일 책임 원칙(Single Responsibility)
    - OCP: 개방/폐쇄 원칙(Open/Closed)
    - LSP: 리스코프 치환 원칙(Liskov Substitution)
    - ISP 인터페이스 분리 원칙(Interface Segregation)
    - DIP: 의존관계 역전 원칙(Dependency Inversion)

#### 바이트코드

- 자바 코드는 컴파일시 바이트코드(.class)로 변환된다. 그리고 JVM은 런타임에서 이 바이트코드를 기계어로 번역한다. 덕분에 자바는 플랫폼 독립적인 특성을 갖는다. 즉 바이트코드로 컴파일을 한번만 하면 JVM에서 읽을 수 있고, JVM이 올라가는 여러 플랫폼에서 똑같은 프로그램을 실행시킬 수 있다. 물론 JVM이 많은 메모리리를 사용하고, 실행속도가 빠르지 않다는 단점이 있다. 바이트코드를 기계어로 변환하는 속도도 일반적인 컴파일 언어보다 느리다.

#### 자료형

- 자바에서 다룰 수 있는 데이터의 종류를 말한다. 좀 더 엄밀하게 얘기하면, 데이터를 메모리에 저장하기 위해 공간을 할당할 때 그 공간의 특성을 의미한다. 기본 자료형은 변수에 값 자체가 저장된다. 반면 참조 자료형은 객체의 주소가 저장된다. 객체는 힙 영역에 생성된다.
- 기본 자료형
  - 정수형
      - `byte`: 1byte == 8bit == 2^8
      - `short`: 2byte == 16bit == 2^16
      - `int`: 4byte == 32bit == 2^32
      - `long`: 8byte == 64bit = 2^64
  - 문자형
      - `char`: 2byte == 16bit == 2^16
  - 실수형
      - `float` : 4byte == 32bit == 2^32
      - `double`: 8byte == 64bit == 2^64
  - 논리형
      - `boolean`: 1byte == 8bit == 2^8
- 참조 자료형
  - 배열(Array)
  - 열거(Enumeration)
  - 클래스(Class)
  - 인터페이스(Interface)
- 자료형과 변수의 차이는?

  - 변수: 메모리를 저장하는 공간
  - 자료형: 그 공간의 특성

- Wrapper Class
  - 박싱: Primative -> Wrapper Class
  - 언박싱: Wrapper Class -> Primative
  - 자바 1.5부터는 오토박싱과 오토언박싱을 지원

#### 예약어

자바에 등록되어 특정 목적을 위해 사용되는 단어. 예약어는 변수명, 메소드명 등으로 사용할 수 없다. 몇가지 주요 예약어 특징 정리.

- `final`: 한번 값이 할당이 되면 바꿀 수 없다.클래스, 메소드, 변수에 붙을 수 있다.
  - 클래스: 상속이 불가능하다.
  - 메소드: 오버라이드가 불가능하다.
  - 변수: 원시 타입의 경우 상수값이 된다. 참조 타입의 경우 할당된 객체를 바꿀 수 없다. 인스턴스를 다른 인스턴스로 바꿀 수 없는 것이지, 인스턴스가 갖고 있는 값은 바꿀 수 있다.
- `static`: static 영역에 할당되어 프로그램이 끝날 때까지 메모리가 유지된다. 객체가 공유할 수 있어 어디서든 접근할 수 있다는 장점이 있다. 그러나 GC에 의해서 관리되지 않기 때문에 과도하게 사용하면 시스템 성능에 악영향을 줄 수 있다. Method Area에 있었으나 자바8부터 Heap으로.
- `synchronized`: 여러 쓰레드가 하나의 자원에 접근하려 할 때, 단 하나의 쓰레드만이 접근 가능하도록 한다. 

#### 접근제어자

변수, 함수, 클래스 등으로의 접근 제한을 설정하는 예약어. 대상이 변경되어서는 안 될 곳에서 변경되는 것을 방지하기 위해 사용한다.

- `public`: 어디서나 접근 가능
- `protected`: 같은 패키지 및 자식 클래스에서 접근 가능
- `default`: 같은 패키지에서만 접근 가능
- `private`: 클래스 내부에서만 접근 가능 

#### 컬렉션

- Set: 순서없음. 중복 허용하지 않음.
- Map: Hash. 키 순서 없음. 키는 중복 허용하지 않음.
- List: 순서있음. 중복 허용.

#### 제네릭

- 제네릭을 사용하는 이유?

#### Stack vs Heap

- Stack
  - 정적으로 메모리가 할당된다.
  - `Primative` 타입의 데이터가 값과 함께 할당된다.
  - `Heap`에 생성된 오브젝트의 참조값이 할당된다.
  - `Thread`마다 고유의 `Stack`을 가진다.
  - 지역변수와 매개변수가 저장된다.(전역변수, 정적변수는 데이터 영역에)
  - 높은 주소에서 낮은 주소로.
- Heap

  - 동적으로 메모리가 할당된다.
  - 오브젝트가 저장된다.
  - 낮은 주소에서 높은 주소로.

> Stack Overflow  
> 스택 프레임이 스택 영역의 경계를 넘어서서 힙 영역과 겹치게 되는 오류

#### [JVM 가비지 컬렉터](./java/210501_JVM_Garbage_Collector)

- Stack의 변수는 Heap의 오브젝트를 참조한다. 변수와 오브젝트의 연결이 끊어진 상태를 Unreachable, JVM GC는 이 상태의 오브젝트에 대해 메모리를 해제한다.
- Heap은 Young과 Old로 구분되고, Young은 또 Eden과 Survival들로 구분된다. 각 영역이 찰 때 GC가 발생하고, 살아남은 오브젝트를 다른 영역으로 옮긴다. Young을 Minor GC, Old를 Major GC라고 부른다.
- 오브젝트는 처음 Eden에 생성. Eden에서 살아남으면 Survival로 이동. Survival에서 살아남은 건 다른 Survival로 이동. 왔다갔다 하다가 특정 age에 도달하면 Old로 이동.
- GC 전략에는 무엇이 있을까?


#### [String의 equals()와 hashCode()](./java/210513_String의_equals()와_hashCode())

- 리터럴과 new의 차이
- String Constant Pool
- hashCode

#### 정리 필요
- 추상 클래스(abstract class)와 인터페이스(interface)..



### 스프링

- MVC 패턴
    - `Controller`: 사용자로부터 요청을 받는 영역
    - `Model`: 데이터를 처리하는 영역
    - `View`: 처리 결과를 바탕으로 응답을 표시하는 영역
- IoC, DI

#### @Transactional

- Propagation

  |               | 트랜잭션 진행 중                         | 트랜잭션 미진행      |
  | ------------- | ---------------------------------------- | -------------------- |
  | REQUIRED      | 해당 트랜잭션 사용                       | 새로운 트랜잭션 생성 |
  | MANDATORY     | 해당 트랜잭션 사용                       | 예외 발생            |
  | REQUIRES_NEW  | 해당 트랜잭션 보류, 새로운 트랜잭션 생성 | 새로운 트랜잭션 생성 |
  | SUPPORTS      | 해당 트랜잭션 사용                       | 트랜잭션 없이 진행   |
  | NOT_SUPPORTED | 해당 트랜잭션 보류                       | 트랜잭션 없이 진행   |
  | NEVER         | 예외 발생                                | 트랜잭션 없이 진행   |
  | NESTED        | 중첩 트랜잭션 생성                       | 새로운 트랜잭션 생성 |

- Isolation

- Rollback

#### Dispatcher Servlet

- sevlet container -> dispatcher servlet -> handler



### 네트워크

#### OSI 7 Layer

- `Physical Layer`: 비트를 전자기파로 바꾸는 역할. PHY였나? 물리적으로 구현되어 있다.
- `Data Link Layer`: 내용 앞뒤로 구분자(?)를 추가하는 역할. Lan 카드인가 물리적으로 구현되어 있다.
- `Network Layer`: 주소 추가.
- `Transport Layer`: 포트 추가.
- `Application Layer`: 현대에 5~7은 Application Layer로 통합.
- 5와 6은 무엇을 할까? 

#### TCP/IP 4 Layer

#### HTTP 프로토콜
- `Hyper Text Transfer Protocol`
- 구성
    - Header
    - Body

#### Restful API
- 아키텍처
- `Representational State Transfer`
- HTTP URI(Uniform Resource Identifier)를 통해 자원(Resource)을 명시.
- HTTP Method와 해당 자원에 대한 CRUD Operation 매핑
    - `POST`: 데이터 생성 요청. Create
    - `GET`: 데이터 조회 요청.Read
    - `PUT`: 데이터 수정 or 생성 요청. Create or Update
    - `DELETE`: 데이터 삭제 요청. Delete
    - `PATCH`: 데이터 수정 요청. Update
- 장점
    - 의도하는 바를 쉽게 파악할 수 있다.
- 단점
    - 표준이 없다.

####  HTTP vs HTTPS

- HTTP에 보안 계층을 추가하여 패킷 암호화.

#### TCP vs UDP
- `TCP`: 상호작용. 파일 전송 등 신뢰성 중요.
  - 3 way handshake: 가상회선 수립. SYN(n) -> ACK(n+1) + SYN(m) -> ACK(m+1). 
  - 4 way handshake: 연결 해제.
- `UDP`: 보내고 끝. 빠름. 스트리밍 등 효율성 중요.



### 데이터베이스

#### [트랜잭션의 특성과 격리 수준](./database/211025_트랜잭션의_특성과_격리_수준)

데이터베이스의 기능을 논리적 작업으로서 하나의 단위로 묶은 집합.
- ACID
  - Atomicity(원자성): 커밋 || 롤백 -> 집합의 각 기능은 모두 반영되거나, 아무것도 반영되지 않아야 한다.
  - Consistency(일관성): 작업 처리 결과는 항상 일관적이어야 한다.
  - Isolation(독립성): 여러 트랜잭션이 실행 중일 때 다른 트랜잭션에 간섭할 수 없다. -> 데이터베이스 락
  - Durability(지속성): 트랜잭션 커밋 결과는 영구 반영되어야 한다.

- 데이터베이스 락
  - ex) 100,000원 계좌에서 10,000원 출금 요청 A와 10,000원 출금 요청 B가 0.1초 간격으로 들어온다면?
             1. A가 현재 잔액 100,000원 조회
          2. B가 현재 잔액 100,000원 조회
          3. A가 10,000원 뺀 90,000원 업데이트
          4. B가 10,000원 뺀 90,000원 업데이트
          5. 남은 잔액은 90,000원? -> 이럼 안된다.

- 트랜잭션 격리 수준

  - Read uncommited: 격리 수준이 제일 낮음. 커밋되지 않은 작업내역도 다른 트랜잭션에서 볼 수 있음.
      - Dirty Read
  - Read commited: 커밋된 작업만 다른 트랜잭션에서 읽을 수 있음. 그러나 여러번 조회시 값이 바뀌는 문제가 생김.
      - Unrepeatable read
  - Repeatable read
      - Phantom read
  - Serializable: 락이 걸림. 최고 수준의 격리이나 성능이 많이 떨어짐. 

  데이터 정합성과 동시성의 trade-off 때문에 현실적으로는 격리 수준을 고려한다.

  일반적인 경우 Read Commited - Repetable Read 사이에서 선택.

#### 정규화

데이터의 중복을 허용하지 않는 방식으로 데이터베이스를 설계하는 것. 무결성을 유지하고 용량을 줄일 수 있다.

- 제1정규화: 컬럼이 원자값을 갖도록 한다.

  - 정규화 전

    | 이름     | 그룹명           |
    | -------- | ---------------- |
    | 랩몬스터 | 방탄소년단       |
    | 황민현   | 뉴이스트, 워너원 |
    | 윈터     | 에스파           |

  - 정규화 후

    | 이름     | 그룹명     |
    | -------- | ---------- |
    | 랩몬스터 | 방탄소년단 |
    | 황민현   | 뉴이스트   |
    | 황민현   | 워너원     |
    | 윈터     | 에스파     |

    

- 제2정규화: PK에 종속된 컬럼이 포함되어선 안 된다.

  - 정규화 전

    | 이름(PK) | 그룹명(PK) | 소속사   |
    | -------- | ---------- | -------- |
    | 랩몬스터 | 방탄소년단 | 빅히트   |
    | 황민현   | 뉴이스트   | 플레디스 |
    | 황민현   | 워너원     | YMC      |
    | 윈터     | 에스파     | SM       |

  - 정규화 후

    | 이름(PK) | 그룹명(PK) |
    | -------- | ---------- |
    | 랩몬스터 | 방탄소년단 |
    | 황민현   | 뉴이스트   |
    | 황민현   | 워너원     |
    | 윈터     | 에스파     |

    | 그룹명(PK) | 소속사   |
    | ---------- | -------- |
    | 방탄소년단 | 빅히트   |
    | 뉴이스트   | 플레디스 |
    | 워너원     | YMC      |
    | 에스파     | SM       |

- 제3정규화

  - PK가 아닌 컬럼에 종속된 컬럼이 생겨선 안 됨.

- BCNF 정규화

  - 모든 결정자가 후보키가 되도록 테이블을 분ㅂ해한다.

- 반정규화

  - 효율을 위해서 중복을 허용하는 것. 
  - Join의 엄청난 연산량을 피하기 위해서 사용
  - 정규화되지 않은 것과는 다름.

#### 인덱스

#### 실행계획



### 운영체제

#### 메모리 구조

|영역||설명|
|---|---|---|
|코드(텍스트)|코드|실행할 프로그램의 코드.|
|데이터|전역 변수, 정적변수|프로그램의 시작시 할당, 종료시 소멸.|
|힙|인스턴스|런타임에서 크기가 결정됨. 낮은 주소에서 높은 주소로 메모리 할당.|
|스택|지역변수, 매개변수|컴파일에서 크기 결정. 높은 주소에서 낮은 주소로 메모리 할당.|

- 

#### 동적할당

#### 프로세스 vs 쓰레드

- Multi Process
  - 하나의 프로그램을 여러 프로세스로 구성.
  - 각 프로세스가 하나의 Task를 처리.
  - 프로세스 하나가 잘못 되어도 동작은 하나, Context Switching 비용 발생
- Multi Thread
  - 하나의 프로세스를 여러 쓰레드로 구성.
  - 각 쓰레드가 Task를 처리.
  - 시스템 자원 소모, 처리 비용 등이 감소되나 동기화 이슈 발생 가능.
  - 쓰레드 하나의 오류로 전체 프로세스 문제 발생 가능.
- Context Switching
  - CPU에서 여러 프로세스를 돌아가며 작업을 처리하는 과정
- Thread Safe
  - Multi-Thread 환경에서 안전하다.
  - 함수가 전역 변수를 참조하고 있다면 Thread-Safe 하지 않을 수 있음
  - synchronized 블록

#### 메모리 파편화

- 정의
  - RAM에서 메모리의 공간이 조각나서, 가용 메모리가 충분하지만 할당이 불가능한 상태.
- 종류
  - 내부 단편화
    - 프로세스가 필요한 양보다 큰 메모리가 할당되는 경우
    - ex) 4kb 메모리를 할당받았지만, 1kb만 사용하고 있을 때 3kb의 내부 단편화 발생
  - 외부 단편화
    - 메모리의 할당 및 해제가 반복될 때 중간중간 빈 메모리가 존재. 빈 메모리의 총합은 충분하지만, 각 메모리의 최대치가 작아 프로세스를 할당할 수 없는 상태.
    - ex) 여러 프로세스를 할당, 해제를 반복하다보니 빈 메모리 공간이 3kb, 6kb, 7kb가 존재. 8kb의 프로세스를 할당하고 싶다. 전체 빈 메모리는 16kb이지만 할당 가능한 메모리 구역이 없다.
- 해결방법
  - 페이징: 가상 메모리 사용. 외부 단편화 해결. 내부 단편화 존재.
    - 페이지: 일정한 크기로 나눈 가상(논리) 메모리 블록.
    - 프레임: 페이지와 같은 크기의 물리 메모리(RAM) 공간
    - 사용하지 않는 프레임을 페이지에 옮기고, 필요한 메모리를 페이지 단위로 프레임에 옮긴다.
    - 같은 크기로 분할.
    - 페이징 테이블에는 페이지 번호와 매핑된 프레임의 시작 물리 주소 저장.
    - 페이지 단위를 작게 하면 내부 단편화 해결. 그러나 페이지 매핑이 많아지므로 오히려 효율 저하 가능성.
  - 세그멘테이션: 가상 메모리 사용. 내부 단편화 해결. 외부 단편화 존재.
    - 세그먼트: 서로 크기가 다른 가상 메모리 블록.
    - 크기가 다르기 때문에 미리 분할할 수 없고, 메모리에 적제될 때 빈 공간을 찾아 할당.
    - 각 세그먼트는 연속적인 공간에 저장.
    - 세그먼트 테이블에는 시작 물리 주소와 세그먼트의 길이를 저장.
  - 메모리 풀
    - 필요한 메모리 공간의 크기와 개수를 사용자가 직접 지정하여 할당받아놓고, 필요할 때마다 사용하고 반납.
    - 알고리즘에 의한 할당 및 해제와 달리 미리 공간을 할당해 사용하고 반납하기 때문에 외부 단편화 발생 X.
    - 필요한 크기만큼 할당하기 때문에 내부 단편화 X.
    - 메모리 단편화로 인한 메모리 낭비 > 메모리풀을 쓰지 않을 때의 메모리양일 경우 사용하지 않아야 함.
    - 메모리의 할당 및 해제가 잦은 경우 효과적.
    - 메모리 누수 존재.

#### 메모리 누수

- 더이상 사용되지 않는 메모리가 계속 점유되고 있는 현상. 자바에서는 GC에서 회수되지 않고 누적되는 경우를 부른다. old 영역에 객체가 누적됐으나, 해제되지 않으면서 메이저 GC가 빈번하게 발생하고 성능이 떨어진다.

#### 메모리 컴팩션

- 메모리를 이동하여 여유 공간을 만드는 작업. 작업 단위로 프로세스를 할당하면 작업이 끊기지 않고 잘 수행될 수 있지만, 메모리 공간이 부족할 때 Compaction을 하면 오버헤드 발생, 비용이 크기 때문에 잘 사용하지 않는다.

#### 스케줄링

- 선점
  - Round Robin
  - Shortest Remaint Time
  - Multi Level Queue
  - Multi Level Feedback Queue
- 비선점
  - First Come First Service == FIFO
  - Shortest Job First
  - Highest Respons-ratio Next

#### 페이지 교체 알고리즘

- LRU(Least Recently Used)
  - 가정: 가장 오랫동안 사용하지 않은 페이지라면 앞으로도 사용할 확률이 적을 것이다.
- FIFO(First In First Outs)
  - 메모리에 올라온 페이지 순서대로 내보냄
- LFU(Least Frequently Used)
- MFU(Most Frequently Used)
- NUR(Not Used Recently)
- OPT(Optimal)
  - 가장 오랫동안 사용되지 않을 페이지 교체
  - 프로세스가 앞으로 사용할 페이지를 미리 알아야 함
  - 이상적이나 실현 불가능



### 소프트웨어 일반

#### 스케일아웃 vs 스케일업

- 스케일아웃은 서버의 대수를 늘리고, 스케일업은 서버의 성능을 늘린다.

#### 라이브러리 vs 프레임워크

- 핵심은 제어의 주도권. 라이브러리는 개발자가 실행 흐름을 제어한다. 반면 프레임워크는 정해진 생명 주기 같은 것이 있고, 그 흐름 안에서 개발할 수 있다.

#### MSA

- 장점: 일부 서비스의 장애가 전체 서비스에 주는 영향이 적다. 각 서비스의 특성에 따라 적합한 기술 스택을 선택할 수 있다.
- 단점: 트랜잭션 처리가 어렵다. 통신 비용이 발생한다.
- MSA 트랜잭션은 어떻게 처리할까?
  - SAGA 패턴

### 자료구조 & 알고리즘

#### Stack으로 계산기 구현

#### Stack 2개로 Queue 구현

- inStack과 outStack. outStack이 비었을 때만 inStack -> outStack.

#### DFS && BFS

- DFS -> Stack
- BFS -> Queue



### 기타

#### MyBatis vs JPA

#### Kafka vs RabbitMQ

###  참고
- https://martianlee.github.io/posts/naver-interview-review/
- https://remover.tistory.com/177
- https://jw910911.tistory.com/8
- https://dongza.tistory.com/m/13
- https://github.com/JaeYeopHan/Interview_Question_for_Beginner
- https://github.com/MaximAbramchuck/awesome-interview-questions
- https://mellowp-dev.tistory.com/4
