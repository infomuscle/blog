# 인텔리제이에서 스프링부트 프로젝트 생성



## 시작 화면

인텔리제이 열기

`NEW PROJECT` 버튼 클릭


## New Project 1

좌측 탭에서 `Spring Initializr' 선택

`Project SDK`에서 원하는 자바 버전 선택

Default 선택 후 `NEXT` 버튼 클릭

## New Project 2 (Project Settings)

Group 및 Artifact 설정

> [MEMO]
> 여기에서 패키지명이 정해짐

Maven || **Gradle** 선택

**Java** || Kotlin || Groovy 선

**Jar** || War 선택

> [MEMO]
> Jar와 War의 차이
> JAR: 스프링부트의 내장 톰캣을 이용
> WAR: 외부 톰캣을 이용


## New Project 3 (Dependencies)

### Developer Tools

- Lombok

### Web

- Spring Web

### Template

- 기타(Thymeleaf, Freemarker, Mustache 등)

### Security

- 기타(Spring Security, OAuth2 등)

### SQL

- Spring Data JPA

- H2 Database

- 기타 (JDBC, MyBatis, 기타 DB 드라이버 등) 

### NoSQL

- 기타(MongoDB 등)

### Messaging

- 기타(RabbitMQ, Kafka 등)

### Observability

- 기타(Datagdog, Influx, Prometheus 등)

### New Project 4

프로젝트 이름 및 경로 세팅 후 `FINISH` 버튼 클릭



