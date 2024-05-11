## NGINX
- primarily a web server
- can also be a reverse proxy and load balancer
- has a cache for static content
- event-driven approach
- master-worker process architecture
### Best practices
- enable gzip compression to reduce bandwith of responses
	- save costs, faster
- use `proxy_cache` to store frequent responses
- implement SSL/TLS (https)
### Debugging
- Nginx logs, backend logs, network connectivity (ping, traceroute), resource usage
### Master Process
- reads configuration, binds ports and spawn and managing worker processes
- distributes tasks to workers
- worker processes are forks (near copy) of the master process
### Worker Process
- handle all incoming HTTP requests
- each worker can handle thousands of connections concurrently
- number of worker processes can be set in the configuration
- a single thread in a worker process can handle multiple requests
- communicates with upstream servers (if NGINX is used as reverse proxy)
### Reverse Proxy
- Nginx sits in front of backend servers
- Nginx handles static requests, offloading backend servers
- Dynamic requests get sent to backend and receive responses to then deliver back to client
- use `proxy_pass` directive in location block to send traffic else where
### Load Balancing
- default uses round-robin, but also can do least-connection or ip-hashing
- use `proxy_pass` in location block to redirect to load balancer name  in `upstream` directive, e.g.:
```
upstream load_balancer123 {
	server server1.com
	server server2.com
}
```
### Request matching
- Nginx compares the "Host" header of request against `server_name` directive in each server block
- within the best matching server block, Nginx looks at request URI and finds the `location` block with longest matching prefix
### Configuration
- main config file usually in `/etc/nginx/nginx.conf`
- contains blocks which are groups of directives
- capable of conditional configuration (if statements)
- `user`: specifies the user worker processes run as
- `worker_processes`: number of worker processes
- max number of simultaneous connections = worker_processes * worker_connections
#### http block
- contains server and location block
#### server block
- within http block (can be more than one)
- `server_name`: denotes the domain name of the server (e.g.: google.com)
- `listen`: denotes the IP / port of the server
	- use port 443 and append `ssl` to enable https (w/ certificate)
- use `ssl_certificate` and `ssl_certificate_key` to denote paths to ssl certificate and private key
#### location block
- within server block (can be more than one)
- contains path (e.g.: `/`) that gets concatenated to server_name
	- regex can be used to 
- `root`: base directory for serving files
	- combined with location path to form full filesystem path?
- `index`: default file in root directory to serve when client sends a request for a directory
- `error_log`: path to error logs and logging level
#### events block
- `worker_connections`: number of simultaneous connections that can be opened by a single worker process
#### upstream block
- used to implement load balancing, e.g.:
```
upstream load_balancer123 {
	server server1.com
	server server2.com
}
```
### NGINX Ingress Controller
- software built on top of NGINX to work with Kubernetes and their Ingress resources (rules for handling external traffic to the k8 cluster)
- **Monitors Kubernetes:** 
	- monitors the Kubernetes API for Ingress resources and changes to those resources
- **Dynamic Configuration:**
	- It dynamically generates NGINX configuration files based on the Ingress resources it observes, instructing NGINX how to route incoming traffic
- **Acts as Entrypoint:** 
	- It usually runs as a Deployment within your Kubernetes cluster and acts as the point of entry for external traffic