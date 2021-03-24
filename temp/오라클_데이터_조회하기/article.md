# [Oracle] 오라클 데이터 조회하기


## DESC, DESCRIBE

테이블 정보를 살펴볼 수 있다.

```bash
SQL> DESC EMP;
 Name					   Null?    Type
 ----------------------------------------- -------- ----------------------------
 EMPNO					   NOT NULL NUMBER(4)
 ENAME						        VARCHAR2(10)
 JOB						        VARCHAR2(9)
 MGR						        NUMBER(4)
 HIREDATE					        DATE
 SAL						        NUMBER(7,2)
 COMM						        NUMBER(7,2)
 DEPTNO 					        NUMBER(2)
```
