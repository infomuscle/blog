# 스프링부트 @Transactional 어노테이션

스프링의 `@Transactional` 어노테이션, 언제 커밋이 되고 롤백이 되는가. 중요한 것도 알고 개념도 알겠는데 와닿지가 않아서 직접 테스트 후 정리해봤다.

## Checked vs Unchecked

Checked 익셉션과 Unchecked 익셉션이 무엇인지 알아보기보단, `@Transactional`이 어떤 경우에 롤백을 수행하는지 확인하기 위한 목적이다. 익셉션의 종류에 대한 설명은 생략한다.

트랜잭션에 대한 내용을 찾아볼 때 가장 흔히 드는 예시가 은행 업무다. 돈만큼 정확해야 하고 오류가 나면 안 되는 케이스도 잘 없기 때문. 오직 어떤 경우에 DB에 커밋이 되고, 롤백이 되는지 확인하기 위한 아주 간단한 서비스를 만들어보았다. 3개의 클래스로 이루어져있다.

1. AccountService
    - 계좌 생성, 계좌 조회, 이체 로직이 아주 심플하게 구현되어 있다.
    - 이체는 송금인 잔액 업데이트, 수취인 잔액 업데이트 메소드가 순서대로 실행된다.
2. Account
    - 아이디와 잔액만 가지고 있다.
3. AccountRepository
    - JPARepository를 구현한다.


### AccountService

#### 계좌 생성 && 계좌 조회

```java
/**
 * 신규 계좌 생성
 */
public Account openAccount(Account account) {
    return accountRepository.save(account);
}

/**
 * 계좌 조회
 */
public Account getAccount(Long accountId) {
    return accountRepository.findById(accountId).get();
}
```

계좌 생성은 Account 객체를 ACCOUNT 테이블에 삽입한다. 중복 체크 등은 생략했다. 계좌 조회는 계좌 ID로 객체를 가져온다. 마찬가지로 Null 체크 등은 생략한다.

#### 이체

```java
/**
 * 이체
 */
@Transactional
public Map<String, Account> transfer(Long senderId, Long receiverId, Long amount) throws Exception {

    Map<String, Account> result = new HashMap<String, Account>();
    result.put("sender", updateSender(senderId, amount));
    result.put("receiver", updateReceiver(receiverId, amount));
    log.info("Transfer Result: {}", result);

    return result;
}
```

이체도 아주 단순하게 구현되어 있다. 먼저 송금인 계좌에서 요청 금액만큼 잔액을 차감한다. 그리고 수취인 계좌에 요청 금액만큼 잔액을 더한다. 변경된 상태의 계좌 정보를 맵에 담에 반환한다.

여기서 이체는 DB의 상태가 변경된다. 그리고 처리 결과는 항상 정확해야 한다. 그래서 `@Transactional`로 묶어줘야 한다. 근데 여기서 궁금증이 생긴다.

1. 만약 송금인 업데이트 전에 예외가 발생한다면?
    - DB에는 아무 일도 발생하지 않을테니 문제가 없다.
2. 송금인 업데이트 후, 수취인 업데이트가 완료되기 전에 예외가 발생한다면?
    - 송금인 업데이트 작업 내역 또한 무효가 되어야 한다.
    안 그러면 돈 보낸 사람은 있는데, 받은 사람은 없는 상황이 발생한다.
3. 수취인 업데이트까지 완료된 후 결과를 리턴하기 전에 예외가 발생한다면?
    - 예를 들어 각각의 업데이트 내용을 바탕으로 이체 결과를 따로 기록하는 테이블이 있다고 해보자.
    - 보내고 받고 다 했어도, 기록이 없으면 나중에 문제 소지가 있다.

#### 송금인과 수취인 잔액 업데이트
```java
/**
 * 송금인 잔액 업데이트
 */
private Account updateSender(Long senderId, Long amount) throws Exception {

    log.info("Sender ID: {}", senderId.toString());

    Optional<Account> sender = accountRepository.findById(senderId);
    sender.ifPresentOrElse(s -> {
        s.setAmount(s.getAmount() - amount);
        s = accountRepository.save(s);
    }, () -> {
        throw new RuntimeException("No Sender Found");
    });

    return sender.get();
}

/**
 * 수취인 잔액 업데이트
 */
private Account updateReceiver(Long receiverId, Long amount) throws Exception {

    // 예상못한 NullPointerException 테스트
    log.info("Receiver ID: {}", receiverId.toString());

    // 의도적인 Unchecked Exception 테스트
    if (receiverId == 777L) {
        throw new RuntimeException("Intentional RuntimeException");
    }

    // 의도적인 Checked Exception 테스트
    if (receiverId == 555L) {
        throw new ClassNotFoundException("Intentional ClassNotFoundException");
    }

    Optional<Account> receiver = accountRepository.findById(receiverId);
    receiver.ifPresentOrElse(r -> {
        r.setAmount(r.getAmount() + amount);
        r = accountRepository.save(r);
    }, () -> {
        throw new RuntimeException("No Receiver Found");
    });

    return receiver.get();
}
```

각각 송금인 ID와 수취인 ID, 그리고 요청 금액을 받는다. ID로 계좌를 조회하고, 요청 금액만큼 잔액을 가감한 후 DB를 업데이트 한다. 조회 결과가 없으면 예외를 리턴한다. 그런데 수취인 처리 메소드는 테스트 목적으로 몇 줄 더 추가했다. 어떤 테스트를 위한건지는 주석에 나와있다.


> Account와 AccountRepository는 생략한다.  
> Account는 Long 자료형의 id와 amount 필드만 있다.  
> AccountRepository에 다른 메소드를 추가하진 않았다.


### 테스트 코드


```java
@Test
@DisplayName("이체: 정상")
public void _11_testTransferTransaction() throws Exception {

    Account sender = accountService.openAccount(new Account(100000L));
    Account receiver = accountService.openAccount(new Account(0L));

    Map<String, Account> result = accountService.transfer(sender.getId(), receiver.getId(), 50000L);

    assertThat(result.get("sender").getAmount()).isEqualTo(50000L);
    assertThat(result.get("receiver").getAmount()).isEqualTo(50000L);
}

@Test
@DisplayName("이체: 송금인 업데이트 후 예상못한 Unchecked Exception")
public void _12_testTransferTransaction2() {

    Account sender = accountService.openAccount(new Account(100000L));
    Account receiver = accountService.openAccount(new Account(0L));

    Exception e = assertThrows(Exception.class, () -> {
        accountService.transfer(sender.getId(), null, 50000L);
    });

    assertThat(e.getClass().getSimpleName()).isEqualTo("NullPointerException");
    assertThat(accountService.getAccount(sender.getId()).getAmount()).isEqualTo(100000L);
    assertThat(accountService.getAccount(receiver.getId()).getAmount()).isEqualTo(0L);
}

@Test
@DisplayName("이체: 송금인 업데이트 후 의도적인 Unchecked Exception")
public void _13_testTransferTransaction3() {

    Account sender = accountService.openAccount(new Account(100000L));
    Account receiver = accountService.openAccount(new Account(0L));

    Exception e = assertThrows(Exception.class, () -> {
        accountService.transfer(sender.getId(), 777L, 50000L);
    });

    assertThat(e.getMessage()).isEqualTo("Intentional RuntimeException");
    assertThat(accountService.getAccount(sender.getId()).getAmount()).isEqualTo(100000L);
    assertThat(accountService.getAccount(receiver.getId()).getAmount()).isEqualTo(0L);
}

@Test
@DisplayName("이체: 송금인 업데이트 후 의도적인 Checked Exception")
public void _14_testTransferTransaction4() {

    Account sender = accountService.openAccount(new Account(100000L));
    Account receiver = accountService.openAccount(new Account(0L));

    Exception e = assertThrows(Exception.class, () -> {
        accountService.transfer(sender.getId(), 555L, 50000L);
    });

    assertThat(e.getMessage()).isEqualTo("Intentional ClassNotFoundException");
    assertThat(accountService.getAccount(sender.getId()).getAmount()).isEqualTo(50000L);
    assertThat(accountService.getAccount(receiver.getId()).getAmount()).isEqualTo(0L);
}
```

테스트는 다음 4가지 상황을 일차적으로 구현했다.

1. 정상적으로 이체 완료
2. 로직 수행 중 예상치 못하게 발생한 Unchecked Exception 
    - null.toString()을 수행하도록 유도되어 있다.
3. 의도적으로 발생시킨 Unchecked Exception
    - 수취인 ID가 777일 때 발생하도록 했다.
4. 의도적으로 발생시킨 Checked Exception
    - 수취인 ID가 555일 때 발생하도록 했다.

모든 케이스는 처음에 10만원의 잔액이 있는 송금인과 0원 잔액의 수취인 계좌를 생성한 후, 5만원을 입금하는 상황이다. 비즈니스적으로 봤을 때 각 케이스의 결과는 이체 메소드 수행 후 각 계좌의 잔액은 다음과 같아야 한다. 

1. 송금인 5만원, 수취인 5만원
2. 송금인 10만원, 수취인 0원 
3. 송금인 10만원, 수취인 0원 
4. 송금인 10만원, 수취인 0원 

그런데 각 테스트 메소드의 assertThat()에서 예상하는 결과는 아래와 같다.

1. 송금인 5만원, 수취인 5만원
2. 송금인 10만원, 수취인 0원 
3. 송금인 10만원, 수취인 0원 
4. 송금인 5만원, 수취인 0원 <-- 중요!! 
