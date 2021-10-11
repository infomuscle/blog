# 웹 브라우저 요청 흐름



> 웹 브라우저에서 다음과 같은 URL을 입력한 후 엔터를 누르면 무슨 일이 일어날까?

```text
https://www.google.com:443/search?q=hello&hl=ko
```

- URL 구조에 대한 내용은 아래 링크를 참고
  - URI, URL, URN



## 클라이언트



### 애플리케이션 레이어

1. 도메인 네임을 가지고 DNS 서버에서 IP 정보를 조회한다.
2. Port 정보 확인
   - 별도로 입력하지 않으면, http는 80, https는 443을 세팅한다.

3. HTTP 요청 메시지를 생성한다.

   ```http
   GET /search?q=hello&hl=ko HTTP/1.1
   Host: www.google.com
   ```

4. Socket 라이브러리를 통해 TCP/IP 계층으로 전달

   - A. TCP/IP 연결
     - Syn, Syn+Ack, Ack 과정을 통해 서버 연결 여부를 확인

   - B. 데이터 전달



### TCP/IP

1. 데이터를 감싼 패킷 생성
2. 네트워크 인터페이스 통해 전달



## 서버

1. 패킷 수신 및 분석

2. HTTP 메시지 해석

3. path, query대로 자원 탐색

4. HTTP 응답 메시지 생성

   ```
   HTTP/1.1 200 OK
   Content-Type: text/html;charset=UTF-8
   Content-Length: 3423
   <html>
    <body></body>
    </html>
   ```

5. 응답 패킷 전달



