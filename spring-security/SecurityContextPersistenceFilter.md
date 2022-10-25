SecurityContextPersistenceFilter
-----------------

**SecurityContextPersistenceFilter**는 spring security가 기본으로 제공하는 필터로 SecurityContextRepository로부터 SecurityContext를 받아 SecurityContextHolder에 저장하는 필터이다.  
SecurityContextHolder에 SecurityContext를 저장하는 이유는 SecurityContextHolder는 스레드로컬이므로 실행 스레드에 어느 곳에서든지 SecurityContext에 접근할 수 있다.
```java
//SecurityContextPersistenceFilter의 doFilter 메소드
private void doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain)
			throws IOException, ServletException {
		// ensure that filter is only applied once per request
		if (request.getAttribute(FILTER_APPLIED) != null) {
			chain.doFilter(request, response);
			return;
		}
		request.setAttribute(FILTER_APPLIED, Boolean.TRUE);
		if (this.forceEagerSessionCreation) {
			HttpSession session = request.getSession();
			if (this.logger.isDebugEnabled() && session.isNew()) {
				this.logger.debug(LogMessage.format("Created session %s eagerly", session.getId()));
			}
		}
		HttpRequestResponseHolder holder = new HttpRequestResponseHolder(request, response);
		SecurityContext contextBeforeChainExecution = this.repo.loadContext(holder);
		try {
			SecurityContextHolder.setContext(contextBeforeChainExecution);
			if (contextBeforeChainExecution.getAuthentication() == null) {
				logger.debug("Set SecurityContextHolder to empty SecurityContext");
			}
			else {
				if (this.logger.isDebugEnabled()) {
					this.logger
							.debug(LogMessage.format("Set SecurityContextHolder to %s", contextBeforeChainExecution));
				}
			}
			chain.doFilter(holder.getRequest(), holder.getResponse());
		}
		finally {
			SecurityContext contextAfterChainExecution = SecurityContextHolder.getContext();
			// Crucial removal of SecurityContextHolder contents before anything else.
			SecurityContextHolder.clearContext();
			this.repo.saveContext(contextAfterChainExecution, holder.getRequest(), holder.getResponse());
			request.removeAttribute(FILTER_APPLIED);
			this.logger.debug("Cleared SecurityContextHolder to complete request");
		}
	}
```
SecurityContextRepository는 SecurityContext를 보관하는 인터페이스로 spring security는 default로 해당 구현체 HttpSessionSecurityContextRepository를 제공한다. 
해당 구현체는 세션에 세션ID와 SecurityContext를 key-value로 저장하고 로드하는 역할을 한다.

```java
//HttpSessionSecurityContextRepository의 loadContext 메소드
public SecurityContext loadContext(HttpRequestResponseHolder requestResponseHolder) {
		HttpServletRequest request = requestResponseHolder.getRequest();
		HttpServletResponse response = requestResponseHolder.getResponse();
		HttpSession httpSession = request.getSession(false);
		SecurityContext context = readSecurityContextFromSession(httpSession);
		if (context == null) {
			context = generateNewContext();
			if (this.logger.isTraceEnabled()) {
				this.logger.trace(LogMessage.format("Created %s", context));
			}
		}
		SaveToSessionResponseWrapper wrappedResponse = new SaveToSessionResponseWrapper(response, request,
				httpSession != null, context);
		requestResponseHolder.setResponse(wrappedResponse);
		requestResponseHolder.setRequest(new SaveToSessionRequestWrapper(request, wrappedResponse));
		return context;
	}
```

세션에 SecuritContext를 저장하지 않고 상황에 맞게 SecurityContextRepository를 구현하면 레디스나 사용자가 원하는 곳에 저장하고 로드할 수 있다. 

