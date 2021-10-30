# [Database] 실행계획과 인덱스



## 실행계획이란

실행계획은 데이터베이스가 SQL을 실행하려 데이터를 추출할 때 옵티마이저가 수립하는 작업 절차이다. 



### 해석 순서

- 트리의 가장 좌측 아래부터 역순으로 해석



## 스캔

스캔은 데이터를 읽는 작업을 말한다. 그리고 가장 효율적으로 읽기 위한 전략이 있는데, 특히 아래 3가지 방법이 중요하다.

- FULL TABLE SCAN
- ROWID SCAN
- INDEX SCAN

각각 다음과 같은 경우에 해당 전략을 사용한다.



### FULL TABLE SCAN

항상 FULL TABLE SCAN이 나쁜 것은 아니다. 테이블에 데이터가 적어서 인덱스를 타는 시간보다 그냥 다 훑어버리는 게 빠를 수도 있다. 또는 추출 데이터가 엄청 많아도 FULL TABLE SCAN이 유리할 수도 있다.

- 조건절의 컬럼에 인덱스가 없는 경우
- 조건절의 컬럼에 인덱스가 있지만, 해당 데이터가 많아 FULL TABLE SCAN이 낫다고 옵티마이저가 판단하는 경우
- 조건절의 컬럼에 인덱스가 있지만 테이블의 데이터가 적어 FULL TABLE SCAN이 낫다고 옵티마이저가 판단하는 경우
- 테이블 생성시 DEGREE 속성 값이 크게 설정되어 있는 경우



### ROWID SCAN

단일행 접근이 매우 빠르다. ROWID SCAN을 탈 수 있는 상황이라면 사용하는 것이 좋다.

- 조건절에 ROWID를 직접 명시하는 경우
- INDEX SCA을 통해 ROWID를 추출한 후 테이블에 접근할 경우



### INDEX SCAN

- INDEX UNIQUE SCAN
  - UNIQUE INDEX를 구성하는 모든 컬럼이 조건에 "="로 명시된 경우
  - ex)
- INDEX RANGE SCAN
  - UNIQNE 성격의 결합 인덱스의 선두 컬럼이 WHERE절에 사용되는 경우
  - 일반 인덱스의 컬럼이 WHERE절에 존재하는 경우
- INDEX RANGE SCAN DESCENDING
  - INDEX RANGE SCAN + ORDER BY DESC
- INDEX SKIP SCAN
  - 결합 인덱스의 선행 컬럼이 WHERE절에 포함된 경우
  - 옵티마이저가 FULL TABLE SCAN보다 낫다고 판단하는 경우
- INDEX FULL SCAN
  - ORDER BY / GROUP BY의 모든 컬럼이 인덱스의 전체, 또는 일부로 정의된 경우
  - 정렬이 필요한 명령에서 INDEX ENTRY를 순차적으로 읽는 방식으로 처리된 경우
- INDEX FULL SCAN DESCENDING
  - INDEX FULL SCAN + ORDER BY DESC
- INDEX FAST FULL SCAN
  - FULL TABLE SCAN을 하지 않고도 INDEX FAST FULL SCAN으로 원하는 데이터를 추출할 수 있고 추출된 데이터의 정렬이 필요 없으며 결합 인덱스를 구성하는 컬럼 중에 최소 한 개 이상은 NOT NULL인 경우
- INDEX JOIN
  - 추출하고자 하는 데이터가 조인하는 인덱스에 모두 포함되어 있고 추출하는 데이터의 정렬이 필요없는 경우