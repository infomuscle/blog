## URI(Uniform Resource Identifier)



### URI? URL? URN?

> URI는 로케이터(locator), 이름(name) 또는 둘 다 추가로 분류될 수 있다. 



url: foo://example.com:8042/over/there?name=ferret#nose

​	scheme / authority / path / query / fragment

urn:example:animal:ferret:nose



- Unifrom: 리소스 식별하는 통일된 방식

- Resource: 자원, URI로 식별할 수 있는 모든 것(제한없음)
- Identifier: 다른 항목과 구분하는데 필요한 정보



URL(Uniform Resource Locator): 리소스가 있는 위치를 지정

URN(Uniform Resource Name): 리소스에 이름을 부여

위치는 변할 수 있지만 이름은 변하지 않는다.

urn:isbn:8960777331(어떤 책의 isbn URN)

URN이름 만으로 실제 리소스를 찾을 수 있는 방법이 보편화 되지 않음

-> URI :=: URL



### URL

```
scheme://[userinfo@]host[:port][/path][?query][#fragment]
```

ex) https://www.google.com:443/search?q=hello&hl=ko

- 프로토콜(https)
- 호스트명(www.google.com)
- 포트 번호(443)
- 패스(/search)
- 쿼리 파라미터(q=hello&hl=ko)



#### Scheme

- 주로 프로토몰 사용
- 프로토콜: 어떤 방식으로 자원에 접근할 것인가 하는 약속 규칙
  - ex) http, https, ftp
- http는 80 포트, https는 443 포트를 주로 사용, 포트는 ㅅ애략 가능
- https는 http에 보안 추가

#### User Info

- URL에 사용자 정보를 포함해서 인증
- 거의 사용하지 않음

#### Host

- 호스트명
- 도메인명 또는 IP 주소를 직접 사용 가능

#### Port

- 접속 포트
- 일반적으로 생략, 생략시 http는 80, https는 443

Path

- 리소스 경로, 계층적 구조
- ex)
  - /home/file.jpg
  - /members
  - /members/100, /items/iphone12

#### Query

- key=value 형태
- ?로 시작, &으로 연결 ?keyA=valueA&keyB=valueB
- query parameter, query string 등으로 불림.
- 웹서버에서 제공하는 파라미터
- 문자 형태

#### Fragment

- html 내부 북마크 등에 사용
- 서버에 전송하는 정보 아님