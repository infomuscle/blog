# 스프링부트 @Transactional 어노테이션

## 트랜잭션이란?

- 데이터베이스의 기능을 논리적 작업으로서 하나의 단위로 묶은 집합
    - (논리적) 이체
    - (기능적) UPDATE 출금 계좌 && UPDATE 입금 계좌

## ACID
- Atomicity(원자성): 커밋 || 롤백 -> 집합의 각 기능은 모두 반영되거나, 아무것도 반영되지 않아야 한다.
- Consistency(일관성): 작업 처리 결과는 항상 일관적이어야 한다.
- Isolation(독립성): 여러 트랜잭션이 실행 중일 때 다른 트랜잭션에 간섭할 수 없다. -> 데이터베이스 락
- Durability(지속성): 트랜잭션 커밋 결과는 영구 반영되어야 한다.

### 데이터베이스 락

- ex) 100,000원 계좌에서 10,000원 출금 요청 A와 10,000원 출금 요청 B가 0.1초 간격으로 들어온다면?
        1. A가 현재 잔액 100,000원 조회
        2. B가 현재 잔액 100,000원 조회
        3. A가 10,000원 뺀 90,000원 업데이트
        4. B가 10,000원 뺀 90,000원 업데이트
        5. 남은 잔액은 90,000원? -> 이럼 안된다.

## 트랜잭션 격리 수준

데이터 정합성과 동시성의 trade-off 때문에 현실적으로는 격리 수준을 고려한다.

- Read uncommited
- Read commited
    - Problem: Unrepeatable read
- Repeatable read
    - Problem: Phantom read
- Serializable

## @Transactional