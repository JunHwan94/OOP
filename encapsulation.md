## 캡슐화 연습 1

- 캡슐화 적용 전

  ```java
  public AuthResult authenticate(String id, String pw){
    Member mem = findOne(id);
    if(mem == null) return AuthResult.NO_MATCH;
    
    if(mem.getVerificationEmailStatus() != 2){ // 조건을 판단하는 기능 캡슐화 가능
      return AuthResult.NO_EMAIL_VERIFIED;
    }
    if(passwordEncoder.isPasswordValid(mem.getPassword(), pw, mem.getId())){
      return AuthResult.SUCCESS;
    }
    return AuthResult.NO_MATCH;
  }
  ```

- 캡슐화 적용 후

  ```java
  public AuthResult authenticate(String id, String pw){
    Member mem = findOne(id);
    if(mem == null) return AuthResult.NO_MATCH;
    
  	if(!mem.isEmailVerified()){ // 캡슐화 적용
      return AuthResult.NO_EMAIL_VERIFIED;
    }
    if(passwordEncoder.isPasswordValid(mem.getPassword(), pw, mem.getId())){
      return AuthResult.SUCCESS;
    }
    return AuthResult.NO_MATCH;
  }
  ```

  ```java
  public class Member{
    private int verificationEmailStatus;
    
    public boolean isEmailVerified(){ // 기능을 캡슐화
      return verificationEmailStatus == 2;
    }
  }
  ```

## 캡슐화 연습 2

- 캡슐화 적용 전

  ```java
  public class Rental{
    private Movie movie;
    private int daysRented;
    
    public int getFrequentRenterPoints(){ // renterpoint 계산하는 기능 캡슐화 가능
  		if(movie.getPriceCode() == Movie.NEW_RELEASE && daysRented > 1){
        return 2;
      }else{
        return 1;
      }
    }
  }
  
  public class Movie{
    public static int REGULAR = 0;
    public static int NEW_RELEASE = 1;
    private int priceCode;
    
    public int getPriceCode(){
      return priceCode;
    }
    ...
  }
  ```

- 캡슐화 적용 후

  ```java
  public class Rental{
    private Movie movie;
    private int daysRented;
    
    public int getFrequentRenterPoints(){ // 캡슐화한 기능 사용
      return movie.getFrequentRenterPoints(daysRented);
    }
  }
  
  public class Movie{
    public static int REGULAR = 0;
    public static int NEW_RELEASE = 1;
    private int priceCode;
    
    public int getFrequentRenterPoints(int daysRented){ // renterpoint 계산하는 기능을 캡슐화
      if(priceCode == NEW_RELEASE && daysRented > 1){
        return 2;
      }else{
        return 1;
      }
    }
    ...
  }
  ```

## 캡슐화 연습 3

- 캡슐화 적용 전

  ```java
  Timer t = new Timer();
  t.startTime = System.currentTimeMillis();
  ...
  t.stopTime = System.currentTimeMillis();
  
  long elapsedTime = t.stopTime - t.startTime;
  ```

  ```java
  public class Time{
  	public long startTime;
  	public long stopTime;
  }
  ```

- 캡슐화 적용 후

  ```java
  Timer t = new Timer();
  t.start();
  ...
  t.stop();
  long time = t.elapsedTime(MILLISECOND);
  ```

  ```java
  public class Time{
  	public long startTime;
  	public long stopTime;
    
    public void start(){
      this.startTime = System.currentTimeMillis();
    }
    
    public void stop(){
      this.stopTime = System.currentTimeMillis();
    }
    
    public long elapsedTime(TimeUnit unit){ // 시간 계산 기능 캡슐화
      switch(unit){
        case MILLISECOND:
          return stopTime - startTime;
        case NANO:
       	...
      }
    }
  }
  ```

## 캡슐화 연습 4

- 캡슐화 적용 전

  ```java
  public void verifyEmail(String token){
    Member mem = findByToken(token);
    if(mem == null) throw new BadTokenException();
    
    if(mem.getVerificationEmailStatus() == 2){
      throw new AlreadyVerifiedException();
    }else{
      mem.setVerificationEmailStatus(2);
    }
    // DB반영
  }
  ```

- 캡슐화 적용 후

  ```java
  public void verifyEmail(String token){
    Member mem = findByToken(token);
    if(mem == null) throw new BadTokenException();
    
    mem.verifyEmail();
    // DB반영
  }
  ```

  ```java
  public class Member{
    private int verificationEmailStatus;
    
    public void verifyEmail(){ // 이메일 확인하는 기능 캡슐화
      if(isEmailVerified()){
        throw new AlreadyVerifiedException();
      }else{
        this.verificationEmailStatus = 2;
      }
    }
    
    public boolean isEmailVerified(){
      return verificationEmailStatus;
    }
  }
  ```
