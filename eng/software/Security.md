## OWASP 10
## Best Practices
- use HTTPS, encrypt sensitive info at rest
- sanitize user inputs to prevent injection
## SSL/TLS (Secure Sockets Layer / Transport Layer Security)
- enables HTTPS
- cryptographic protocol used to encrypt data in transit between web browser and website
	- attackers can't intercept and read sensitive information
- verifies the legitimacy of the website you're connecting to
- performs a handshake
- SSL deprecated
### SSL/TLS Certificate
- small file containing data that binds a websites identity to a cryptographic key pair
- issued by trusted authorities
- consists of: public key, domain name of website, certificate issuer and their signature, validity dates
- needs to be installed on a web server to enable HTTPS
- browsers check if a website is authentic by checking if the certificate:
	- is issued by a trusted CA with valid signature, has correct domain name, hasn't expired
#### SSL/TLS Certificate
- obtained using trusted third-party
## Injection
### SQL Injection
- When attacker adds SQL code as part of their user input to a form
- Prevention:
	- validate and sanitize user inputs
	- parameterized queries
	- stored procedures
## XSS (Cross-site scripting)
- allows attacker to inject JS code to read cookies / browser storage
## CORS
- Cross-Origin Resource Sharing (CORS) is an HTTP-header based mechanism that allows a server to indicate any origins (domain, scheme, or port) other than its own from which a browser should permit loading resources
## CSP (Content Security Policy)
- computer security standard introduced to prevent cross-site scripting, clickjacking and other code injection attacks resulting from execution of malicious content in the trusted web page context
## Server Security
- Use a firewall: One of the most effective ways to secure a server is to use a firewall to block all unnecessary incoming traffic
- Close unnecessary ports: Make sure to close any ports that are not needed for your server to function properly. This will reduce the attack surface of your server
## Least Privilege Principle
- fundamental security concept that dictates that users, accounts, processes, or services should be granted the **minimum** amount of privileges needed to perform their functions
- minimizes the potential damage that can occur in the event of a security breach