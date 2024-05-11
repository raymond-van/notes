# Java
#### Java SE 
- "Core Java"
- General purpose programming language with basic APIs
#### JDK
- Devs need the JDK, which includes the JRE (which contains JVM) + dev tools (compiler, debugger, etc.)
- Contains archiver (jar) which is a tool for creating, manging, and extracting JAR (Java Archive) files
##### JAR file
- JAR files bundle together Java class files (.class), resources (images, config files) and metadata into a single file, typically used to package libraries
	- Can be thought of as a Zip file for Java
	- Contains Manifest file: META-INF/MANIFEST.MF
		- Metadata defines how to use the JAR
		- Specifies the main class to execute if the JAR is an executable app
			- Can run the class like so: `java -jar myapp.jar`
		- Specifies the Classpaths to other JAR dependencies
		- Classpaths:
			- a list of locations that the JVM searches for the classes and resources needed to run your java app 
			- including JAR files in your classpath is how you utilize external libraries in your java projects
##### WAR file
- bundles and deploys all the necessary components of your Java **app** in the J2EE ecosystem
- makes it easy to deploy web app onto web servers like Tomcat or Jetty
- contains static HTML, images, CSS, javascript files
- Contains `WEB-INF` directory
	- contains `web.xml` which defines servlets, url mappings
	- `/classes` java .classes 
	- `/lib` JAR files containing libraries
##### EAR file
- standard way to package java **enterprise** apps into a single, deployable unit
- can contain WARs, JARs
- contains META-INF directory which contains:
	- `application.xml` which serves as the deployment descriptor, which:
		- Lists Modules: Specifies the WAR, JAR, and client modules included in the EAR.
		- Defines Dependencies: Describes how modules within the EAR depend on each other.
		- Security Configuration: Can include security roles and constraints.
- Deployment: EAR files simplify deployment of multi-tiered or multi-module enterprise applications by delivering everything in one package.
- Management: Allows you to manage the lifecycle of all components within a complex application as a cohesive unit.
#### JRE
- Software bundle that includes everything needed to **run** Java applications
- Contains the JVM
- Contains the standard Java libraries (java.lang, java.util)
- Contains basic (command-line) tools for running Java programs
- What most users install to **run** Java applications
#### JVM
- "Write once, run anywhere" portability enabled by JVMs
- Reads Java bytecode (.class files) and translate it into machine instructions specific for the underlying hardware
	- Executed by the "runtime system" (not JRE)
- Manages memory (garbage collecting), handles threading, and 
- E.g.: Oracle, OpenJDK
#### JDBC (Java Database Connectivity)
- Standardized API that allows Java apps to interact with RDBMs
- provides a standard way to connect to DBs and execute SQL queries
- JDBC provides classes and methods for working with database objects like connections, statements, result sets
- Need specific JDBC driver for each particular DB
#### J2EE (Jakarta EE)
- a platform built on top of Java 
- provides a set of specs, APIs and technologies for building enterprise apps
- Addresses needs of large-scale enterprise applications
	- distributed systems (EJB), transactions, security, scalability, web services
- Key components of J2EE
	- Servlets, JSP, EJB, JMS, Webservices
- J2EE Container
	- handles lifecycle of J2EE apps and runtime engine for executing J2EE apps
	- e.g. Tomcat, JBoss
##### Multi-tiered Enterprise App
- separates an app into modular layers (tiers)
1. Presentation Tier (Client):
    - Responsible for user interaction and the display of information.
    - Technologies: Web browsers, HTML, CSS, JavaScript, frontend frameworks (React, Angular, Vue.js), or dedicated desktop/mobile applications.
2. Web Tier (Business Logic):
    - Contains the core business logic, processes incoming requests, and coordinates interactions with other tiers.
    - Technologies: Servlets, JSPs, web frameworks like Spring MVC, RESTful web services.
3. Data Tier (Persistence):
    - Handles data storage, retrieval, and database operations.
    - Technologies: EJBs (Entity Beans), JDBC, ORM tools like Hibernate.
##### Jboss EAP / Tomcat (J2EE container)
- **web server** and **servlet container** used for **deploying and running java apps**
	- web server handles http requests and serves static content
- implements Servlet and JSP specs
- provides the run time env for your servlets and JSPs to execute
##### Servlet
- Java classes that handle HTTP requests and responses
- Dynamically generate content based on user interaction
- Extend the `HttpServlet` class and override methods like `doGet` and `doPost` to handle GET and POST requests
- can interact with databases, session information, generate HTML output, among other things
- E.g.: Imagine a servlet processing a login request, validating credentials against a database, and redirecting to a welcome page upon successful login.
##### JSP
- Java uses JSPs as HTML templates that can contain Java code snippets, allowing for dynamic content generation
- When a JSP page is requested, the J2EE container translates it into a servlet behind the scenes
	- the embedded Java code is executed in the servlet
	- then the servlet generates the final HTML content to send to the client
##### JMS
- API for asynchronous messaging between components
- allows for apps to send and receive messages through queues or topics
	- can subscribe to these queues or topics
- Apps use JMS producers to send messages and JMS consumers to receive and process those messages asynchronously
##### Web Services
- J2EE allows us to build web services that enables communication and data exchange between different apps over the internet
- define a standardized way to expose functionalities over the internet
- E.g. JAX-WS (SOAP) and JAX-RS (REST)
	- [Java API for XML Web Services (JAX-WS)](http://jax-ws.java.net/) is a standardized API for creating and consuming SOAP (Simple Object Access Protocol) web services.
##### JPA (Java Persistence API)
- Persistence means the state of the system outlives the process that created it (e.g.: saving data)
- API specification within J2EE that defines how to manage relational data within Java apps
	- a set of rules and best practices
	- i.e. defines a standardized way to perform ORM
- API defined in `javax.persistence`
- JPQL
- Object-relational metadata
- Entities
	- POJOs that represent data you want to persist in the DB
- @Entity: Designates a class as a persistent entity to be mapped to a database table
	- class will represent a table, an instance of that class will represent a row
#### Maven
- Build automation and project management tool
- Dependency Management
	- Maven coordinates with central repositories (Maven Central) to download dependencies
	- Manages conflicting versions of a library
- Build Lifecycle
	- Maven defines standard phases for a project's build process:
		- **`compile`:** Compile your Java source code.
		- **`test`:** Execute unit tests.
		- **`package`:** Create the distributable artifact (e.g., WAR, JAR).
		- **`install`:** Install the artifact in your local Maven repository.
		- **`deploy`:** Deploy the built artifact to a remote repository or server.
- Plugins
- Project Structure (POM.xml / Project Object Model)
	- heart of a Maven project
	- defines: metadata (name, group, version), dependencies, plugins, goals to execute in build phases
- Manages the JAR files that your project depends on

#### Hiberate ORM (Object-Relational Mapping)
- implements JPA
- bridges the gap between Java's OOP and Relational databases
- simplifies interactions with relational databases by allowing you to work with your data as Java objects without needed to write SQL queries
- does this by mapping java classes and properties to databases tables and columns
- HQL allows you to write database queries using OO syntax
- VS JDBC
	- JDBC is more low-level, you write rawl sql queries and manually map results to Java objects
	- JDBC more performant
#### EclipseLink
- implements JPA
#### JAXB (Java Architecture for XML Binding)
- standard Java API that allows you to map Java objects to XML and vice-versa through the process of marshalling (Java -> XML) and unmarshalling
- by simplifying XML data handling, it also simplifies web services by making sending and receiving data in XML formats (JAX-WS) easier
- use XSD (XML Schema) that defines how your java classes should make to XML
# General
#### Concurrency vs. Parallelism
- Concurrency
	- Gives the impression of parallelism by quickly switching back-and-forth between processes
	- Single Short-Order Cook: Imagine a cook taking multiple orders. They switch between tasks – frying a burger, chopping vegetables, plating an order. Only one thing is actively happening at a given time, but the cook gives the illusion of working on all orders simultaneously.
- Parallelism
	- Multiple processes are being executed at the exact same tiem
	- requiring multiple processing units (CPU cores)
	- split a large task in to smaller, independent sub-tasks
	- Multiple Cooks: Now imagine multiple cooks, each working on different parts of an order simultaneously. This is true parallelism, multiple things literally happening at the same time.
#### Synchronous vs. Asynchronous
- Synchronous (Blocking)
	- Step-by-Step: In synchronous programming, tasks are executed sequentially. One task must fully complete before the next one begins.
	- The Waiter Analogy: Think of ordering at a sit-down restaurant. You tell the waiter your entire order, and then you _wait_ for each dish to be prepared and brought to you, one after the other. You can't do anything else while you wait.
- Asynchronous (Non-Blocking)
	- Concurrent but not necessarily Parallel: Asynchronous programming allows multiple tasks to run concurrently. A task can start, hand off work that might take time, and then continue doing other things without waiting for that work to complete.
	- The Fast Food Analogy: Imagine ordering at a fast food counter. You place your order and get a number. You can go sit down, chat with your friend, and when your food is ready (based on the number), you pick it up.

#### SOA
- larger version of microservices
- a way to design software systems where components provide functionality as reusable services
- these services are designed to be independent and interact with each other to fulfill business needs
- Focus on Services: The building blocks of SOA are services. A service is:
    - Self-contained: It handles a discrete unit of functionality.
    - Loosely-coupled: It doesn't rely heavily on other services for its operation.
    - Well-defined interface: It has a clear way for other components or systems to interact with it.

#### GWT (Google Web Toolkit)
- allows you to write front-end code in Java
- compiler translates java into javascript and HTML
#### OOP
- Representing your software as a collection of "objects" that model real-world entities or concepts within your problem domain
- Objects hold data (properties or attributes) and behaviors (methods)
1. **Class:** A class is a blueprint for creating objects. It defines the structure (data) and functionality (behaviors) that all objects of that type will share.
    - Example: A `Car` class could have properties like `color`, `make`, `model` and methods like `start()`, `stop()`, `accelerate()`.
2. **Object:** An object is an instance of a class. You create objects based on the template defined by the class.
    - Example: `myHondaCivic` could be an object of the class `Car`.
3. **Encapsulation:** This principle involves bundling an object's data and methods together within a class, with controlled access to its internal state. Encapsulation helps to:
    - Protect data: You have control over how the object's data can be changed.
    - Reduce Complexity: The internal workings of the object are hidden from other parts of your code.
4. **Inheritance:** Inheritance allows one class (subclass or child class) to inherit properties and behaviors from another class (superclass or parent class).
    - Example: A `SportsCar` class might inherit from a `Car` class, adding things like `turboBoost()`.
    - Promotes code reuse and relationships: Captures "is-a" relationships between things.
5. **Polymorphism:** The idea that objects of different classes can respond to the same method call in their own unique ways.
    - Example: A `Car` and a `Truck` could both have a `move()` method. Polymorphism lets you write code that interacts with various types of vehicles without needing to know their specific details upfront.
**Benefits of OOP**
- Modularity: Objects are self-contained, making code organized, manageable, and reusable.
- Extensibility: Inheritance and polymorphism let you modify or add new functionality without breaking existing parts of your code.
- Maintainability: Encapsulation protects the internals of classes, making changes and debugging easier.
- Modeling: OOP provides a natural way to model software systems that closely mirrors how we think about the world around us.