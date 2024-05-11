## Integration
- integrating code changes from developers into a shared branch
- automatically building, packaging and testing those changes 
### Building
- **Building** transforms source code into runnable artifacts
	- You compile your `.java` files into `.class` files (bytecode).
- **Compilation/Transformation:** This is about taking your source code (e.g., Java, Python, C++) and transforming it into a form the computer understands. This may involve:
    - **Compilation:** Converting source code into machine code or an intermediate form (like Java bytecode).
    - **Transpilation:** Translating from one higher-level language to another (e.g., TypeScript to JavaScript).
    - **Linking:** If your project has multiple modules, the build process links them together into a final executable.
### Packaging
- **Packaging** prepares those artifacts, along with resources, for distribution and deployment
	- You package the `.class` files, required libraries, and configuration into a `.jar` file ready for execution.
- **Gathering and Organizing:** Packaging is about collecting all the necessary components of your application into a distributable format. This includes:
	- **Built Executables:** The output from the build process.
	- **Dependencies:** Any libraries or additional files your application needs to function.
	- **Configuration Files:** Settings and parameters controlling how your application runs.
	- **Documentation:** Help files, READMEs, etc.
## Delivery
- ensures your software is **always in a deployable state**
- adds more automation around testing / staging environments
- involves a manual step to deploy to production
## Deployment
- Get all approvals
- Create backups, release notes
- Have rollback plan
- Have a deployment strategy
	- e.g.: Canary, blue-green, rolling-update
### Canary
- involves gradually rolling out a new version of your software to a small subset of users while the previous version remains live for the rest
- if problem with deployment, only a small subset of users will be impacted
- rollbacks are can be easily done by redirecting traffic
- can test with real user feedback
- Traffic routing
	- can be done with load balancers, service meshes, deployment tools like kubernetes have built-in capabilities
#### Example Deployment
1. **Baseline & Canary:** Your current live production version is the "baseline," and the new version is the "canary."
2. **Gradual Traffic Shift:** A small percentage of traffic (e.g., 5%) is routed to the canary version
3. **Monitoring and Analysis:** You closely monitor key metrics (error rates, latency, user behavior) for both the baseline and the canary version
4. **Decisions & Adjustments:**
    - **Success:** If the canary shows good health, you gradually increase the percentage of traffic it handles
    - **Issues:** If problems arise, you quickly roll back the canary, minimizing the impact
### Blue-green
- technique for deploying software updates with minimal downtime and reduced risk by using two identical production environments termed "blue" and "green"
- near-zero downtime
- simple rollbacks
- costly as it requires double the infrastructure
#### Example Deployment
1. **Blue is Live:** At any given time, one environment (e.g., blue) is live, handling all production traffic
2. **Green is Staged:** The new version of the application is deployed to the other environment (e.g., green), where it's inactive but ready for testing
3. **Testing on Green:** Thorough testing is conducted on the green environment to verify functionality and catch any errors
4. **The Switch:** Once testing is successful, traffic is routed to the green environment, making it the new live production instance
5. **Blue Standby:** The blue environment becomes inactive. It can either be kept as a rollback option or updated for future releases
### Rolling Updates
- involve incrementally replacing old instances of your application with new ones, instead of updating everything at once
## Testing
### Unit Testing
- individual units (e.g. functions) of software are tested to ensure their correctness. 
- Ensures smaller components are functionally sound while taking the burden off of higher-level tests
- write these tests during the development process and they are run as automated tests
### Integration Testing
- broad category of tests where multiple software modules are integrated and tested as a group
- meant to test the interaction between multiple services, resources, or modules
- e.g. an API’s interaction with a backend service, or a service with a database
### Functional Testing
- software is tested to ensure functional requirements are met
- testing is performed by providing input and comparing expected/actual output.
### User Acceptance Testing
- validate that the software meets user requirements, thinks about the user's perspective and system meets business requirements
### System Testing
- software works end-to-end
### Performance Testing
- measures speed, responsiveness under variety of conditions
- Identify bottlenecks that might cause performance degradation
- Metrics:
	- Response time, throughput (reqs/second), resource usage (cpu, memory, network), error rates
### Load/Stress Testing
- Specifically examines how a system performs under expected user load or pushing the system beyond its limits (determine breaking point)
### Regression testing
- ensure new features don't break existing functionality
### Smoke tests
- quick set of tests to verify major components are working
## Environments
### Dev
- often using lightweight DBs 
- verbose logging
- relaxed security / authentication
- less emphasis on performance
- testing
### Staging
- should aim to mirror production as closely as possible
- more testing
- should meet performance requirements
- should be in state that is ready to deploy
### Production
- minimize logging
- strict security
- should be scalable, highly available, fault tolerant, highly performant
- should have secure mechanisms for handling secrets (API keys, credentials)
- end users
## Scaling
- make replicas, distribute across machines
- vertical scaling (more power)
- horizontal scaling (more machines)
- DB sharding
- Load balancing
### Load balancing
**Strategies**
- Dynamic/Adaptive
	- Distribute based on real-time metrics like server load / resource usage / response time
- Round-robin
	- sequential, e.g.: (3 requests and 2 servers: -> s1, s2, s1)
- Least connections
	- allocate to least used server
- IP-hashing
	- hash IP
#### Layer 4 LB (Transport)
- distribute based on raw network segments (TCP, UDP)
- routes primarily based on IP addresses and port numbers
- e.g.: port 80 -> route to web server
- limited flexibility but faster
#### Layer 7 LB (Application)
- can inspect the full content of requests
- can distribute based on URLs, HTTP headers, cookies (containing session information), payload content
- e.g.: route /api traffic to API servers
- flexible but slower
## Performance
- replicas to handle throughput
- cache if possible
- DB sharding
- use CDN (closer geographic location, cache)
## Availability / Fault Tolerance
- minimize single point of failures
- make replicas
- distribute across machines + data centers
## DB Migrations
- see DB notes
- https://phauer.com/2015/databases-challenge-continuous-delivery/
- Any changes to the schema may require a DB migration as DBs have a strict schemas -> need to adapt existing data with schema changes and address compatibility with app code
	- adapting existing data can be difficult if there is a lot of data
	- if multiple apps use our DB, then you may need to assess compatibility with multiple apps
	- hard to roll back changes done in a DB
	- difficult to test as you would need DB similar to production DB
- If scheduled downtime is possible, then handling DB changes is simple
	- Downtime -> Make DB changes -> Update and deploy app -> Restart app
- DB migrations can be a challenge when trying to implementing continuous delivery
- Schema changes are ideally backwards compatible
- Each app / service ideally has its own DB, thus a DB change only needs to coordinate with a single app / service
- If no downtime is allowed -> do blue-green deployments -> make step-by-step DB and app changes to blue/green envs
## Monitoring
TODO