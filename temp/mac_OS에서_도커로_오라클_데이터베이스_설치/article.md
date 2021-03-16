# [Oracle] mac OS에서 도커로 오라클 데이터베이스 설치

맥 환경에서 도커를 활용하여 오라클 데이터베이스 설치 방법.
- 도커가 설치되어 있다고 전제한다.

> **[TODO]**  
> Homebrew로 Docker 설치 포스팅 후 링크 추가


## 1. 도커 Hub 오라클 데이터베이스 페이지

https://hub.docker.com/_/oracle-database-enterprise-edition

Proceed Checkout 클릭

## 2. 정보 입력

정보를 입력하고 약관에 동의한다.

## 3. 도커 이미지 다운로드 정보 확인

## 4. 터미널에서 도커 이미지 Pull

```bash
$ docker pull store/oracle/database-enterprise:12.2.0.1
$ docker pull store/oracle/database-enterprise:12.2.0.1-slim
```

슬림 버전 이미지는 약 1.4GB

## 5. 도커 컨테이너 생성 

```bash
$ docker images
```

```bash
$ docker run -d -it --name <Oracle-DB> -p 1521:1521 -e "TZ=Asia/Seoul" store/oracle/database-enterprise:12.2.0.1-slim
```

도커 실행 옵션
- `-d`
- `-it`
- `--name <Oracle-DB>` 컨테이너 이름을 \<Oracle-DB\>로 지정
- `-p 1521:1521` 포트
- `-e "TZ=Asia/Seoul"` Time Zone 
- `store/oracle/database-enterprise` 실행할 이미지명
- `:12.2.0.1-slim` 이미지 태그 지정

```
$ docker ps -a

CONTAINER ID   IMAGE                                            COMMAND                  CREATED         STATUS                            PORTS                              NAMES
c6b7bfe0ab73   store/oracle/database-enterprise:12.2.0.1-slim   "/bin/sh -c '/bin/ba…"   3 seconds ago   Up 2 seconds (health: starting)   0.0.0.0:1521->1521/tcp, 5500/tcp   oracle-slim
```

