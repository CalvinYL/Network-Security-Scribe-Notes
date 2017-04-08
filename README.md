---
layout: notes
title: Architecture of the Internet
scribe: Calvin Liang
---

# Architecture of the Internet

**Internet Infrastructure**

* Physical infrastructure consists of two hosts - one sending data to another who is receiving - and intermediate devices (ie. routers)
* Anybody can be an ISP
* Key components of the Internet:
  * ISPs are typically referred to as "domains"
    * Domain names like cs.bu.edu are converted into IP addresses
  * The ability to find routes from one end of the Internet to another (ie. between hosts)
    * Local and interdomain routing
      * TCP/IP used for routing and messaging
      * BGP used for routing announcements
      
**TCP**

* Breakup of TCP Protocol Stack into subsystems (ie. layers):

![TCP Protocol Stack](http://imgur.com/a/c99Wm)

* This demonstrates the communication process between corresponding layers on different systems
* The flow of control/information in a network operates from top to bottom
* A protocol is defined as a "precise set of messages exchanged to accomplish some purpose"
* Data broken up into packets before being exchanged over the internet **PICTURE2**
  * The way packets are turned into actual protocols is through the use of headers
  * TCP encapsulated into IP (ie. TCP packet put into an IP packet)
* When applications exchange protocol messages
* TCP segments become packets at the IP layer, which become messages at the Link layer
* TCP contains "ports" which are designators of an application
  * Two in particular are the source and destination ports
* Client chooses source port so when communication is sent back to you, your system will know which application to deliver the message to.

**IP Prefixes & Address**
* IP Addresses are 32 bits in length (ie. it has a size of 2^32 = 4 billion)
* IP Addresses are broken into four 8-bit segments (or octets) to make it more convenient for writing down down.
* Prefixes are a collection of addresses that all have the same initial bits (denoted by a /# after the IP address)
  * Example: 204.16.254.0/24
    * 204.16.254 is the prefix
    * /24 indicates that the organization owns a total of 2^8 = 256 addresses (ie. all the addresses between 204.16.254.0 and 204.16.254.255)
  
**Steps in IP Routing**
1) Meg wants to send data to Tom
2) An IP packet containing Meg's source port and a destination port is sent through a series of routers
3) A router has a routing/forwarding table that computes a "Next Hop IP" for the destination IP address in the packet using the packet header. It then forwards the packet accordingly to the next router and this process is repeated - as a typical route uses several hops - until the packet reaches the destination address

* Packets are sent independently, so they can take entirely different paths (ie. routers can use different Next Hops) and arrive at different times as well.
It's important to note that this process is best used for things like skype where you want to send data in real time

**IP Protocol Functions**
* Routing:
  * Recall: routers compute paths from a source to a destination
  * Hosts only need to know where a router (connected to a network; aka a gateway) is 
    * Another way to say this is that an end host has a default gateway that the host can send all of its packets to
  * Routers, which have multiple connections to other routers, need to know what the right Next Hop is for all destinations on the internet
* Fragmentation and Reassembly:
  * When a router wants to send a packet to a Next Hop and knows the Next Hop can't handle a packet that large in data size, it can send the Internet Protocol packets as fragments, where they are reassembled upon arriving at the destination
* Error Reporting:
  * Internet Control Message Protocol (ICMP) allows packets to be dropped within a network
    * With how the network is designed, there is no guarantee that a packet will always make it to its destination
    * As a result, if a router decides that it's too busy, it can freely drop any packet that it wants
    * A router can also send a packet back to a source if the packet is dropped, but this is optional
* TTL Field:
  * TTL stands for "Time To Live"
  * How it works:
    * When a packet is first created, we attach an integer number to it. Every time a router receives this packet and passes it on (ie. after every hop), the number is decremented by 1
    * When a router receives a packet with a TTL of 0, it drops the packet. This prevents the network from being congested with endlessly cycling packets.
    
**NATS - Network Address Translation**
* Idea that we can take a bunch of hosts within an organization and give them "private" addresses from a "specially allocated space" (private address space)
  * When data is sent outside of this organization through their gateway to the internet, the hosts' addresses will be changed to the router's address followed by a port
    * Example: Host (IP Address 10.0.0.3) sends data to a router (IP Address 123.1.1.1), and the router changes the IP address to 123.1.1.1:1000 before sending the data out to the internet
   * Different ports will always be given to different hosts
   * These addresses are never used outside of the organization's gateway
   * Only works for TCP/UDP connections that have ports

**UDP - User Datagram Protocol**
* Very simple, but unreliable form of transport on top of the IP layer
  * THe UDP Header consists of only two ports (source & destination), a UDP length, and a UDP cksum
* UDP transport connection to another host provides no guarantee that the data (ie. every packet) you send will make it to the destination
  * "You don't have to get the data there; just do your best :)"
  * Used for VoIP, video, NTP (network time protocol), etc. where latency matters more than reliability
    * Delivers to the application as soon as the data arrives at the network interface
    * For Skype, it's important for your voice to be synchronized with the video so you can have a conversation
* Dangers
  * There is no authentication of source IP addresses 
    * Clients are trusted to embed correct source IP, but there is no actual requirement for this
    * An individual may also put anything inside the source field
* Attacks
   * An attacker could potentially open a connection to another host and put any source address onto the host's packets so when the destination sends a response back, that response will be sent instead to the forged source IP.
  * Anonymouse DoS attacks
    * An attacker may also send a large quantity of packets to all different hosts on the internet, each using a single victim's address as the source. The hosts will reply back to that victim, which sends the victim an enormous flood of packets
  * Anonymous infection attacks (e.g. slammer worm)
    * An attacker can craft a malicious packet and send it to a host (while using a random source address to hide) that will infect the host's system upon receiving the packet


**TCP - Transmission Control Protocol**
* More reliable than UDP because it is connection-oriented and it preserves order
  * The sender breaks the data up into packets, all of which are assigned a number to indicate an order.
    * Packets contain two sequence numbers - one specifying where the packet is in the stream, and one that signifies the number of bytes received
  * The receiver acknowledges receipt of the packets and reassembles the packets in correct order. If no acknowledgement has been made after a certain amount of time, the packets are then resent.
  * Process of establishing a connection before sending data:
    * Device B opens a connection by sending a SYN packet to Device A
    * If Device A is willing to open a connection with Device B, it sends a SYN-ACK packet back.
    * After Device B sends an ACK packet to A after receiving SYN-ACK, they know the connection is open and working.
  
* Security Problems
  * Network packets pass by untrusted hosts
    * Allows eavesdropping and packet sniffing (ie. collecting information, such as passwords, from the packets)
    * This is especially easy when an attacker has control of a machine that is close to the victim
  * If sequence numbers aren't random, an attacker can easily guess them and perform attacks such as spoofing or session hijacking










