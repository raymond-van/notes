### Network
- [[Work Notes/git-notes/eng/software/Networking|Networking]]
- OSI 7-Layer Networking Model/Stack
	- is a conceptual model that describes how networking works at a high level to help understanding
	- each layer builds on top of each other, adding more functionality
	- Media layers (Physical - Network)
		- how data is moved from point a to point b
	- Host layers (Transport -  Application)
		- how data is chopped up for transport, reassembled and formatted
![](Pasted%20image%2020220809224929.png)
##### Layer 1 - Physical 
- Focuses on the physical shared medium and the standards for transmitting onto / receiving from the medium (e.g. specific network card/cable or wifi card/radio frequency)
- Green things represent network interface cards
![](Pasted%20image%2020220809225230.png)
- a certain voltage can be defined as a 1 bit or 0 bit, then pc A can emit voltages that represent binary code to pc B
![](Pasted%20image%2020220809230012.png)
- A hub can be used to communicate with multiple devices
- Media access control
	- controlling which device can communicate with others
- Collisions occur when multiple devices try to communicate at the same time
	- at layer 1, if a collision occurs, communication breaks down
	- as more devices are connected to the hub, rate of collisions increase
- no uniquely identified devices (via addresses), thus there is no specific device to device communication (goes to all)
- simply transmits raw data on the physical medium
##### Layer 2 - Data Link 
- e.g. Ethernet
![](Pasted%20image%2020220813105910.png)
- Frames (orange dotted box)
	- format for sending information over L2 network
	- frames are transmitted to layer 1 / physical medium which means it gets converted to voltages/light/radio frequency depending on the medium
		- the frame traverses the physical medium and received by other devices
		- at the destination address, the frame then gets analyzed and the payload gets extracted and given back to layer 3 which interprets the data
	- MAC (media access control) address is attached to hardware and  consists of two parts:
		- Organizationally Unique Identifier (OUI), assigned to companies who manufacture network devices (each company has its own OUI)
		- Network interface controller (NIC) 
	- Preamble / Start Frame Delimitter (SFD)
		- lets you know where the frame starts
	- Destination MAC address can be ALL F's which allows you to broadcast your message to all devices
		- Destination is generally a local address
	- Ether type (ET) specifies which layer 3 protocol was used when putting the data/payload inside the frame
		- e.g. Internet protocol (IP)
		- layer 3 uses layer 2 frames for device-to-device communication over a local network
		- layer 2 frames are transmitted through layer 1 / physical medium
	- Payload/Data
		-  If ET is IP, then the payload is an IP packet
		- the data gets *encapsulated* inside a frame
	- Frame check sequence (FSD)
		- indicates if an error has occured via CRC check
Carrier Sense Multiple Access (CSMA, step 2)
- used to communicate with layer 1
- checks for carrier signal
	- if no carrier signal, can pass frame onto layer 1
	- if carrier signal, wait until no signal
Layers are independent
- Layer 2 devices communicates with other layer 2 devices even though layer 2 is using layer 1 to communicate (lower layers get abstracted away)
Collision Detection (CD)
- if a collision is detected a jam signal is transmitted
- backoff period is when no device can send frames
- at end of backoff period, device can resend
![](Pasted%20image%2020220813112833.png)
Hubs
- are layer 1 devices (thus doesn't understand layer 2)
- non destination devices will check destination MAC address, will see that they are not the intended device and discard the frame
- the destination device, will pass on and interpret the layer 2 frame
- Collisions can still occur because hubs are layer 1 devices and will impact all devices
![](Pasted%20image%2020220813113849.png)
Switches
- are layer 2 devices with layer 2 software so they understand frames and MAC addresses
- Stores frames and fowards if valid, then discards after forwarding
- doesn't forward collisions
![](Pasted%20image%2020220813114516.png)
Layer 2 gives us:
- identificable devices via MAC addresses
- Media access control 
- Collision detection
- Unicast (1-1 communication)
- Broadcast (1:all)
- Switches (gives us ability to scale)
Layer 2 only gives us LAN communication via switches or communication via direct point-to-point links using the SAME layer 2 protocol (expensive) 
![](Pasted%20image%2020220813115606.png)
##### Layer 3 - Network 
![](Pasted%20image%2020220813121000.png)
- Responsibility of layer 3 is to get **data** from 1 location to another
- Internet Protocol (IP) is a L3 protocol which adds cross-network **IP addressing** and **routing** to move data between LANs without direct point-to-point links
- IP Packets are moved step by step from **source MAC address** to the **destination MAC address** via *intermediate* networks
	- the packet/payload is *encapsulated* in different frames along the way
- **Routers**
	- are layer 3 devices that move data packets across networks
	- *encapsulate* data packets inside a layer 2 ethernet frame at every hop
		- once the frame reaches a network, the frame is removed and a new frame is created and *encapsulates* the same packet to move to the next network
			- this process continues until the final destination is reached
	- travels using IP addresses
- **Internet protocol (IP)**
	- allows you to connect to other remote networks via intermediate networks
	- **Packets**
		- similar to frames
		- contain data and source + destination address
			- source and destination addresses can be other side of planet
		- packets can be encapsulated inside (L2) frames
		- packets remain the same as it traverses the network
		- frames get removed and created at each hop
			- frames are specific to the local network
		- Time to live (TTL) = hop limit
			- max number of hops that the packet can take before being discarded
			- prevents infinite loops
		- Protocol
			- stores which layer 4 protocol was used
			- so at the L3 destination, it knows which L4 protocol to transmit to
			- e.g.: TCP (6), ICMP/Pings (1), UDP (17)
		- v6 packets provide a larger IP address space
![](Pasted%20image%2020220813121841.png)
**IP Addressing (v4)**
- Dotted-decimal notation - 4 x 0-255 value
- IP addresses need to be globally unique
![](Pasted%20image%2020220817135127.png)
**Subnet Mask**
![](Pasted%20image%2020220817135209.png)
**Routes and Route Tables**
![](Pasted%20image%2020220817135449.png)
- The 24 bit slash prefix (52.217.13.0/24) indicates how specific the match is
	- 32 bit prefix indicates a singular IP address (indicates 52.217.13) is the network?
	- 16 bit prefix indicates 52.217 is network and 13.0 is the host 
	- 0 is the default route that matches all IP addresses
	- also, /24 bit prefix indicates that the subnet mask is 255.255.255.0 
**Address Resolution Protocol (ARP)**
- recall mac address e.g: 3e:22:fb:b9:fb:78
- ARP used to find the MAC address for a given IP address
- runs between layer 3 and layer 2
- if destination is local: there will be 1 L2 frame per packet
- Else if destination is remote: there could be many L2 frames per packet
![](Pasted%20image%2020220817140430.png)
**IP Routing**
- R1 = router, GW - gateway, P1 = packet, F1 = frame, D1 = device
- For d2 -> d3:
	- the packets destination IP address is to d3 (119,18.35.60)
	- F2s destination mac address is to the mac address of r1
	- normal devices that receive packets that aren't destined to it will simply drop that packet
	- routers OTOH, route that packet to somewhere else using route tables
	- f3 has r2 as destination mac (using ARP), p2 is unchanged
	- L3 packet remains unchanged, but L2 frames constantly get removed and created, encapsulating the L3 packet
	![](Pasted%20image%2020220817141425.png)
**L3 Summary**
![](Pasted%20image%2020220817141735.png)
### Layer 4 - Transport 
**L3 Problems**
- In L3, every single packet delivery is independent of one another
- different routes for the same destination can result in out of order packets
- L3 packets only have a source and destination IP address, no method of splitting by application or channel
	- thus, can't have two apps running on source, communicating with two apps at the destination (no method of distinguishing between the applications)
- no flow control - over saturating the destination results in packet loss
![](Pasted%20image%2020220817142625.png)
#### TCP & UDP
- TCP/IP means TCP running on top of L3 IP 
	- TCP is slower but more reliable
- UDP is also a L4 protocol that runs on top of IP
	- UDP is faster but less reliable 
#### TCP Segments
- encapsulated within L3 IP packets
- contains source and destination ports
	- enables the ability to have multiple streams of conversations at the same time between two devices
- sequence number is a unique number that is incremented with each segment sent
	- used for error correction
	- used for ordering
- acknowledgement number is used with the sequence number to let the sender know that the receiver has received a particular segment via the sequence number
- flags (9 bits) + ...
	- used to close connection
	- used to synchronize sequence number, among others
- window
	- the number of bytes that you're willing to receive between acknowledgements
	- once reached, the sender will pause until you acknowledge that amount of data
	- implements flow control
		- lets the receiver control the rate at which the sender sends data
		- prevents the destination address from getting over saturated with packets
- checksum 
	- error checking and for retransmission if a segment gets lost
- urgent pointer
	- can give priority to certain packets/segments?
	![](Pasted%20image%2020220818125012.png)
### Transmission Control Protocol (TCP)
- bidirectional communication channel/stream
	- channel is created using segments
		- since segments are reliable and ordered
	- there will be two sets of segments
		- Initiating: source -> dest
		- Response:  above reversed
- ephemeral port = temporary
![](Pasted%20image%2020220818130026.png)
#### TCP 3-way Handshake
- used to establish a connection between a client and server
![](Pasted%20image%2020220818130310.png)
#### Sessions & State
- Firewalls
	- Stateless firewalls don't understand the state of a connection (initiating vs. Response)
		- need two rules that define the Outbound and Response
		- Used in AWS: Network Access Control List 
	- Stateful firewalls
		- understands state of traffic/connection
		- needs one rule, the opposite direction is automatically allowed
		- understands layer 4
		- In AWS: Security groups
![](Pasted%20image%2020220818130842.png)

### Network Address Translation (NAT)
- designed to overcome IPv4 address shortages
- Private addresses (e.g. 10.0.0.0) can't be routed over the internet so we use NAT to translate to public
- PAT
	- used at home, differentiates devices using ports
![](Pasted%20image%2020220818132556.png)
#### Static NAT
- Green = Private network, Blue = Router(default gateway)/NAT device, Red = Public Network
- Want to communicate with public network from private network
- AWS: Internet Gateway (IGW)
![](Pasted%20image%2020220818133413.png)
#### Dynamic NAT
- used when there are a lot of private ip addresses and not enough public ip addresses
![](Pasted%20image%2020220818133651.png)
#### Port Address Translation (PAT)
- Each client gets a new public source port, gets stored in NAT table
- router at home uses PAT
- can't initiate traffic to these private devices because there would be no entry in the NAT table that would tell us how to translate from public to private
![](Pasted%20image%2020220818133413.png)