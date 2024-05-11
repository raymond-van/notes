## RPC
- Executes functions / procedures on remote server
- Isn't tied to HTTP, works over various protocols (HTTP, TCP)
- Custom interfaces -> more tailored to the functions being exposed, can require specific knowledge
 - Stateful
 - Legacy
### gRPC
- built by google, faster than REST (better for high-volume data or low-latency apps), more complex, uses Protobufs (binary serialization)
## REST APIs
- REpresentational State Transfer
- Uniform interface
	- all API requests for the same resource should look the same
- Stateless
	- server/client can understand any message without knowledge of the previous messages sent
	- thus, each message needs to include all information needed for processing it
	- server doesn't need to know anything about the client's state and vice-versa (no server-side sessions)
- Separate concerns between client and server
	- implementation of client and server are done independently without knowing about each other
	- changes can be made to client or server without affecting operation
	- relies on the fact that as long as each side knows the format of messages to send to each other, the two sides can communicate
- Cache
	- resources should be cached on the client/server side whenever possible (if it's allowed)
- Layered system architecture
	- client and server need to be able to communicate w/ each other indirectly through layers
- Code-on-demand (optional)
	- responses can contain executable code
- REST became dominant because it addressed many of the issues with the technologies before it:
	- **Simplicity:** REST leverages existing HTTP and web concepts, making it easier for developers to understand and use.
	- **Flexibility:** REST's resource-based model and use of HTTP methods makes it adaptable to various use cases.
	- **Performance & Scalability:** The stateless nature of REST, along with its efficient use of HTTP caching, makes it suitable for large-scale, high-performance APIs.
### HTTP Requests / Responses
- contain a HTTP verb, a header, a path to a resource, an optional message body containing data
- POST (create), PUT (update)
- Response Codes:
	- 200 (OK), 201 (Created), 204 (No content), 400 (Bad request), 301 (Permanent re-direct), 304 (Not modified), 307 (Temporary re-direct, 403 (Forbidden), 404(Not found), 500 (Server error), 503 (Server unavailable)
	- Temp/perm re-directs effect SEO
- Message body
	- used for file uploads, form submissions, sending data
#### Req Headers
- Host: server's domain name
- User-agent: browser
- Accept: the type (MIME) of content that it is able to receive back from the server
	- e.g. text/html, application/json, application/pdf, application/xml, application/octet-stream
#### Res Headers
- Content-type: the content type of the response sent from the server, should match one of the accepted types from the request 
#### Cookies
- can be created/modified by server (client + server-side)
- small text files that websites send to your browser that get stored onto your computer/browser that the website can retrieve later in future visits
- automatically included with HTTP requests that match the cookies domain + path
- Components
	- Name-value pair (data), Expiration, Domain (Websites that can access), Path (Section of website where cookie applies), Secure (Use HTTPS), HttpOnly (Prevent JS)
- Session cookies
	- Last only for current session and deleted when you close
	- used for shopping cart
- Permanent cookies
	- Have an expiration date and stick around longer
	- used for remembering login information or website preferences
- Avoid storing sensitive information -> use server-side storage
##### Cookie Authentication
- **Login:** When you log in to a website, the server can send a `Set-Cookie` header in the response, containing a unique session ID.
- **Browser Stores Cookie:** Your browser remembers this cookie.
- **Subsequent Requests:** Every time you visit a page on that website, your browser automatically includes the cookie in the HTTP request headers.
- **Server Validation:** The server uses the session ID in the cookie to identify you, confirming that you're logged in and authorized to see the page you requested.
- Alternatives:
	- JWT (Encoded tokens)
	- Session tokens in server storage (Instead of storing the session ID in a cookie, the server can keep it in a database or memory. This is a bit more secure than plain cookies)
#### Local / Session Storage
- Client side storage on the browser unlike cookies which can be server-side as well
- Not explicitly added to requests unlike cookies
- Larger storage limit (5-10 MB) than cookies (4KB) 
- Local has no expiration
- Use cases
- Avoid storing sensitive information -> use server-side storage
## GraphQL
- is a spec that defines a query language for APIs and a server-side runtime to fulfill those queries
- uses HTTP, uses schema and types
- has a single endpoint where all queries are directed
- uses resolvers to fetch the data for all fields in the payload
- GraphQL’s conceptual model is an entity graph. As a result, entities in GraphQL are not identified by URLs. Instead, a GraphQL server operates on a single URL/endpoint, usually `/graphql`, and all GraphQL requests for a given service should be directed at this endpoint.
### Schema Definition Langauge (SDL)
- Schema defines the contract between server and client
- special root types that are the entry points for the requests sent by the client
```
type Query { allPersons: [Person!]! }
type Mutation { createPerson(name: String!, age: Int!): Person! }
type Subscription { ... }
```
 - define types and the field e.g.
 ```
type Person {
  name: String!
  age: Int!
  posts: [Post!]!
}
```
#### Queries
- can have arguments in query
- allPersons is the root, everything that follows is the payload
```
{
  allPersons (last: 2) {
    name
    posts {
      title
    }
  }
}
```
#### Mutations
- createPerson is the root and takes in 2 arguments, will return id, name and age in the response
```
mutation {
  createPerson(name: "Bob", age: 36) {
	 id
    name
    age
  }
}
```
#### Subscriptions
- clients can subscribe to an event to establish a realtime connection to the server
- server pushes data to client when event occurs
- represents streams of data
### vs. REST 
- fetches what you need, doesn't get too much data, doesn't get too little data, gets exactly the data you need
- more efficient, as with REST, you may need to send multiple requests to get the data you need, whereas GraphQL can potentially get everything in one request
- due to flexibility there is more insight about the data that's requested on the backend 
- adding new data often requires new endpoints or modifying existing apis
- steeper learning curve, REST better for simpler or if data neatly maps to APIs
- caching is more complicated because REST has predictable endpoints
## OpenAPI Spec (OAS / Swagger Spec)
- machine-readable format (YAML/JSON) for describing REST APIs
- allows you to define everything about your API (endpoints, operations, parameters, authentication methods, responses)
- encourages consistent API design and better maintainability so other devs can easily understand and consume your API
- allows automated tools to process your APIs
	- includes, data validation, doc generation, code generation, mock servers
- soves the problem of having incomplete, non-existing or unclear documentation
- emphasizes a design-first approach to creating APIs
	- create API description code first -> then write code or potentially use tools to provide boiler plate
	- advantage is that writing code will have a skeleton to base off of
### API Description File
- root object is the OpenAPI Object
	- mandatory fields: `openapi` (version), `info` (about the API) and one of `paths` (endpoints, incl. parameters and possible responses), `components`, `webhooks`
- Paths is the container for all operations supposed by the API
![](../../../images/Pasted%20image%2020240427172403.png)
- other important objects/fields include: content (describing type of data), Parameter object (inputs to request), Server object (base URLs where API is being served)
### Swagger
- Tools (editor, UI, Codegen) built around OAS
- Editor allows you to design APIs according to OAS
- UI visualize OAS APIs
- Codegen allows you to generate code from your OAS descriptions
## Authentication
### JWT
- Stateless, server doesn't need to maintain session information for each user, making it scalable
 **1. User Login:**
- The user enters their credentials (username/password) on the login form.
- The credentials are sent to the authentication server for verification.
**2. Authentication and Token Issuance:**
- The authentication server validates the credentials (typically against a database).
- If valid, the server creates a JWT. This token contains:
    - **Header:** Defines the token type (JWT) and the signing algorithm used.
    - **Payload:** Contains claims about the user, such as their user ID, name, roles, and any other relevant information. This data is Base64URL encoded.
    - **Signature:** Created using the server's private key and the encoded header and payload. This ensures the token's integrity and authenticity.
**3. Sending the JWT:**
- The server sends the JWT back to the client-side (web application).
- JWT stored as a cookie (HttpOnly to prevent JS, Secure mode for HTTPS, SameSite=strict to defeat CSRF, set expiration)
**4. Including the JWT in Requests:**
- With subsequent requests to access protected resources on the web application server, the client includes the JWT in the Authorization header. The format typically follows:
    ```
    Authorization: Bearer <JWT_TOKEN>
    ```
**5. Server-Side Validation:**
- The server receives the request containing the JWT.
- The server extracts the JWT from the Authorization header and verifies it using its public key (which corresponds to the private key used for signing).
- If the verification is successful (signature is valid and no tampering occurred), the server decodes the payload to access the user's claims.
- Based on the claims (user's roles or permissions), the server decides whether to grant access to the requested resource.
### OAuth (Open Authorization)
- enables apps to obtain limited access (scopes) to a user’s data without giving away a user’s password
- is an authorization framework (gives access to certain resources)
- assumes authentication has already taken place
	- authentication can be done with an identity provider (e.g. google / meta), OpenID Connect
- OAuth defines four roles:
	- **Resource Owner**: The resource owner is the _user_ who authorizes an _application_ to access their account. The application’s access to the user’s account is limited to the scope of the authorization granted (e.g. read or write access)
	- **Client**: The client is the _application_ that wants to access the _user_’s account. Before it may do so, it must be authorized by the user, and the authorization must be validated by the API.
	- **Resource Server**: The resource server hosts the protected user accounts.
	- **Authorization Server**: The authorization server verifies the identity of the _user_ then issues access tokens to the _application_.
![](../../../images/Pasted%20image%2020240427183912.png)
#### Flow
1. **"Login with..."** You see the option to sign in to an app (App A) using your Google/Facebook/etc. account.
2. **Consent:** When you click "Login with Google ", you're redirected to Google's authorization server. Google asks if you'll allow App A to access specific parts of your Google data (like your profile info).
3. **Authorization Code:** If you grant permission, Google sends a temporary code to App A.
4. **Token Exchange:** App A exchanges this code with Google for an access token. This token is like a special key with limitations based on the permissions you approved.
5. **Getting Your Data:** App A uses the access token to request the authorized parts of your Google data.
### SSO
- **Key Concept:** A single set of credentials (username/password) grant you access to multiple related but independent applications or services.
	- instead of establishing identity over and over, a user establishes their identity once and can then access several different services
- **Example:** Think of it like a master key for a group of related buildings. Once you unlock one, you can easily access the others.
### SAML (Security Assertion Markup Language)
- XML-based standard for exchanging authentication and authorization data between parties, particularly between an identity provider (IdP) and a service provider (SP)
- In a SAML-based system, a user requests access to a protected resource. The service provider asks the identity provider to authenticate the user and assert whether they are granted access to the resource.
- Single Sign-On (SSO): Users can log in once at the IdP and access multiple service providers without needing to authenticate again.
### Best Practices
- implement MFA
- Sanitize all user input to prevent injection attacks like SQL inj. or cross-site scripting (XSS)
#### Salt
- Proper password storage involves "salts" – unique, random data added to each password before hashing
- Salting forces attackers to crack each password individually, even if two users have the same password. It makes precomputed password tables (like rainbow tables) less effective.
#### Password Hashing
- utilize client-side hashing which is written in front-end code
- Argon2, bcrypt, scrypt
- Avoid older, faster algos like MD5 and SHA-1, vulnerable to cracking
## Caching
### CDN
- geographically group of servers that caches content close to end users
- Improves website load times (strategic distributed servers), reduces bandwidth an origin server must provide, increased availability and redundancy (due to distributed nature), increased security (e.g. DDoS mitigation)
### Server-side
- used dedicated cache systems like Redis/Memcached
- caching can be done within your app's memories (use map?)
- HTTP caching via Nginx (reverse proxy)
- DBs often cache frequent queries
#### Redis
- See DB notes
#### Shared sessions
- If you have multiple web servers handling traffic (for load balancing purposes), a user's requests could be routed to different servers, each unaware of the session created on another server.
- Web applications often use sessions to store user-specific data (e.g. shopping cart)
- Redis acts as a shared, centralized storage location for session data.
- All your web servers connect to the same Redis instance.
- When a user's session needs to be created or retrieved, the web servers interact with Redis, ensuring consistency no matter which server handles the user's requests
#### Memcached
- simpler than redis, only stores strings, no way to persist data
### Client-side
- used for speed, reducing server-strain, can provide offline-functionality
- Browser Cache
	- HTTP response headers can instruct the browser how long and under what conditions it should cache a resource
- Local / Session Storage
- Service Workers
- Important to know when to update cached resources
#### Service workers
- exist between web app and network
- JavaScript files that run in the background, separate from the main browser thread, giving them the ability to work even when the user navigates away from your web page or closes the browser tab
- enable push notications, tasks to be run periodically in the background even if user isn't on the site
- **Custom Caching Strategies:** With service workers, developers can implement sophisticated caching strategies. Here are some common ones:
	- **Cache Only:** Retrieve resources exclusively from the cache, primarily used for precached content.
	- **Network Only:** Always fetch from the network.
	- **Cache First, Network Fallback:** Attempt to retrieve from the cache; if unsuccessful, fetch from the network.
	- **Stale-While-Revalidate:** Serve a potentially stale cached resource while simultaneously updating the cache in the background with a fresh version from the network.
### Cache Invalidation
- The core problem is striking the balance between:
	- Serving Cached Content: Improves speed and reduces server loads.
	- Ensuring Freshness: Users shouldn't be stuck with old versions of your site or data
- HTTP headers for time-based invalidation
	- Cache-control, Expires
- Content-based Invalidation
	- If content has changed, fetch the content
	- Each content version gets hashed
## Message Queues
- provide a way for different components of an application to communicate asynchronously.
- producer sends messages to a queue and consumer processes these messages when it's ready
- e.g. AWS SQS
### Messages vs. Events
- messages are sent to a specific destination, events are just an emitted signal
- similar to event-driven architecture
	- message queues often deals with task distribution whereas EDA deals with change in system state
- Benefits
	- **Decoupling**: Producers and consumers don't need to be aware of each other or be online simultaneously.
	- **Reliability**: Queues often **persist** messages, ensuring they won't be lost if a sender or receiver is temporarily unavailable.
	- **Load balancing**: Messages can be distributed among multiple consumers, improving workload handling.
	- **Scalability**: Queues help build applications that can scale in response to varying traffic.
## Message Brokers
- an intermediary that handles messages, often includes message queues as a core component
- **Decoupling:** Applications interact with the broker, not directly with each other.
- **Publish/Subscribe (Pub/Sub):** One message can be delivered to multiple interested subscribers.
- **Complex Routing:** Messages can be routed based on criteria, content, or rules.
- **Message Transformation:** Adapting message formats between systems.
- **Reliability and Persistence:** Ensuring message delivery, even in the case of failures.
- e.g.: AWS SNS, RabbitMQ
### RabbitMQ
- traditional message broker
- focuses on routing messages between producers and consumers
- **Queues:** Stores messages until consumers can process them.
- **Exchanges:** Route messages to queues based on rules and bindings.
- **Message Types:** Supports various messaging patterns like direct, topic, fanout, RPC, etc.
- **AMQP:** Primarily uses the AMQP protocol (Advanced Message Queuing Protocol), but also supports other protocols like MQTT and STOMP
- used for distributing tasks or jobs between workers asynchronously
### Apache Kafka
- distributed streaming platform that is massively scalable, super-efficient for handling streams of data
- includes a message broker (and thus message queue) as a component
- used for big data apps, real-time data pipelines, building event-driven architectures, high-volume messaging systems, log aggregation
- **Topics:** 
	- Data is organized into streams called topics
- **Producers:** 
	- Applications that send data (messages) to Kafka topics.
- **Consumers:** 
	- Applications that read and process data from topics.
- **Partitions and Brokers:**
	- Topics are split into partitions and stored across a cluster of Kafka servers (brokers) to provide fault tolerance and massive throughput

## Real-time Data
### Server-Sent Events (SSE)
- one way communication
- allows a web server to send updates / events directly to a web browser over HTTP
- used when you want the webpage to display real-time updates w/o client having to constantly ask server for new data
	- e.g.: live news feeds, social media updates, chatrooms
- simple compared to WebSockets, supported by modern browsers, less overhead than constantly polling server for updates
- data is mostly text/JSON
#### How it works
- browser establishes persistent HTTP connection with server using `EventSource` JavaScript API
- server keeps connection open and sends events with a special `text/event-stream` format
- browser receives events and triggers frontend JS code to process the new data
### WebSockets
- bidirectional communication
- low-latency
- flexible data formats (text + binary)
- reliable as it is over TCP
- slightly more complex than SSE
### UDP
- see networking notes for more details
- very low-latency, but unreliable
- not common for typical web apps
### Short Polling
- unidirectional
- browser sends repeated requests to the server at regular intervals to check for new data
- simple but low-latency and high overhead due to frequent requests
### Long Polling
- unidirectional
- browser sends single request to server
- server holds connection open until new data is available and then sends it to the client
- lower latency and less overhead than short polling
- increase server load, as it holds open connections for longer periods
- better to use SSE or websockets
## Application Servers
- needed by frameworks like Django, node.js, Spring
- e.g.: Python (uWSGI, Gunicorn), Java (Apache Tomcat)
## Single Page Applications
- e.g.: Gmail / Twitter
- make AJAX (background HTTP calls) to APIs
## URI (Uniform Resource Identifier)
- general string used to identify resources (webpages, books  (isbn), email (mailto), files (ftp))
**Components:** A URI may include the following:
- **scheme:** Indicates the protocol to use (e.g., `http`, `ftp`, `mailto`)
- **authority:** Can include hostname, port (e.g., `www.example.com:80`)
- **path:** Hierarchical structure to locate the resource (e.g., `/images/logo.png`)
- **query:** Parameters passed to the resource (e.g., `?search=term`)
- **fragment:** Identifier within the resource (e.g., `#section1`)
### URL
- subset of URI
- focuses on specifying the location of a resource on a network and how to retrieve it
- must include the protocol (e.g.: https)