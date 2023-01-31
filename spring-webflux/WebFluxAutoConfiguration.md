# WebFluxAutoConfiguration

> WebFluxAutoConfiguration 클래스는 이름에서도 알 수 있지만 WebFlux 관련 객체들을 생성하고 spring conatiner에 등록하는 객체이다.    
> WebFlux를 위해 다양한 객체들이 필요하지만 그 중에서도 가장 주목해야할 부분은 아래 그림이다. 
>
> ![image](https://user-images.githubusercontent.com/89891704/215823094-1d27f8cf-c432-4131-b04c-ba8c3a009402.png)
>
> WebFluxAutoConfiguration의 내부 클래스로 정의된 EnableWebFluxConfiguration 클래스는 부모 클래스로 WebFluxConfigurationSupport 클래스를 상속하였다.
> 그리고 WebFluxConfigurationSupport 클래스는 내부적으로 DispatcherHandler를 생성한다. 
> 
> ![image](https://user-images.githubusercontent.com/89891704/215824179-f7fd5a6b-9ef8-48c3-a273-281aefa43773.png)
>
> 뿐만 아니라 다양한 HandlerMapping 객체와 HandlerAdapter 객체, ResultHandler 등의 DispatcherHandler에 필요한 객체들을 생성한다.   
> 이렇게 생성된 다양한 객체들은 SpringApplication.run을 실행하면서 DispatcherHandler의 초기화가 시작된다.   

## DispatcherHandler의 초기화 과정

> DispatcherHandler는 ApplicationContextAware를 구현하고 있다.   
> 
> ![image](https://user-images.githubusercontent.com/89891704/215829955-9aed8206-870d-40bb-90f8-934d85c62a2c.png)
>
> 그래서 SpringApplication.run을 실행하면 ApplicationContextAwareProcessor 클래스의 invokeAwareInterfaces 메소드를 실행하게 된다. 그리고 해당 메소드에서는 
> ApplicationContextAware를 상속한 DispatcherHandler의 setApplicationContext 메소드가 실행된다.
>
> ![image](https://user-images.githubusercontent.com/89891704/215830679-b2294f4d-3e66-4b63-af54-411f3cf88772.png)
>
> DispatcherHandler의 setApplicationContext 메소드가 실행되면서 initStrategies 메소드가 실행되고 DispatcherHandler 객체를 초기화 시킨다.
>
> ![image](https://user-images.githubusercontent.com/89891704/215830930-21cb915f-e564-48a5-9bcd-f4ad7db784d7.png)
>
> ![image](https://user-images.githubusercontent.com/89891704/215831067-a3ab1f82-8993-4219-ac42-4900129692a1.png)
>
> 이렇게 초기화된 DispatchHandler는 이후 HttpHandler 객체가 생성될 때 참조되고 HttpHandler로부터의 모든 요청들은 DispatcherHandler에 의해 처리된다. 
### 다음 장에서는 HttpHandler의 생성 과정 및 내부 로직을 살펴보도록 하겠다.
