# HttpHandlerAutoConfiguration


> HttpHandlerAutoConfiguration는 WebFluxAutoConfiguration클래스가 적용되고 실행되는 클래스이다.   
> 그리고 DispatcherHandler와 HttpHandler 클래스(Bean이 아님)가 존재해야 실행되는 클래스이다.  
> 
> ![image](https://user-images.githubusercontent.com/89891704/216352242-45af3e7f-d5e1-4425-a44e-5d7b90b02e85.png)
>
> 내부적으로 HttpHandler 객체를 생성해 빈으로 등록한다. 해당 HttpHandler를 통해 WAS(Netty)에서 전달하는 HttpServerRequest, HttpSeverResponse 객체들을 
> Webflux에서 제공하는 ServerHttpRequest, ServerHttpResponse 객체로 변환하고 exchange 객체를 생성 후 실제 요청을 처리하게 된다. 결국 Webflux의 가장 핵심이 되는 컴포넌트이다.   
> 
> ![image](https://user-images.githubusercontent.com/89891704/216352972-12e3c4ed-826d-462e-9c1c-359d7da52f65.png)
> 
> 그렇다면 HttpHandler가 생성되는 과정을 살펴보자

## WebHttpHandler가 생성되는 과정

> WebHttpHandlerBuilder의 static 메소드인 applicationContext()를 통해 WebHttpHandlerBuilder 객체가 생성된다. 이 때, 스프링 컨테이너에 등록된 여러 빈들(WebFliter, WebHandler, sessionManager, forwardedHeaderTransformer 등) 이 WebHttpHandlerBuilder의
> 필드에 등록된다.   
> 
> 그 후, build() 메소드가 실행되면 우선 DispatcherHandler와 WebFliter들로 FilteringWebHandler 객체를 생성한다.   
> 그 후 FilteringWebHandler를 래핑하여 ExceptionHandlingWebHandler 객체를 생성한다.   
> 마지막으로 ExceptionHandlingWebHandler를 래핑해 HttpHandler 객체(구현체 : HttpWebHandlerAdapter)를 생성한다.
> 이렇게 생성된 HttpHandler 객체에 Builder에서 등록한 여러 컴포넌트들을 등록하고 HttpHandler 객체를 리턴하게 된다. 
> 
>![image](https://user-images.githubusercontent.com/89891704/216356311-2bfb912c-21a7-4690-908a-9601f21bad7d.png)
> 
> 구조를 도식화해서 살펴보면 다음과 같다.
> 
> ![image](https://user-images.githubusercontent.com/89891704/216359446-55de734b-9dac-4ea5-b99e-99956632bef3.png)

## 다음 장에서는 Netty와 Webflux를 연결해주는 컴포넌트에 대해 살펴보도록 하겠다.
