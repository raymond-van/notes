# Architecture
## Monolith
- entire app is built and deployed as a single unit
- all components in a single code base
- e.g.: Spring app
- Pros:
	- Simple to develop
- Cons:
	- Hard to manage, scale and deploy as complexity increases
	- need to scale entire app even if one part has most of load
## Microservices
- app is a suite of small, independently deployable services focused on very specific tasks
- communication occurs through lightweight APIs
- ideally, each microservice should get its own database
- independently scalable
- Pros:
	- agility: teams can work independently on individual services with the best technology for their service
	- resilient: failure of one service shouldn't take down the entire system
	- faster deployments
	- can scale only the services under load
- Cons:
	- managing distributed system is more complex
	- need to worry about data consistency
### Service Mesh
- dedicated infrastructure layer that sits on top of your microservices responsible for advanced network capabilities and managing inter-service communication between microservices
- "sidecar proxies" deployed alongside each service instance manage traffic and add the service mesh features
- e.g.: Istio
- Features:
	- **Traffic Management:** Load balancing, routing, retries, circuit breakers (prevent cascading network failures)
	- **Observability:** Metrics, logging, tracing for distributed systems
	- **Security:** Encryption, authentication, authorization between services
### Serverless
- servers get managed by cloud providers, provides resources as needed, on demand with auto-scaling
- good for unpredictable workloads or event-driven tasks or individual microservices
- e.g.: image processing lambda function that gets triggered by image upload
- AWS Services: Lambda, DynamoDB, API Gateway
## SOA
- Breaks app into larger, reusable services communicating over a network (REST, messaging)
- in between microservices and monolith
- Pros:
	- has some benefits of microservices
- Cons:
	- has some cons of monoliths
## Architectural Patterns
### N-Layered
- Segregate the application into logical layers (e.g., presentation, business logic, data access).
- Web app: UI layer, service layer and database layer
### Client-server
- server provide resources or services to clients 
- Clear separation of concerns, centralized resource management, potential for scalability
- e.g.: Web apps (client: browser, server: web server), database systems, file sharing systems
### Master-worker
- centralized master node distributes tasks and workload across workers
- master managers workers (creates, destroys, assigns)
### MVC
- building UI / web apps
- **Model:** Represents the data and core logic of the application. It encapsulates the data and the business rules.
- **View:** Responsible for presenting the data to the user in a human-readable format. It doesn't contain any application logic and simply displays information received from the controller.
- **Controller:** Handles user interactions and updates the model accordingly. It receives input from the view, interacts with the model to perform actions, and updates the view based on the model's state.

# Design 
## Gang of Four Design Patterns
- patterns made famous by the four authors of book "Design Patterns: Elements of Reusable Object-Oriented Software"
- https://refactoring.guru/design-patterns
### Creational
- Include: Factory, Abstract Factory, Builder, Prototype, Singleton
- Building a UI:
	- **Singleton:** A configuration manager might be a Singleton, ensuring there's only one source of settings.
	- **Factory Method:** A button factory could create various button types (text, icon, etc.)
	- **Abstract Factory:** You might have factories for different UI themes (light, dark) creating matching buttons, text boxes, etc.
	- **Builder:** Constructing a dialog window with a title, content, and buttons could use the Builder pattern for flexibility.
	- **Prototype:** You might have a base layout template to clone and customize.
#### Factory
- Creates a product within a single family of products 
- e.g.: Logistics -> Ships and trucks
- Consists of:
	- Creator: abstract class with factory method that subclasses must implement (Logistics)
	- Concrete Creators: subclasses of creator that create concrete products (Sea Logistics and Road Logistics)
	- Product: interface that defines the general object that can be created (Transportation)
	- Concrete Products: implementations of Product interface (Ship and Truck)
![](Pasted%20image%2020240428183516.png)
#### Abstract Factory
- Creates products within multiple families of products
- e.g.: Furniture Factory -> Modern Furniture Factory / Victorian Furniture Factory-> Chairs / Sofas/ Tables
- Consists of:
	- Abstract Products: interface (Chair, Sofa, Table)
	- Concrete Products: implements above (Modern Chair, Victorian Chair, etc..)
	- Abstract Factory: interface (Furniture Factory)
	- Concrete Factory: implements above (Modern / Victoria Factory)
	![](Pasted%20image%2020240428183503.png)
#### Builder
- **Problem:** Constructing complex objects step-by-step using a simple constructor can become unwieldy, especially when there are many optional parameters
- **Solution:** The Builder provides a fluent interface with methods for setting various parts of the object. Finally, a "build" method returns the completed complex object.
- **Analogy:** Think of building a pizza. You use a builder with methods like "addTopping", "chooseCrust", "selectSauce", etc., and then get your finished pizza
#### Singleton
- **Problem:** You need to guarantee there's only one instance of a class in your entire application, and you want global access to it.
- **Solution:** The Singleton pattern restricts a class to having a single instance. It provides a global access point to retrieve that instance.
- **Analogy:** Think of a government having a single president—the Singleton pattern ensures this principle.
- need thread locks if multithreaded
### Structural
- Include: Adapter, Bridge, Composite, Decorator, Facade, Flyweight, Proxy
- Building a reporting system:
	- **Adapter:** Your system needs to use data from an old legacy system with a strange interface. An Adapter can wrap the legacy interface and provide a modern API.
	- **Decorator:** You want users to add charts, filters, and summaries to reports dynamically. Decorators can add these capabilities on the fly.
	- **Facade:** The report generation logic is complex. A Facade provides functions like "generateSalesReport" simplifying the process for users.
	- **Composite:** You can build reports that have sections, subsections, etc. The Composite pattern allows treating everything as a "ReportElement" for consistency.
	- **Bridge:** Your reports should be rendered as HTML, PDF, or raw data. The Bridge pattern decouples the report format (abstraction) from the rendering mechanism (implementation).
#### Adapter
- **Problem:** You have two interfaces that are incompatible (e.g., trying to fit a square peg in a round hole).
- **Solution:** The Adapter acts as a translator. It creates a class that wraps one of the interfaces and implements the other, converting the calls and data so that the objects can collaborate despite their mismatched interfaces.
- **Analogy:** Think of travel adapters that let you use an appliance with outlets in a different country.
#### Bridge
- **Problem:** You have an abstraction (like a shape) and multiple potential implementations (different drawing methods). You want flexibility in switching between these implementations without rigid coupling.
- **Solution:** The Bridge decouples the abstraction and implementation. You have separate hierarchies for each, and they connect through a "bridge" interface.
- **Analogy:** Imagine a TV remote (abstraction) that can work with different brands of TVs (implementations). The remote interacts with the TV through a standard set of controls (the bridge).
- e.g.: 3d data can be presented in: point sets, voxels, point clouds
### Decorator
- **Problem:** You need to add extra features or responsibilities to an object, but want to do so dynamically and flexibly without changing the original class structure.
- **Solution:** Create "Decorator" classes that wrap the original object. Each Decorator adds its own behavior, and you can stack multiple Decorators to chain different functionalities.
- **Analogy:** Think of decorating a Christmas tree by adding ornaments, lights
### Facade
- just creating a higher level of abstraction e.g.: sci-kit learn's fit() method
### Behavioral
- Include: Chain of Responsibility, Command, Interpreter, Iterator, Mediator, Memento, Observer, State, Strategy, Template Method, Visitor
- Game Development
	- **Chain of Responsibility:** Handling player input (keyboard presses, mouse clicks) could be a chain where different UI elements and game logic components try to process the event.
	- **Observer:** Health bars, power-up indicators, etc., are observers that update based on the player's state.
	- **Strategy:** AI enemies might switch strategies (aggressive, defensive, evasive) based on conditions.
	- **Command:** Player actions (move, attack) could be commands queued and executed by the game engine.
	- **Iterator:** Iterating over a list of in-game objects to draw them on the screen.
#### Observer
- pub/sub
- **Problem:** You have an object (the "subject") and a set of other objects ("observers") that need to be notified when the subject's state changes. You want this relationship to be loosely coupled.
- **Solution:** The subject maintains a list of dependent observers. When its state changes, it automatically notifies all its observers.
- **Analogy:** A news website is the subject. Subscribers are observers who receive notifications when new articles are published.
#### Strategy
- **Problem:** You have a family of algorithms, and you need to select one of them at runtime based on the context.
- **Solution:** Define each algorithm in its own class, all sharing a common interface. The context object holds a reference to a "Strategy" object and delegates the work to it. You can change the strategy of the context at runtime.
- **Analogy:** A navigation app. It has different routing strategies (shortest route, avoid highways, scenic route) that you can switch based on your needs
#### Command
- queues

## Domain Driven Design
- Domain-driven design (DDD) is a software design approach focusing on modeling software to match a domain according to input from that domain’s experts
- In terms of object-oriented programming, it means that the structure and language of software code (class names, class methods, class variables) should match the business domain
- e.g.: if a software processes loan applications, it might have classes like LoanApplication and Customer, and methods such as AcceptOffer and Withdraw
## TDD
- development methodology where you write tests _before_ writing the actual implementation code. Tests drive the design and development process.
## CQRS (Command and Query Responsibility Segregation)
- **Segregating Operations:** CQRS divides the operations you perform on data into two categories:
	- **Commands:** Actions that change the state of your system (e.g., create an order, update a customer's address).
	- **Queries:** Actions that simply read data without changing anything (e.g., get a list of products, retrieve a user's profile).
- separating the responsibilities can lead to simpler code
- can independently scale commands or queries
## Event Sourcing
- Events as the Source of Truth
- Instead of storing only the current state of your data, Event Sourcing stores all changes to that data as a sequence of immutable events
- application state is derived by replaying these events
- data is stored in an append-only database that store events in the order they happened
- combined with event-driven architecture
- provides a full history of changes
- used when require strong audit trails (e.g.: financial / compliance)
- used in Kafka
## Event-driven
- software design pattern where loosely coupled components communicate by producing and consuming events
- An event signifies a change in state or an important occurrence (e.g., "Order Placed," "Item Shipped," "Payment Processed").
- Event Producers generate events when something relevant happens
- Event Routers/Brokers route events to interested components
- Event Consumers subscribe to specific types of events
- used in Kafka, RabbitMQ, AWS SNS & SQS, Apache Spark
**Key Characteristics**
- **Asynchronous:** Producers and consumers don't directly interact and don't need to wait for each other.
- **Decoupled:** Components remain highly independent, making the system more flexible.
- **Scalable:** It's easy to add new producers or consumers without disrupting the overall system.
### Messages vs. Events
- messages are sent to a specific destination, events are just an emitted signal
- similar to message queues
	- message queues often deals with task distribution whereas EDA deals with change in system state
