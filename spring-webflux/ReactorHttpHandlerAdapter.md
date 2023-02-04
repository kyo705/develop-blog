# ReactorHttpHandlerAdapter

> ReactorHttpHandlerAdapter 클래스는 WAS(Netty) 와 Spring Webflux를 연결해주는 adapter 클래스이다.   
> Netty는 servlet을 사용하지 않기 때문에 http request, response를 HttpServerRequest, HttpServerResponse 객체로 다루게 된다. 
> 이 때, 해당 객체들을 Webflux에서 사용하는 ServerHttpRequest, ServerHttpResponse 객체로 변경 후 HttpHandlerAutoConfiguration을 통해 생성된 HttpHanlder로 전달된다.   
> 
> 아래는 ReactorHttpHandlerAdapter 의 apply() 메소드이다. 
> 
> ![image](https://user-images.githubusercontent.com/89891704/216773818-6ebaa8d2-bc61-496f-9429-768c8f8d1bff.png)
> 
>  ReactorHttpHandlerAdapter에서의 Http Handler는 앞장에서 살펴본 HttpWebHandlerAdapter 객체이다. 해당 객체가 어떻게 동작하는지 궁금하다면 이전 블로그 글을 살펴보길 바란다.

### ※ Netty는 네트워크 서버 프레임워크로 http 외에도 다양한 네트워크 서버로서 역할을 수행할 수 있다.

### 이 후, 기회가 된다면 Netty에 대해 깊이 있는 글을 작성하도록 하겠다.
