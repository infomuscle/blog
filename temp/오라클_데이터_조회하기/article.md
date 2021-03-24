# [Oracle] 오라클 데이터 조회하기


## 셀렉션, 프로젝션, 조인

데이터베이스의 데이터 조회는 3가지 차원으로 나눌 수 있다.
- 셀렉션: `행 단위`로 데이터 조회
- 프로젝션: `열 단위`로 데이터 조회
- 조인: 두 개 이상의 `테이블을 연결`하여 데이터 조회


## SELECT 정리

### DESC, DESCRIBE

테이블 정보를 살펴볼 수 있다. `DESC`를 사용하여 학습에 사용할 `SCOTT`의 테이블들을 살펴보겠다.

```bash
SQL> DESC EMP;
 Name					   Null?    Type
 ----------------------------------------- -------- ----------------------------
 EMPNO					   NOT NULL NUMBER(4)
 ENAME						        VARCHAR2(10)
 JOB				    		    VARCHAR2(9)
 MGR				    		    NUMBER(4)
 HIREDATE			    		    DATE
 SAL						        NUMBER(7,2)
 COMM						        NUMBER(7,2)
 DEPTNO 					        NUMBER(2)

SQL> DESC DEPT;    
 Name					   Null?    Type
 ----------------------------------------- -------- ----------------------------
 DEPTNO     					    NUMBER(2)
 DNAME		    				    VARCHAR2(14)
 LOC				    		    VARCHAR2(13)

SQL> DESC SALGRADE;
 Name					   Null?    Type
 ----------------------------------------- -------- ----------------------------
 GRADE  						    NUMBER
 LOSAL  						    NUMBER
 HISAL  						    NUMBER
```

`DESC`를 사용해서 `EMP`, `DEPT`, `SALGRADE`의 테이블 정보를 조회했다. 테이블을 구성하는 컬럼과 컬럼의 간략한 정보를 볼 수 있다.

> [TODO]  
> 오라클 자료형 정리 후 링크 추가

> [MEMO]  
> DESC는 SQLPlus에서 지원된다. 다른 툴도 지원하는 경우가 있지만, 필수는 아니기 때문에 DESC 명령어를 사용할 수 없다면 SQLPlus에 접속해서 사용한다.

### SELECT, FROM

```sql
```

### DISTINCT

```sql
```

### AS

### ORDER BY
