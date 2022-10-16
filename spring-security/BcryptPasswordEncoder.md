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
