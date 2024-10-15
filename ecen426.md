# ECEN 426
# Content for Midterm 1

## UNIT 1: NETWORKING

### The Internet, Protocols
The internet is composed of billions of connected computing devices
- hosts are end systems which run network *apps* at the internet's *edge*
- routers and switches forward packets all over the place in the middle of the network
- communication links connect all parts of the network
    - fiber, copper, radio, satellite
- networks are small collections of these things: hosts, routers/switches, and links
- the internet is a network of networks
- it's also an infastructure that provides services to many of the edge applications
> ***Protocols*** define the *format*, *order* of *messages sent and received* among network entities, and *actions taken* on message transmission, receipt.

### Network Edge:
- hosts can be *clients* or *servers*
    - they send *packets* of data
    - servers are often in datacenters
> packet transmission delay = L / R

> L = length of packet (bits)

> R = bandwidth (bits/sec)

### Network Core:
- **packet-switching** is when a host breaks application-layer messages into *packets*
    - packets get forwarded from one router to the next, across links on a path from source to destination
    - this is very efficient, especially for large networks
    - can't always garuntee immediacy of transmission (challenge for streaming)
    - great for "bursty" data
    - buffer overflow could be an issue
- **circuit-switching** is when end-to-end garunteed connection is provided
    - this is very fast for a single connection, but very inefficient
- the internet operates on *store-and-forward* principle:
    - a router will receive an entire packet, veryify its completeness and *only then* will it begin to transmit the packet to the next link
- there are two key network-core functions
    - **Routing**: 
        - a *global* action: determines the source destination paths to be taken by packets. 
            - determined using routing algorithms, which are updated from time to time
    - **Forwarding**:
        - a *local* action: moves arriving packets from the input link to the appropriate output link

### Network Performance:
- how does packet delay and loss occur?
    - transmission delay
        - L / R
        - L = packet length (bits)
        - R = link transmission rate (bits/sec)
    - queueing delay
        - time waiting at output link for transmission
        - depends on congestion level of router
    - loss
        - can occur if there are no free buffers when packets arrive
    - propogation delay
        - d / s
        - d = length of physical link
        - s = propogation speed (~3\*10^8 m/s)(~speed of light)
    - nodal processing
        - check bit errors
        - determine output link
        - typically milliseconds or less
- traceroute is a cool program that can track delay between locations
- **throughput** is the rate (bits/time unit) at which bits are being sent from sender to receiver
    - the throughput can only be as high as the bottleneck link

### Protocol Layers, Service Models
- Internet Protocol Stack
    - application
        - supporting network applications
    - transport
        - process to process data transfer
    - network
        - routing of datagrams from source to destination
    - link
        - data transfer between neighboring network elements
    - physical
        - bits "on the wire" or transmitted wirelessly
- each part of the internet protocol stack adds its own header to the payload.
- these headers are removed by the receiving end as they are used

## UNIT 2: APPLICATION LAYER

### Principles of Network Applications
when creating a network app, the whole philosophy is that the program doesn't need to know anything about lower layers. it "just works"

#### Client-Server Paradigm
- server
    - a host that is always on
    - has permanent IP address
- client
    - can contact & communicate with server
    - may be intermittently connected
    - may have dynamic IP address
    - clients don't directly communicate with each other
    - HPPT, IMAP, FTP

#### Peer-to-Peer Architecture
- *no* always-on server
- arbitrary end systems directly communicate
- peers request from and provide service to each other
> self-scalability: new peers bring additional service capacity **as well as** new service demands--in a healthy network each peer will pull its weight
- peers are intermittently connected and change IP addresses
    - this leads to complicated management problems

#### Process Communication
- within the same host, two processes communicate using *inter-process communication* (defined by OS)
- between hosts, processes communicate by exchanging *messages*
    - processes send/receive messages to/from their *sockets*
    - to receive messages, processes must have identifiers:
        - IP address is associated with the host
        - port numbers are usually used to identify specific processes, as many processes may be running on one host
- *client process*: a process that initiates communication
- *server process*: a process that waits to be contacted

#### Responsibilities of Application-Layer Protocol:
- types of messages exchanged
    - request
    - response
- message syntax: what fields in messages, etc..
- message semantics: meaning of information in fields
- rules for when and how to send/respond to messages

timing, data integrity, throughput, and security are all concerns to be balanced in the choice of transport service for an application

#### TCP vs UDP
**TCP**
- reliable transport
- flow control: sender won't overwhelm receiver
- congestion control: sender won't overload network
- connection oriented: setup required between client and server process
- has options for security through TLS
    - end-point authentication through encrypted TCP connections
- does not provide:
    - timing
    - minimum throughput garuntee
    - security

**UDP**
- unreliable data transfer
- does not provide:
    - reliability
    - flow control
    - timing
    - throughput garuntee
    - security
    - connection setup

#### Web and HTTP
**Web**
- Web page consists of objects, which can be stored on different servers

**HTTP**
- hypertext transfer protocol
- web's application layer protocol
- operates on a client/server model:
    - client: browser that requests, recieves, and displays web objects
    - server: sends objects in response to requests
- uses TCP (typically port 80 or port 443 on server)
- HTTP is "stateless" - server maintains no information about past client requests
- **TWO TYPES OF HTTP**:
    - Non-Persistent HTTP
        - TCP connection opened
        - one object sent
        - TCP connection closed
    - Persistent HTTP
        - TCP connection opened to server
        - several objects sent over that single TCP connection
        - TCP connection closed
    RTT: rount-trip time: time for a small packet to travel from client to server and back
- HTTP Request method has 3 main parts:
    - request line
        - each line in request and header ends in crlf (``\r\n``)
    - header lines
    - body
- There are a few other HTTP request messages
    - POST
    - GET
    - HEAD
    - PUT
- HTTP Response Status Codes
    - 200 OK
    - 301 Moved Permanently
    - 400 Bad Request
    - 404 not found
    - 505 HTTP version not supported

#### E-Mail, SMTP, IMAP
**E-Mail**
- 3 major components
    - user agents
        - "mail reader"
        - composes, edits, reads mail messeges
        - outlook, iphone mail client
        - outgoing, incoming messages are stored on the server
    - mail servers
        - *mailbox* contains incoming messages for user
        - message queue of outgoing mail messages
        - SMTP protocol between mail servers to sent email messages
    - simple mail transfer protocol (SMTP)
        - email uses TCP, port 25
        - sending server acts as client to receiving server
        - some handshaking happens, the message gets sent, then the socket gets closed
        - messages must be in 7-bit ASCII
        - CRLF.CRLF (``\r\n.\r\n``) signifies end of message
        - SMTP sends several objects in multipart message through a persistent connection, whereas with HTTP the object is encapsulated in the response message
        - SMTP is push, HTTP is pull

#### DNS
- DNS services at their most basic level are just hostname to IP address translation
- host aliasing
    - canonical name: the real name (server.example.com)
    - alias name: the alias (example.com)
- mail server aliasing
- laod distribution using replicated web servers:
    - many IP addresses correspond to one name
- DNS is a distributed, heirarchical database
```
ROOT DNS SERVERS---------------------               ROOT
    |                               |
    |                               |
.com DNS servers            .org DNS servers        Top Level Domain (TLD)
|               |                   |
|               |                   |
yahoo.com       amazon.com          pbs.org         Authoritative
dns servers     dns servers         dns servers
```

**Types of Servers**
- Root DNS Servers
    - there are 13 logical root name 'servers worldwide' - these have been replicated hundreds of times
    - root servers are the contact-of-last-resort if absolutely nothing is cached for a given TLD
- TLD Servers
    - contain the information for the authoritative DNS servers in their domains (.com, .org, .net, etc)
- Authoritative DNS Servers
    - managed by individual organizations (amazon.com, byu.edu, etc)
    - provide authoritative hostname to IP mappings for the organization's hosts
    - can be maintained by the organization or by a service provider
- Local DNS Name Servers
    - each ISP has one (also called "default name server"
    - does not belong to heirarchy
    - is more like a 'DNS cache' of recently visited addresses

**Types of Queries**
- Iterated Query
    - contacted server replies with the name of a server to contact
    - "i don't know this name, but go ask this guy"
    - the local DNS server will keep querying servers until it finds the right one
- Recursive Query
    - each server that gets asked takes ownership of resolving. 
    - server 1 talks to server 2, who asks server 3, who asks server 4. server 4 has the name and gives it to 3 who passes it to 2 and finally 2 gives it to 1.
    - this results in a heavy load at the upper levels of the heirarchy

**Types of Resource Records**
(name, value, type, ttl)
- type=A
    - ``name`` is hostname
    - ``value`` is IP address
- type=CNAME
    - ``name`` is alias for some canonical name
    - ``value`` is canonical name
    - ex: alias ``www.ibm.com`` is really ``servereast.backup2.ibm.com``
- type=NS
    - ``name`` is domain
    - ``value`` is hostname of authoritative name server for this domain
- type=MX
    - ``value`` is name of mailserver associated with ``name``

**DNS query/reply Messages**
```
+------------------------------------+
| Identification  | Flags            |
+------------------------------------+
| # Questions     | # Answer RRs     |
+------------------------------------+
| # Authority RRs | # Additional RRs |
+------------------------------------+
| questions *                        |
+------------------------------------+
| answers *                          |
+------------------------------------+
| authority *                        |
+------------------------------------+
| additional info *                  |
+------------------------------------+
* variable number of items
```
**Identification**: 16 bit number for query, reply to query uses same number
**Flag options**:
- query or reply
- recursion desired/available
- reply is authoritative

**Questions**: name, type fields for a query  
**Answers**: RRs in response to a query  
**Authority**: Records for authoritative servers  
**Additional Info**: stuff that might be helpful/useful  

To register a new website:
- provide the *DNS Registrar* with names, IP addresses of authoritative name server
- the registrar then inserts A & NS RRs into the .com TLD server
- then you create an authoritative local server
    - an A record for www.example.com
    - an MX record for example.com

**DNS Security**
- DDOS attacks
    - bombard root servers with traffic
        - this typically doesn't work
    - bombard TLD servers
        - this is potentially more dangerous
- Redirect attacks
    - man-in-the-middle: intercept DNS quries
    - DNS poisoning: send bogus replies to DNS server, which caches it

### P2P Applications
> File Distribution Thought Experiment  
> Time to distribute F (file) to N clients using client-server approach:  
> D >= max{N\*F/u, F/dmin}  
> Time to distribute F (file) to N clients using peer-to-peer approach:  
> D >= max{F/u, F/dmin, NF/(u + sum(ui))}  
>   N = number of copies  
>   F = file size  
>   dmin = minimum client download rate  
>   u = server upload capacity  
>   ui = individual user upload capacity  

**BitTorrent**
- Files are divided into 256Kb chunks
- peers in the torrent send & receive file chunks
- also a tracker tracks which peers are participating in the torrent
- tit-for-tat philosphy

### Video Streaming / Content Distribution
- distributed, application-level infastructure

**Video Streaming (encoding + DASH + playout buffering)**
- video compression uses spatial and temporal encoding
    - spatial: if some group of pixels uses the same color, then just send that color once and what group of pixels use it
    - temporal: only send the differences between the previous frame and the current frame, instead of a whole new frame
- CBR = constant bit rate
- VBR = variable bit rate
- challenges
    - server to client bandwidth will vary over time
    - packet loss and delay will cause poor video quality or lagging
- DASH: **D**ynamic, **A**daptive **S**treaming over **H**TTP
    - server:
        - divides video file into multiple chunks
        - each chunk is stored and encoded at different rates
        - *manifest file* is created, which contains different URLs for chunks of various bitrates
    - client:
        - periodically measures server-to-client bandwidth
        - consulting *manifest*, requests one chunk at a time
        - can choose different coding rates at different times, depending on bandwidth
    - intelligence & decision-making is left up to the client:
        - when to request chunks
        - what encoding rate to request
        - where to request chunk (which server)

**Content Distribution Networks (CDN)**
challenge: how to stream contnt to hundreds of thousands of *simultaneous* users?
1. mega-server: doesn't work
2. store/serve multiple copies of videos at multiple geographically distributed sites (CDN). there are 2 approaches:
    1. enter deep:
        - push many CDN servers deep into many access networks, closer to the user
    2. bring home:
        - smaller number of larger clusters very close to high-traffic internet crossroads
here's how a CDN works:
- store several copies of content at various CDN nodes
- subscriber requests content from CDN
- client is directed to a nearby copy

### Socket Programming with UDP and TCP
**Socket:** door between application process and end-end transport protocol
Two Socket Types:
- UDP: unreliable datagram
    - no 'connection' between client and server
    - no handshaking
    - sender explicityly attaches IP destination address and port # to each packet
    - receiver extracts sender IP address and port # from received packet
    - data may be lost or received out of order
- TCP: reliable, byte stream-oriented
    - client must contact server & establish connection
    - server creates new TCP socket for the client

# Content For Midterm 2

## UNIT 3: NETWORKING LAYER
NOTE: I BEGAN TAKING NOTES ON SLIDE #33 (NO UDP NOTES)

### Transport Layer Services 
- Transport services and protocols provide logical communication between application processes running on different hosts
- Transport protocol actions on end devices:
    - Sender: breaks application messages into segments and then passes to the network layer
    - Reciever: reassembles segments into messages, and then passes to the application layer 

Transport vs. Network layer services and protocols 
- Network layer: logical communication between hosts 
- Transport layer: logical communication between processes 

### Two Principal Internet Transport Protocols 
- TCP: Transmission Control Protocol 
    - Reliable in-order delivery
    - congestion control
    - flow control
    - connection setup
- UDP: User Datagram Protocol 
    - unreliable, unordered delivery
    - no-frills extension of "best-effort" IP 
- Both do not guarantee:
    - Delay guarantees
    - bandwidth guarantees

### Multiplexing and Demultiplexing:
- Multiplexing at sender: Handle data from multiple sockets, add transport header (later used for demultiplexing)
- Demultiplexing at receiver: Use header info to deliver received segments to correct socket
- How Demultiplexing works:
    - Host receives IP datagrams
        - Each datagram has source IP address, and destination IP address
        - Each datagram carries one transport-layer segment 
        - Each segment has source, and destination port number
    - Host uses IP addresses and port number sto direct segment to appropriate socket 
- Connectionless Demultiplexing:
    - When creating a datagram to send into UDP socket, must specify:
        - Destination IP address
        - Destination port #
    - When receiving host receives UDP segment:
        - checks destination port # in segment 
        - directs UDP segment to socket with that port #
    - In connectionless demultiplexing, datagrams with the same dest prot # but different source IP addresses and/or 
    source port numbers will be directed to the same socket at receiving host
    
- Connection-oriented demultiplexing
    - TCP socket identified by 4-tuple:
        - source IP address
        - source port number
        - dest IP address
        - dest port number
    - demux: receiver uses all four values to direct segment to appropriate socket
    - If processes from different IPs with the same dest port # are sent to a host, they will be demultiplexed on different 
    sockets
- SUMMARY:
    - Multiplexing demultiplexing: based on segment, datagram header field values
    - UDP: demultiplexing using destination IP and destination port number only 
    - TCP: demultiplexing using 4-tuple: source and destination IP addresses, and port numbers

### Connectionless transport: UDP 
- Connectionless:
    - No handshapking between UDP sender, receiver
    - each UDP segment handled independently of others
- So why UDP?
    - No connection establishment which can decrease total RTT delay
    - It's simple, there is no connection state at sender and receiver
    - small header size
    - UDP can blast away as fast as desired, which means it can function in the face of congestion
- UDP use:
    - Streaming multimedia apps 
    - DNS
    - SNMP
    - HTTP/3 
- If reliable transfer needed over UDP (e.g HTTP/3):
    - add neede reliability and congestion control at application layer 
    - UDP segment header:
| 16 bits | 16 bits | #total of 32 bits wide
| -------------- | --------------- |
| source port # | dest port # |
| length | checksum |
| Payload | More Payload |

- UDP checksum:
    - Goal: detect errors such as flipped bits in transmitted segments 
    - sender:
        - treat contents of UDP segment as sequence of 16-bit integers
        - checksum: addtion (one's complement sum) of segment content
        - checksum value put into UDP checksum field
    - Receiver:
        - compute checksum of received segment
        - check if computed checksum equals checksum field value, if not equal, there is an error in the data received 


### Principles of Reliable Data Transfer
What logic is necessary to make sure that an unreliable channel is still usable for reliable communication?
What should we consider?
- state of sender & receiver sides (they likely can't know each other's states without explicitly communicating that)
- what is/isn't reliable (bit flips, package order, etc)

RDT 1.0: Reliable Data Transfer Over *Reliable* Channel
- We make the base assumptions that:
    - no bit errors
    - no packet loss
- There will be separate FSMs for sender & receiver
    - sender will send data into underlying channel
    - receiver will read data from the underlying channel, just waiting for signal

RDT 2.0: Reliable Data Transfer Over *Unreliable* Channel 
- if you can't assume no bit errors & no packet loss, have to do extra to ensure correct transmission
    - sender will send data and wait for ACK/NAK before returning to waiting state
    - receiver will wait for a call from below,
        - if received correctly then sends an ACK
        - if received incorrectly then sends a NAK
    - once again, they don't know each other's state

RDT 2.1:
- sender:
    - seq # added to packet
    - two seq. #s (0,1) needed
    - must check if received ACK/NAK correctly
    - twice as many states
        - state must remember whether expected packet should have seq # of 0 or 1
- receiver:
    - must ckeck if received packet is duplicate
    - state indicates whether 0 or 1 is expected packet seq #
    - receiver cannot know if last ACK/NAK received OK at sender

RDT 2.2: NAK-free
- instead of sending a NAK, receiver sends ACK for last successfully received packet (this must include seq #)
- this is what TCP does

RDT 3.0: Channels with errors *and* loss
- now, we assume that the underlying channel can also lose packets
- still need:
    - checksums
    - sequence #'s
    - ACKs
    - retransmissions
- need even more though
    - if no ACK received within a 'reasonable' amount of time (use countdown timer) then retransmit
    - receiver needs to know how to handle duplicate packets
    - sender needs to know how to handle duplicate ACKs
- **CONCLUSION: 4 things needed**
    1. acknowledgements
    2. timer
    3. sequence #'s
    4. checksums 

As is, our RDT 3.0 is quite inefficient
- send something, have to wait a while for acknowledgment to know if sent correctly
- pipelining is a way to make this much more effective
- always sending, always receiving


