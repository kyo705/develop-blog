AuthenticationManager가 PasswordEncoder를 등록하는 과정
------------------------
AuthenticationManager는 UsernamePasswordAuthenticationFilter나 BasicAuthenticationFilter 등에서 인증을 위해 사용된다. 
이 때, AuthenticationManager에서 Encoder를 통해 암호화된 password를 확인하는데 어떻게 Encoder를 등록하는지 살펴보자   

AuthenticationManager는 AuthenticationManagerBuilder를 통해 생성되는데 AuthenticationManagerBuilder가 빈으로 등록되는 과정은 아래와 같다.
```java
//AuthenticationConfiguration 클래스의 authenticationManagerBuilder 메소드
@Bean
	public AuthenticationManagerBuilder authenticationManagerBuilder(ObjectPostProcessor<Object> objectPostProcessor,
			ApplicationContext context) {
		LazyPasswordEncoder defaultPasswordEncoder = new LazyPasswordEncoder(context);
		AuthenticationEventPublisher authenticationEventPublisher = getBeanOrNull(context,
				AuthenticationEventPublisher.class);
		DefaultPasswordEncoderAuthenticationManagerBuilder result = new DefaultPasswordEncoderAuthenticationManagerBuilder(
				objectPostProcessor, defaultPasswordEncoder);
		if (authenticationEventPublisher != null) {
			result.authenticationEventPublisher(authenticationEventPublisher);
		}
		return result;
	}
```

LazyPasswordEncoder 메소드를 통해 @bean으로 등록한 BCryptPasswordEncoder가 LazyPasswordEncoder의 필드 값으로 주입된다. 그 과정은 아래의 코드에 나와있다.
```java
//LazyPasswordEncoder 클래스의 getPasswordEncoder 메소드
private PasswordEncoder getPasswordEncoder() {
			if (this.passwordEncoder != null) {
				return this.passwordEncoder;
			}
			PasswordEncoder passwordEncoder = getBeanOrNull(this.applicationContext, PasswordEncoder.class);
			if (passwordEncoder == null) {
				passwordEncoder = PasswordEncoderFactories.createDelegatingPasswordEncoder();
			}
			this.passwordEncoder = passwordEncoder;
			return passwordEncoder;
		}
```
getBeanOrNull 메소드를 통해 application에 등록된 PasswordEncoder 타입의 인스턴스를 LazyPasswordEncoder의 PasswordEncoder 필드 값으로 등록한다. 
그래서 @bean으로 BCryptPasswordEncoder를 application에 등록하면 security의 default encoder로 사용할 수 있다.  
그리고 Encoder는 AuthenticationManagerBuilder의 필드 값에 등록된다. 그 후, AuthenticationManagerBuilder를 통해 AuthenticationManager가 만들어진다.
그래서 AuthenticationManager의 provider에서 password를 증명할 때, 등록된 Encoder의 matches메소드를 사용해 확인한다.   

이렇게 만들어진 AuthenticationManager는 UsernamePasswordAuthenticationFilter의 setter 메소드를 통해 필터에 등록되어 사용된다.

