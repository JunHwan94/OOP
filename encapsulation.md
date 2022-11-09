## 캡슐화 연습1

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





