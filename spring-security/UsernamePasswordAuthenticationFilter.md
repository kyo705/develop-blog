UsernamePasswordAuthenticationFilter
----------------

spring security에서 제공하는 필터 중 로그인을 확인하고 성공, 실패 여부에 따라 특정 로직을 실행하도록 작동하는 필터이다.   
아래 코드는 UsernamePasswordAuthenticationFilter가 필터체인에 의해 실행될 doFilter 메소드이다.
```java
//UsernamePasswordAuthenticationFilter의 부모 클래스인 AbstractAuthenticationProcessingFilter의 doFilter 메소드
private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		if (!requiresAuthentication(request, response)) {
			chain.doFilter(request, response);
			return;
		}
		try {
			Authentication authenticationResult = attemptAuthentication(request, response);
			if (authenticationResult == null) {
				// return immediately as subclass has indicated that it hasn't completed
				return;
			}
			this.sessionStrategy.onAuthentication(authenticationResult, request, response);
			// Authentication success
			if (this.continueChainBeforeSuccessfulAuthentication) {
				chain.doFilter(request, response);
			}
			successfulAuthentication(request, response, chain, authenticationResult);
		}
		catch (InternalAuthenticationServiceException failed) {
			this.logger.error("An internal error occurred while trying to authenticate the user.", failed);
			unsuccessfulAuthentication(request, response, failed);
		}
		catch (AuthenticationException ex) {
			// Authentication failed
			unsuccessfulAuthentication(request, response, ex);
		}
	}
```
위 코드를 살펴보면 우선, requiresAuthentication(request, response) 메소드를 통해 UsernamePasswordAuthenticationFilter가 작동해야할 urlPattern과 현재 요청한 url이 매치되는지 확인한다.
그래서 매치가 된다면 실제 로그인 시도를 한다. 반면, 등록된 urlPattern과 요청 url이 다르다면 해당 필터는 더이상 로직을 실행하지않고 다음 필터로 넘어간다.   
정상적인 로그인 요청이라면 attemptAuthentication(request, response)를 실행하게 된다.   

*attemptAuthentication메소드 코드는 아래와 같다.*

```java
public Authentication attemptAuthentication(HttpServletRequest request, HttpServletResponse response)
			throws AuthenticationException {
		if (this.postOnly && !request.getMethod().equals("POST")) {
			throw new AuthenticationServiceException("Authentication method not supported: " + request.getMethod());
		}
		String username = obtainUsername(request);
		username = (username != null) ? username : "";
		username = username.trim();
		String password = obtainPassword(request);
		password = (password != null) ? password : "";
		UsernamePasswordAuthenticationToken authRequest = new UsernamePasswordAuthenticationToken(username, password);
		// Allow subclasses to set the "details" property
		setDetails(request, authRequest);
		return this.getAuthenticationManager().authenticate(authRequest);
	}
```
attemptAuthentication메소드는 요청을 통해 Authentication 객체를 생성하고 해당 Authentication 객체를 AuthenticationManager에게 전달하여 유저 일치 여부 확인을 위임한다.   
그 후, 정상적인 Authentication 객체가 반환된다면, sessionStrategy의 onAuthentication 메소드를 통해 세션을 생성한다.   
그리고 다음 필터를 수행하러 doFilter 메소드를 실행한다. doFilter 메소드가 실행되고 돌아오면, successfulAuthentication 메소드를 호출하여 로그인 성공 후 이동할 페이지로 redirect한다.   

**참고) AuthenticationManager로 로그인 인증하는 작동 과정 간단 설명**

AuthenticationManager는 자신이 가지고 있는 AuthenticationProvider들 중 Authentication을 처리하는 provider를 선택한다.
그 후, 해당 provider에서 id와 password를 확인하고 다르다면 exception을 날려 UsernamePasswordAuthenticationFilter의 unsuccessfulAuthentication 메소드를 실행시킨다.   
자세한 Provider 작동 로직을 알고 싶다면 [**Lolsearcher**](https://github.com/kyo705/LolSearcher#lolsearcher)의 auth패키지 내부 코드를 살펴보면 도움이 될 것이다.


