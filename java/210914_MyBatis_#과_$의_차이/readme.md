# [Java] MyBatis #과 $의 차이

![MyBatis 로고](./image1.jpeg)
<!--[##_Image|kage@cHBvVM/btreY4WvGSI/eIV9VljpVCXPi0WQKk8cc0/img.jpg|alignCenter|width="100%"|_##] -->

## 서론

약 2년 6개월 전 처음 취업을 하고 MyBatis를 사용해봤다. 데이터베이스에 접근하는 도구, 정도로만 인식을 하고 딱히 알아볼 생각을 안 했었다. 데이터베이스를 공부하겠다고 해도 오라클이나 SQL만 생각했었다.

약 6개월 전 처음 이직 면접을 봤는데, 생각도 못한 질문이 나왔다.

> MyBatis에서 #이랑 $의 차이가 뭐에요?

사실 회사에서는 바인딩을 할 때 #만 사용했었지, $를 본 적이 없어서 전혀 고민해본 적이 없었다. 면접이 끝나고 알아봐야지 하는 생각만 하고, 면접이 끝나고 알아보지 않았다.

그리고 약 한 달 뒤, 다른 기업의 면접을 보게 되었다. 첫 면접의 경험을 바탕으로 이전보단 질문에 잘 대답했었던 것 같다. 그러던 중 아차, 똑같은 질문이 또 나왔다. 아 맞다 이거 찾아보려고 했었는데.

또 약 한 달 뒤, 또 다른 기업의 면접을 보게 됐다. 다행히도 이번에는 대답을 할 수가 있었다. 올해 총 4번의 면접 중 3번을 질문받았는데, 그만큼 중요한 내용이라고 생각돼서 블로그에 가볍게 정리해본다. 서론이 길었다.

## #{variable}

```xml
<select id="getUserById" parameterType="LoginRequest" resultType="User">
  SELECT	*
  FROM		USER
  WHERE		1 = 1
  AND			USER_ID = #{userId}
  AND			USER_PW = #{userPw}
</select>
```

간단한 쿼리를 작성해봤다. 유저의 아이디와 비밀번호를 받아 유저 데이터를 조회한다. 사실 실제 유저 비밀번호는 암호화 되어있고 이렇게 단순하게 조회되진 않겠지만, 이해를 돕기 위해 간단한 모델을 세워봤다.

MyBatis는  `LoginRequest'객체에서 `userId`와 `userPw`를 꺼내와 해당하는 `#{}'영역에 값을 세팅한 쿼리를 만들어준다. 아이디가 `developer123`, 비밀번호가 `d2v!@#`이라면 MyBatis가 변환해주는 쿼리는 다음과 같다.

```sql
SELECT	*
FROM		USER
WHERE		1 = 1
AND			USER_ID = `developer123`
AND			USER_PW = `d2v!@#`
```

쿼리를 살펴보면, 변수의 값 양 옆으로 따옴표가 붙어있다. 

> #은 쿼리의 변수 양 옆으로 따옴표를 붙여준다. 

## ${variable}

```xml
<select id="getUserById" parameterType="LoginRequest" resultType="User">
  SELECT	*
  FROM		USER
  WHERE		1 = 1
  AND			USER_ID = ${userId}
  AND			USER_PW = ${userPw}
</select>
```

같은 쿼리지만 `#'대신 `$`를 사용한 쿼리를 작성해봤다.

```sql
SELECT	*
FROM		USER
WHERE		1 = 1
AND			USER_ID = developer123
AND			USER_PW = d2v!@#
```

> $는 따옴표 없이 변수를 그대로 대입한다.

그런데, 따옴표를 붙이고 안 붙이고가 어떤 다른 결과를 만들어낼까? 가장 큰 차이는 보안이라고 할 수 있다. 

## SQL Injection

 `$`을 바인딩에 사용할 경우 SQL 인젝션을 막기 어려워진다. 만약에 아이디로, `'developer123' or (1 = 1`, 비밀번호로 `'')`을 입력했다고 해보자. 변환된 쿼리는 아래와 같을 것이다.

```sql
SELECT	*
FROM		USER
WHERE		1 = 1
AND			USER_ID = `developer123` or (1 = 1
AND			USER_PW = ``)
```

위와 같은 쿼리를 쓸 경우 비밀번호 조건이 무효화 되고, 로그인이 가능해질 것이다. 아주 단적이고 단순한 예이지만, 충분히 보안상 취약점이라고 할 수 있다.

## #와 $는 언제 써야할까?

그럼 `$`는 보안에 약점이 있는데, `#`만 쓰면 안 되는걸까? 

```xml
<select id="getAllUsers" parameterType="searchCondition" resultType="User">
  SELECT	*
  FROM		USER
  WHERE		1 = 1
  ORDER BY ${column}
</select>
```

`$`는 동적인 쿼리를 만들 때 사용할 수 있다. 만약에 유저 리스트를 모두 조회하는 기능이 있다고 해보자. 그런데 `USER`테이블의 여러 컬럼 중 하나로 정렬 조건을 지정하고 싶을 경우, 위와 같이 사용할 수 있다. `#'을 사용할 경우에는 정렬 조건 파라미터 양 옆에 따옴표가 붙을 것이고, SQL 구문 오류가 날 것이다.

성능에도 차이가 있다. `#`는 `prepared statement` 를 사용한다. 파싱하면 바인딩 부분에 `?`이 붙은 쿼리가 생성이 되고, 이는 재활용 된다. 그러나 `$'를 사용하면 상수가 그대로 붙고, 이 값이 바뀔 때마다 파싱을 진행한다. 사실 이 성능도 데이터베이스 최적화에 따라 바뀔 수 있다는데, 이 부분은 좀 더 공부해서 보완해 넣어야겠다.

## 결론

얼핏 구글링 했을 때에는 간단한 내용인 줄 알았는데, 역시나 깊게 파고들 수록 짚고 넘어가야할 부분들이 있다. 오늘 정리한 내용은 아래처럼 간단히 요약할 수 있겠다.

- `#`은 양 옆에 따옴표를 붙여준다.
- `$`는 따옴표를 안 붙인다.
- `#`은 SQL 인젝션을 방지할 수 있어 보안에 강하다.
- `$`를 이용하면 쿼리를 보다 유연하게 동적으로 생성할 수 있다.
- `#`은 캐시를 이용해서 성능에 유리하다.
- `$`는 쿼리에 상수를 박아넣어, 매 번 쿼리를 파싱해야 한다.
- 데이터베이스 최적화에 따라 성능은 바뀔 수 있다는데, 추후 업데이트 예정이다.
