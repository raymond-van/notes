Internet
- global network of interconnected devices
- sending data involves breaking the data up into packets and your router examines the packet and forwards it to the next router in its path towards its destination
- Application Protocol Layer <-> TCP <-> IP <-> Hardware
![](../../images/Pasted%20image%2020240427131030.png)
- When you type a URL into a web browser, this is what happens:
1. If the URL contains a domain name, the browser first connects to a domain name server and retrieves the corresponding IP address for the web server.
2. The web browser connects to the web server and sends an HTTP request (via the protocol stack) for the desired web page.
3. The web server receives the request and checks for the desired page. If the page exists, the web server sends it. If the server cannot find the requested page, it will send an HTTP 404 error message. (404 means 'Page Not Found' as anyone who has surfed the web probably knows.)
4. The web browser receives the page back and the connection is closed.
5. The browser then parses through the page and looks for other page elements it needs to complete the web page. These usually include images, applets, etc.
6. For each element needed, the browser makes additional connections and HTTP requests to the server for each element.
7. When the browser has finished loading all images, applets, etc. the page will be completely loaded in the browser window.
### OSI 7 Layer Model
#### Physical Layer
- physical transmission of data bits across a network medium (cables, wireless signal)
- includes NICs, cables
#### Data Link Layer
- ensures reliable and error-free transmission of data frames between network devices on the **same** / **local** physical link (e.g.: switch or router)
- packages data into frames with error-checking mechanisms
- consists of MAC (Media Access Cnotrol) addresses, switches, error detection codes (checksums)
- e.g.: switch receives a frame, checks destination MAC, and forwards it to appropriate port
#### Network Layer
- Enables routing of data packets across different networks
- assigns IP addresses to devices
- determines most efficient path for data to travel
- consists of IP addreses, routers, routing protocols
- e.g.: router looks up destination IP in routing table and forwards packet to next hop on path
#### Transport Layer
- provides reliable and ordered communication between apps on different devices
- consists of Port numbers, TCP, UDP
#### Session Layer
- Creates, manages and terminates sessions between communicating apps
- e.g.: SSL/TLS to establish a secure session between web server and web browser
- e.g.: a user logging into a website creates a session, JWT token may be created to coordinate session information or Redis as a centralized session store
#### Presentation Layer
- deals with data format, data encryption/decryption
- ensures data is presented in a way that both communication applications understand
- character sets / encoding
- e.g.: SSL/TLS to encrypt traffic in transit
#### Application Layer
- closest layer to the user is what the user directly interacts with to communicate with network services 
- defines protocols and services that apps use to exchange data over the internet
- e.g.: web browsers (HTTP), email clients (SMTP)
### Socket
- uniquely identified by IP + Port + Protocol (TCP/UDP)
- must contain all of three
### IP
- unreliable, connectionless protocol
- doesn't care if packet gets to its destination or not
- job is to send and route packets to other computers
- IP packets are independent entities and may arrive out of order or not at all. It is TCP's job to make sure packets arrive and are in the correct order
![](Pasted%20image%2020240427132252.png)
![](Pasted%20image%2020240427132252.png)
#### IP Address
- a unique ID assigned to each device on a network
#### Ports
- used to identify the application or server running on a device 
- an IP addr has many ports for diff apps/services
- socket = IP + port
- HTTP = 80, HTTPS = 443, 22 = SSH, 25 = SMTP
##### Port Forwarding
#### IPv4
- 4 numbers from 0-255 separated by .
#### IPv6
- 8 hexa-decimal numbers separated by :
- not enough ipv4 addresses
### TCP
- ensures that packets are transmitted reliably and in the correct order to the application on the other end using ports
	- if packets are lost, they are retransmitted
- connection-oriented, reliable, byte stream service
	- establishes a connection via TCP-handshake
	- uses sequence numbers to determine correct ordering
- When the TCP layer receives the application layer protocol data from above, it segments it into manageable 'chunks' and then adds a TCP header with specific TCP information to each 'chunk'. The information contained in the TCP header includes the port number of the application the data needs to be sent to.
- When the TCP layer receives a packet from the IP layer below it, the TCP layer strips the TCP header data from the packet, does some data reconstruction if necessary, and then sends the data to the correct application using the port number taken from the TCP header.
#### Three-way handshake
- ensures that client and server can send and receive packets from each other
- sequence numbers used to ensure correct ordering
- SYN (synchronize)
	- Client sends SYN to server
		- contains a random sequence number X
- SYN ACK (synchronize-acknowledge)
	- Server responds to with SYN-ACK packet 
		- includes its own random sequence number Y and X+1 where X is the number that was sent by the client
- ACK (acknowledge)
	- Client sends an ACK packet back to server 
		- acknowledges server's sequence number by sending back Y + 1
#### TCP Header
- contains checksum for error-checking
- contains nothing about IP as TCP doesn't know anything about IPs, it's job is to get data from one app to another app
![](Pasted%20image%2020240427131937.png)
![](Pasted%20image%2020240427132305.png)
### UDP
- fast, connectionless, "best effort" protocol, no guaranteed delivery
	- sacrifices reliability for speed
- sends packets of data (datagrams) without establishing a connection
- can lead to packet loss which is a result of sending packets faster than the receiver can handle
- used for streaming, gaming, other real-time applications
### DNS
- hierarchical DB that translates domain names into IP addresses
- if a DNS server doesn't contain the domain name requested, the DNS server re-directs 'up' the hierarchy
![](Pasted%20image%2020240427130758.png)
### SSL/TLS 
-  see security notes
### HTTP (Hypertext Transfer Protocol)
- Application protocol layer used to transfer data between a client (e.g. web browser) and a server (e.g. web server)
- sits on top of TCP and is used by apps to communicate
- text-based and connection oriented
#### HTTP2
- Binary instead of Textual
- Multiplexing - Multiple asynchronous HTTP requests over a single connection
- Header compression using HPACK
- Server Push - Multiple responses for single request
- Request Prioritization
- Security
### HTTPS
- encrypted version of HTTP that relies on SSL/TLS
- client a webserver starts with handshake where they agree on how to encrypt the communication, uses assymetric cryptography, then symmetric cryptography to actually communicate once a secret key is settled