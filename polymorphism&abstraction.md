# 다형성

- 여러 모습을 갖는 것
- 객체지향에서는 한 객체가 여러 타입을 갖는 것
  - 한 객체가 여러 타입의 기능을 제공
  - 타입 상속으로 다형성 구현

## 다형성 예

```java
public class Timer{
	public void start(){...}
	public void stop(){...}
}

public interface Rechargable{
	void charge();
}
```

```java
public class IotTimer extends Timer implements Rechargeable{
	public void charge(){
		...
	}
}
```

```java
IotTimer it = new IotTimer();
it.start();
it.stop();

Timer t = it;
t.start();
t.stop();

Rechargeable r = it;
r.charge();
```

IotTimer는 Timer, Rechargeable 타입에 할당 가능

# 추상화

- 데이터나 프로세스 등을 의미가 비슷한 개념이나 의미있는 표현으로 정의하는 과정
- 두가지 방식의 추상화
  - 특정한 성질, 공통 성질(일반화)
- 예
  - DB의 USER 테이블 (아이디, 이름, 이메일)
  - GPU (지포스, 라데온)

### 서로 다른 구현 추상화

- 'SCP로 파일 업로드, HTTP로 데이터 전송, DB 테이블에 삽입' 작업이 푸시를 보내기 위한 구현이었다면 '푸시 발송 요청'으로 추상화 가능

### 타입 추상화

- 여러 구현 클래스를 대표하는 상위 타입 도출
  - 흔히 인터페이스 타입으로 추상화
  - 추상화 타입과 구현은 타입 상속으로 연결
- 인터페이스는 기능에 대한 의미 제공, 구현은 제공하지 않음, 어떻게 구현할지 모름
- 실제 구현은 추상타입을 상속한 클래스에서 제공 (콘크리트 클래스)

### 추상 타입 사용

- 추상 타입을 이용한 프로그래밍

  ```java
  Notifier notifier = getNotifier(...);
  notifier.notify(someNoti);
  ```

- 추상 타입은 구현을 감춤
  - 기능 구현이 아닌 의도를 더 잘드러냄

### 추상 타입 사용에 따른 이점 : 유연함

- 콘크리트 클래스 직접 사용

  ```java
  private SmsSender smsSender;
  public void cancel(String ono){
  	... 주문 취소 처리
    smsSender.sendSms(...);
  }
  ```

  ```java
  private SmsSender smsSender;
  private KakaoPush kakaoPush;
  
  public void cancel(String ono){
    ... 주문 취소 처리
    if(pushEnabled){
      kakoPush.push(...);
    }else{
      smsSender.sendSms(...);
    }
  }
  ```

  ```java
  private SmsSender smsSender;
  private KakaoPush kakaoPush;
  private MailService mailSvc;
  
  public void cancel(String ono){
    ... 주문 취소 처리
    if(pushEnabled){
      kakoPush.push(...);
    }else{
      smsSender.sendSms(...);
    }
  }
  mailSvc.sendMail(...);
  ```

  요구사항 변경에 따라 주문 취소 코드도 함께 변경된다

- 공통점을 도출하면, 도출한 추상 타입 사용

  'sms 전송, 카카오톡 전송, 이메일 전송' 추상화 -> 통지

  ```java
  public void cancel(String ono){
  	... 주문 취소 처리
    Notifier notifier = getnotifier(...);
  	notifier.notify(...);
  }
  
  private Notifier getNotifier(...){
    // 상황에 따라 알맞은 Notifier 반환
    if(pushEnabled){
      return new KakaoNotifier();
    }else{
      return new SmsNotifier();
    }
  }
  ```

- 사용할 대상 접근도 추상화

  ```java
  public void cancel(String ono){ // 통지 방식이 바뀌어도 cancel메서드는 바뀌지 않음
  	... 주문 취소 처리
  	Notifier notifier = NotifierFactory.instance().getNotifier(...);
  	notifier.notify(...);
  }
  
  public interface NotifierFactory{
  	Notifier getNotifier(...);
  	
  	static NotifierFactory instance(){
  		return new DefaultNotifierFactory();
  	}
  }
  
  public class DefaultNotifierFactory implements NotifierFactory{
    public Notifier getNotifier(...){
      if(pushEnabled) {
        return new KakaoNotifier();
      }else{
        return new SmsNotifier();
      }
    }
  }
  ```

  추상화를 통해 주문 취소 로직은 변경하지 않고 통지하는 방식을 바꿀 수 있는 유연함을 가짐 -> 추상화를 하는 이유

### 추상화는 의존 대상이 변경하는 시점에 수행

- 추상화 -> 추상 타입 증가 -> 복잡도 증가
  - 아직 존재하지 않는 기능에 대한 추상화 주의 -> 잘못된 추상화를 할 가능성이 있고 복잡도 증가
  - 실제 변경, 확장이 발생할 때 추상화시도

 **추상화를 할 때는 구현한 이유가 무엇인지 생각해야 한다**

## 추상화 여부에 따른 차이

### 추상화 하지 않을 때

- 코드가 길어지고 복잡해짐
- 관련 코드가 여러 메서드에 분산
- -> 코드 가독성, 분석속도 저하, 개발 시간 증가

### 추상화 할 때

- 추상화한 타입으로만 핵심 기능 구현 가능
- 추상타입 사용 부분의 코드 수정없이 새로운 타입 추가 가능
- -> OCP를 잘 지키는 코드 작성 가능