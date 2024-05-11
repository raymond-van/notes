# Spring
#### Spring Container
- heart of the spring framework
- responsible for creating, configuring, and assembling beans (app components)
- receive instructions through XML config files which tell the Spring container what beans to create and how to wire them together
	 - xml files can be loaded into the appContext
	 - you can then get the bean through the appContext
#### Spring Bean
- Simply a java object (an instance of a java class) that make up components of our application and is managed by Spring
- Spring is responsible for creating, initializing, wiring together and managing the lifecycle of these beans
- Created using XML config files or using annotations like @Component (autowiring)
- can easily replace a bean without affecting the rest of the app
##### Autowiring
- Spring automatically discovers dependencies to inject, reducing the amount of xml you need to write
- ![](Pasted%20image%2020240325185049.png)
- **`context:annotation-config`:** This is essential to activate the processing of Spring annotations like `@Service` and `@Autowired`.
- **`context:component-scan`**: Instructs Spring to scan the `com.example` package and automatically discover classes annotated with `@Component`, `@Service`, `@Repository`, etc., turning them into Spring beans.
#### Dependency Injection
- Design pattern where a class doesn't actually create the objects it needs, but instead receives them externally
- Spring figure out what beans a class depends on and automatically injects those dependencies when it creates the bean
- Constructor injection - injects dependencies through the constructor
- Setter injection - injects dependencies through setter methods
#### Inversion of Control
- Whenever you delegate certain tasks to a framework, i.e., letting Spring control how things are created and wired together for our convenience
- Dependency injection is IoC
#### MVC
- enables separate of concerns
![](Pasted%20image%2020240325200839.png)
##### Model
- represents the data
##### View
- presents the data to the user, typically through JSP or velocity templates (server-side rendering)
##### Controller
- connects model and view together
- handles incoming user requests
	- interacts with the model to get or manipulate the data
	- then instructs the view on how to present the data
- the heart of Spring MVC and are implemented as Java classes with annotations
#### XML vs Annotation-based configuration
##### XML
- + Central location to manage beans and wiring
- + No recompilation
- - Verbose
- - Less intuitive if you're used to OOP, XML is a bit unnnatural
##### Annotation
- + Concise
- + Safer as compiler can catch errors
- + Can see configuration immediately within the relevant classes
- - Scattered across multiple files
#### Annotations
- @Bean: Designates a method as responsible for producing a bean to be managed by the Spring container.
- @Component: A generic annotation indicating that a class serves as a Spring component.
	- a Component is just a spring bean
- @Service: Specializes `@Component` for classes representing a service layer.
	- service layer contains business logic
	- bridge between controllers and data access layers (repositories)
- @Repository Specializes `@Component` for classes focused on data access.
- @Controller: Specializes `@Component` for classes that handle web requests.
- @Autowired: Enables automatic dependency injection into fields, constructor parameters, or setter methods.
- @Value: Injects values from properties files or other sources into fields.
- @Configuration: Indicates a class as a source of bean definitions for the Spring container.
- @ComponentScan: Directs Spring to scan for components within specific packages.
- @Import: Allows importing other configuration classes.
#### Microservices
- small, self-contained, ready to run apps that are independent of each other
- Preferable over monoliths because it can be easier to maintain, improved productivity
#### Serverless
- relies on cloud to let you focus on logic rather than infrastructure (scaling, runtime envs, resource alloc, security)
# Spring Boot
#### Auto-configuration
- analyzes your project dependencies and sets up sensible defaults for a wide range of common configurations
- Database Connectivity: If you include a JPA library (e.g., Hibernate), Spring Boot will try to configure a basic datasource connection based on your environment variables or application properties.
- Embedded Servers: Spring Boot ships with several embedded servers like Tomcat or Jetty. By default, it will set up and run one of these for your application, eliminating the need for a separate server in development.
- Spring MVC: If you include web-related libraries, Spring Boot will configure a basic Spring MVC application context, including features like automatic view resolution and exception handling
![](Pasted%20image%2020240327220815.png)
