**BcryptPasswordEncoder**
-------------

해당 클래스는 여러 해시 알고리즘 중 Bcrypt 알고리즘을 사용하여 사용자 입력 값을 암호화하는 클래스이다.   

Bcrypt 알고리즘은 다른 해시 알고리즘(sha1, md5 ...)보다 속도가 느리다. 그래서 해당 알고리즘을 사용한다면 해커가 rainbow table을 생성하는데 매우 오래 걸리게 되어 brute-force 공격이 불가능해진다.
Bcrypt 동작 방식은 난수(salt)을 임의의 횟수 만큼 반복적으로 해싱하는 것이다.   
```
String encoding_password = raw_password;

while(임의의 횟수){
  encoding_password = hash(salt + encoding_password);
}

db.save(encoding_password);
```

그렇다면 반복 횟수와 난수값이 같은 비밀번호는 같은 해시값은 가지는가? => YES   

BcryptPasswordEncoder의 난수값과 반복횟수는 고정되어있는가? => NO 

그렇다면 난수값이 매번 바뀌는 과정에서 어떻게 plain값과 해시된 값이 일치하는지 구분할 수 있는가? => BcryptPasswordEncoder에서 제공하는 matches메소드를 사용한다.    

**matches 함수의 동작 원리**   

파라미터로 plain값과 encoding 값을 넘겨주면 해당 encoding 된 값에서 salt 값을 찾아서 plain 값에 salt 값을 더해 hash하여 encoding 한다. 그리고 그 결과 값을 파라미터로 전달받은 encoding 값과 비교하여 같으면 true, 다르면 false로 일치 여부를 판별한다.   


느낀점...   
처음엔 salt 값이 매번 바뀌면 어떻게 비밀번호 판별을 하지? 라는 생각이 있었다. 그래서 당연히 솔트 값과 반복 횟수가 고정되는줄 알았고 실제 document에서 생성자에서 솔트 값과 반복 횟수를 지정할 수 있어서 당연히 고정인 줄 알았다. 하지만 매번 인코딩 되는 값이 달라지는 것을 확인하고 잘못되었다는 것을 알게되었다. 그래서 실제 encoding 값과 plain 값을 비교하는 matches 메소드 내부 코드를 살펴보았고 그 과정에서 시간은 걸렸지만 정확히 어떻게 동작하게 되는지 알 수 있었다.   

결론   
BcryptPasswordEncoder을 통해 비밀번호를 암호화한다면 매번 다른 해시값을 생성하게 된다!!
