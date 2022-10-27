---
date: 2022-10-27T01:54:23Z
hero_image: "/content/images/sarah-dorweiler-9Z1KRIfpBTM-unsplash.jpg"
title: A Gentle Introduction to Ethereum Networking overlay stack DevP2P
author: Vamsi Ramakrishnan

---

> URGENT ALL MINERS: The network is under attack. …a computational DDoS, i.e. miners and nodes need to spend a very long time processing some blocks. …due to the EXTCODESIZE opcode, which has a fairly low gas price but which requires nodes to read state information from disk; the attack transactions are calling this opcode roughly 50,000 times per block. “
>
> A DDoS attack on Geth in 2018, and founder of Ethereum calling for a switch to parity.

This quote piqued my curiosity about the DDoS resistance of a decentralized network. It made me wonder what does DDoS resistance here mean - is it that an individual node is resistant to DDoS through protocol robustness or does it mean that there are enough nodes in the Ethereum network to make sure that individual pawns may get sacrificed but the system runs.

While I did understand that the 32 ETH stake deposit for Block Builders and Validators for having skin in the game and slashing of staked ETH was punishment for bad actors. Combine staking, slashing with gas fee deterrents to prevent actors from transaction spamming. These rules and incentives provided Game Theoretical safety. What wasn’t very obvious to me was how these deterrence mechanisms trickle down to the networking layer of Ethereum.

[![Rewards and Penalties on Ethereum 2.0 \[Phase 0\] | ConsenSys](https://cdn.consensys.net/uploads/2020/03/21102225/ImagefromiOS.jpg)](https://consensys.net/blog/codefi/rewards-and-penalties-on-ethereum-20-phase-0/)

**Rewards and Penalties on Ethereum 2.0 \[Phase 0\] | ConsenSys**

Rewards and Penalties on Ethereum 2.0 \[Phase 0\] | ConsenSys

consensys.net

If Ethereum is a Public, Permissionless Blockchain that allows anybody who knows how to install and run software in a piece of hardware to participate, wouldn’t it be easy to launch DoS attacks ? Here’s these 3 stats according to [Certik](https://certik.medium.com/what-is-a-ddos-attack-how-can-it-affect-crypto-4f62cc1cad8c) to help understand the pervasiveness of this issue

* There are over 2,000 DDoS Attacks that are observed world-wide DAILY.
* One third of all downtime incidents are attributed to DDoS attacks.
* $150 can buy a week-long DDoS attack on the black market.

Ethereum nodes have a Public IP Address, most of them running open source clients. Some could be operating behind a Proxy, a sophisticated firewall that supports Deep Packet Inspection, placed in an ISP Network that offers Clean Bandwidth and protection against typical volumetric DDoS attacks. But that is not the default expectation or vision for a participant’s experience.

If patching a fleet of servers with a bugfix in a centralized environment such as a company where there is no choice but to comply - is a pain, I couldn’t fathom to imagine the state of patches in the nodes running Ethereum. Ethereum does focus on client diversity so that bugs in one client type does not bring the entire network down but it also notable that Ethereum suffers from a concentration risk problem.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%272116%27%20height=%271190%27/%3e)![Eth client diversity. ](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Eth client diversity.

Turns out some of these questions and concerns are at least partially addressed in the protocol design of DevP2P. This article is the first amongst a series that takes a look into the internals, and a summary of what I found when I plunged headlong into the rabbithole of the Ethereum Networking Stack. These findings are a result of running Wireshark Utilities on a Full Node a Prysm Consensus + Besu Execution client without depositing 32 ETH or running the validator process. This specific article is a gentle introduction to the history of Overlays and the Ethereum Networking Overlay stack - DevP2P.

    Ethereum is a peer-to-peer network with thousands of nodes that must be able to communicate with one another using standardized protocols. The "networking layer" is the stack of protocols that allow those nodes to find each other and exchange information. This includes "gossiping" information (one-to-many communication) over the network as well as swapping requests and responses between specific nodes (one-to-one communication). Each node must adhere to specific networking rules to ensure they are sending and receiving the correct information.
    

### History of Overlays

> "Mr. Watson come here. I want to see you."

Alexander Graham Bell spoke to Watson, his assistant and set off a momentous revolution in the world of networks by speaking through electromagentism. William B Coy, inspired by Alexander Graham Bell’s lecture operationalized the world’s first commercial telephone exchange in New Haven, Connecticut in April 1878. The exchange featured a central switchboard, to allow any of it’s 21 clients to talk to each other if they had a telephone. For several decades while the capability and functionality of these switches improved exponentially the underlying objective was essentially the same. Create a closed circuit or connect the line between two parties that wished to speak to each other. This type of switching was called circuit switching. The Public Switched Telephone Network (PSTN) was born by connecting sections of clients and the exchanges that connected them.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271240%27%20height=%27994%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

### The internet as an Overlay

The internet is an overlay on the phone network. In the early 1930s the telegraph printers also known as teleprinter, that evolved from the iterative innovations in the field of telegraph, was adapted and overlaid to run on a circuit switching network to facilitate point to point or even multipoint communication. And then came fax machines. The early internet (ARPANET) was overlaid on Public Switched Telephone Network (PSTN). The early pioneers of the internet innovated on computer nodes and the hardware at the edge that connected to this network while piggybacking on the extensively available telephone network, TCP/IP was born, Tim Berners Lee creates the hypertext protocol at CERN unleashing the era of the internet dubbed as Web1.0. The internet was so consequential that it nudged the Jurassic telephone network to adapt its core based on the IP. This phenomenon is called inversion. Regulation and a best effort service model allowed the IP layer to be overlaid on any Network link layer and below. This open model created unprecedented permission-less innovation.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271000%27%20height=%27716%27/%3e)![State of ARPANET, 1977](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

State of ARPANET, 1977

### The Internet is Overlaid

The internet movement spawned a new breed of innovators that always believed in the vision that the internet would be the path to a permissionless, decentralized, censorship-resistant future. These innovators used overlay networks on top of the internet to establish decentralised peer to peer (p2p) systems. Here’s this lovely illustration by [Paratii](https://medium.com/paratii) that traces the history and contributions of the p2p movement.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271400%27%20height=%27735%27/%3e)![P2P Movement History](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

P2P Movement History

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%27566%27%20height=%27691%27/%3e)![Example of an overlay network](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Example of an overlay network

### The structure of the Ethereum Overlay Network

The Ethereum overlay network is built on both TCP and UDP. Not very different from how we use DNS over UDP to discover the address of the server, and TCP / TLS / HTTP for client server communication,

DNSSEC adds a layer of security by adding cryptographic signatures to existing DNS records, and TLS allows for Perfect Forward Secrecy in communication over an insecure channel namely the internet, Ethereum uses ECIES for communication and Bonding during discovery in clever ways to establish the same outcome without a central / centralised certificate authority.

    UDP for discovery
    TCP for everything else
    

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%271177%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

How does discovery work

    1. Ping Pong and Bonding over UDP
    2. Secured key exchange using ECIES on RLPX on TCP
    3. Find Neighbours and Get List of Neighbours
    4. Iterate over list of nodes and progress 
    

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%271273%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

### Standing on the shoulders of Giants

The overlay network of Ethereum DevP2P borrows heavily from several innovations that occured in adjacencies such as

* Public-Key Encryption ( PKE ) & Elliptic Curve Cryptography ( ECC )
* Kademlia Distributed Hash Tables ( k-DHT )
* TCP / IP

    
    +----------------------+------------------------------------------+
    | Foundational Element |                 Purpose                  |
    +----------------------+------------------------------------------+
    | PKE & ECC            | Addressing Scheme & Secure communication |
    | K- DHT               | Node discovery & Bootstrapping           |
    | TCP/IP               | Permissionless innovation                |
    +----------------------+------------------------------------------+
    

### PKE & ECC

The first step towards participation is communication is discovery and the first step towards discovery is having a unique identifier / address. Ethereum Nodes can have addresses thanks to public key cryptography. Ethereum uses elliptic key cryptography for its

* Addressing system
* Secure P2P communications

### Addressing System

Ethereum’s overlay network has to be self organising from an addressing perspective.

* There is no central coordinating entity that should control who gets what address and how the network gets organised,
* Has to consider a large enough to accommodate enough types of actors ( Wallets, Nodes, Smart Contracts etc.)
* Collision resistant and governed by randomness - the chance that 2 people at any given point in time will not get to the same address

### Encryption

While the earliest known forms of encryption have been around since Caesar’s time known as Caesar’s cipher, encryption and secure communication always warranted preshared secrets. Which means the two parties or multiple parties that needed to communicate had to have a pre-established secure channel that facilitate secure exchange. This was the Achilles heel. The idea of public key cryptography changed all of that and was revolutionised by Martin Hellman, Whittfield Diffie and Ralph Merkel in 1976 at Stanford while they theorized about the Knapsack problem.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%27850%27%20height=%27344%27/%3e)![Knapsack problem theorized by Diffie, Hellman & Merkel](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Knapsack problem theorized by Diffie, Hellman & Merkel

While Elliptic Curves have fascinated humankind since 2nd Century AD, first officially described by Diophantus - known as diophantine equations, Fermat’s last theorem. Despite studying them for 2 Millenia, we found mainstream use for Elliptic curves in cryptography in 1985 when Victor Miller and Neal Koblitz introduced Elliptic Key Cryptography.

    y^2 = x^3 + ax + b (mod p) 
    

The curve behind all the magic

### Why not RSA or something else ?

To understand this in terms of intuition lenstra, a researcher introduced this concept of expressing effort to break security in terms of energy / computational cycles required to do so.

> Breaking a 228-bit RSA key requires less energy to than it takes to boil a teaspoon of water. But breaking a 228-bit elliptic curve key requires enough energy to boil all the water on earth. For this level of security with RSA, you’d need 10x the bit length.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%27728%27/%3e)![Intuitive Security Levels](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Intuitive Security Levels

### Multiple ways of visualizing Key and Node address Generation

A visualisation of the geometry of how the key-pair generation happens

1. Arbitrary / Random 256 bit number from some RNG or PRNG
2. Use that to multiply the base point of Gx and Gy (x,y) coordinates defined by the secp256 k1 curve
3. Get the Public Key (x,y) on the curve.
4. Extract 20 bytes from the public key to get the node address.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271639%27%20height=%271546%27/%3e)![Elliptic curve geometry](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Elliptic curve geometry

If we had to write program to perform this for us, how would it look ?

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%271885%27/%3e)![Programmatic implementation of Keypair generation](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Programmatic implementation of Keypair generation

The node address is 20 bytes / 160 Bits long (extracted from the public key)

* The public key itself if 512 bits / 64 bytes long
* The private key is 256 bits / 32 bytes long

  The Node Address space is a 160 bit address space meaning there could be all of 2^160 addresses (including smart contracts, wallets and nodes).

### How do nodes securely communicate with each other

Over and above encryption, signatures have been an essential part of ensuring that a sender cannot deny sending a message, given a private key is kept secret. Signatures give reasons for the receiver to believe the data was sent by sender and only the sender.

    +-------------------+------------+
    |      Outcome      | Technique  |
    +-------------------+------------+
    | Authenticity      | Signatures |
    | Non-repudiability | Signatures |
    +-------------------+------------+
    

2 phase system. There’s signing which the sender does and verifying the signature which the receiver does.The packets are dropped if the signature is invalid.

    Sign = Signature Fn ( Kpriv, Hash Fn( Kpub, MsgType, Msg ) )
    

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%272360%27/%3e)![Cryptographic Signing process of Payload](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Cryptographic Signing process of Payload

    Kpub = Erecover ( Sign )
    isValid = Validation Fn ( Kpub, Sign ) 
    

### Packet Structure

All UDP packets used for discovery follow the structure described here. About 98 bytes are used for Packet integrity, Packet authenticity and packet Identification followed by the data payload of arbitrary length that is RLP encoded ( `parlance to json serialization and deserialization` )

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%271519%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

For example ping contains the following fields

    1. From Node ( IP, TCP Port, UDP port )
    2. To Node ( IP, TCP Port, UDP Port ) 
    3. RLP version 
    

### Kademlia Distributed Hash Table

The Kademlia DHT was adapted to be used in Ethereum for addressing only as it is not a content storage p2p network because of a few properties

    1. O Log (n) search complexity
    2. XOR distance metric 
    3. Fixed size routing Tables
    

**OLog(n) Search Complexity**

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%27730%27%20height=%27389%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

**XOR distance metric logic**

XOR is a good distance measurement metric as it allows for some important properties. If there are 2 nodes (A,B) that have random addresses, the XOR distance between A,B is the same as B,A. The distance to itself is 0, if A is not equal to B then the distance is non-zero, it also satisfies something known as triangle inequality metric. Which states that for a third non-zero point C , the sum of distance between A,B and B,C is always greater than A,C.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%272360%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

**Routing Table**

The address space is 160 bits, and each bucket corresponds to nodes that share the one bit value from LSB all the way to MSB, with a list of nodes in each bucket that have similar XOR distances. Assume that the node address is `11………1`. Each bucket will have a sorted list of nodes last responded and duration of time a given node is present in the routing table. The `Ethereum` clients may limit the maximum number of peers and also the discV5 implementation keeps exactly 16 nodes per bucket sorted.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271606%27%20height=%271721%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

### ECIES Key Exchange

ECIES stands for `Elliptic Curve Integrated Encryption Scheme` is a part of a family of encryption systems called `integrated encryption scheme.`It builds on top of `Diffie Hellman exchange` the concept is quite simple, both Alice and Bob derive a common key without needing to share any information over an insecure channel.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271803%27%20height=%271288%27/%3e)![ECIES Explanation](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

ECIES Explanation

### Where does the overlay network reside

Every active participant in Ethereum that runs a node of any kind ( Light , Full, Archival, Validator, Relayer ) or any other types of nodes that could arise in the future, must run clients. Almost all the specifications of Ethereum foundation are codified as implementations in these clients. The clients are open sourced so anyone is free to modify and create their own version of the client. The overlay network DevP2P is a part of the client(s). So when you install the client binaries in a virtual machine and start the process, the nodes start communicating with other nodes after following a series of steps. Some notes on what are the components that actually make up an Ethereum node. Unlike a typical client server network, Ethereum’s p2p network has a client client network where all clients of a given type are born equal and build their reputation based on the time and behaviour in the network.

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%271061%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%272360%27/%3e)![](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

### How does all of this come together

Some of the constructs deeply embedded in the protocol design makes Ethereum network and even an individual node decently resistant to basic attacks like eclipse, Sybil etc. Even volumetric DDoS attacks if the packets get dropped in the TCP stack, reduces the impact on the application. Of course this is far from perfect.

    1. Eth clients reached out to hardcoded bootstrap node run by eth foundation 
    2. Eth clients bond with bootstrap node to get peers
    3. Eth clients ping/ping with multiple peers to sync with the chain
    4. The consensus client and execution client have their own p2p overlay networks
    5. Node IDs can be different, ENRs are different between consensus and execution clients 
    6. Without a signed Ping / Pong Bonding your packets will be dropped
    7. All further find nodes and enumeration happens after ECIES key exchange which means arbitrary packet floods will get dropped
    8. Every k-bucket has a max of 16 nodes as peers and limited by max peers parameter in the config file of the client
    9. This list is again sorted based on time on node’s routing table and time to respond. 
    

![](data:image/svg+xml,%3csvg%20xmlns=%27http://www.w3.org/2000/svg%27%20version=%271.1%27%20width=%271640%27%20height=%271054%27/%3e)![Eth Overlay State Diagram](data:image/gif;base64,R0lGODlhAQABAIAAAAAAAP///yH5BAEAAAAALAAAAAABAAEAAAIBRAA7)

Eth Overlay State Diagram

## Further Reading

[![pydevp2p/discovery.py at develop · ethereum/pydevp2p](https://opengraph.githubassets.com/7aedeeae5a71f043f3a177ebbe02e72d161eab2691a3fba1a5d230d7ad593667/ethereum/pydevp2p)](https://github.com/ethereum/pydevp2p/blob/develop/devp2p/discovery.py)

**pydevp2p/discovery.py at develop · ethereum/pydevp2p**

Python Implementation of the Ethereum P2P stack. Contribute to ethereum/pydevp2p development by creating an account on GitHub.

github.com

[![The Wonderful World of Elliptic Curve Cryptography](https://miro.medium.com/max/1160/1*fN1sBKNvVScvbpEFwyZR0Q.png)](https://medium.com/coinmonks/the-wonderful-world-of-elliptic-curve-cryptography-b7784acdef50)

**The Wonderful World of Elliptic Curve Cryptography**

Introduction

medium.com

[![Ethereum: Signing and Validating](https://miro.medium.com/max/1152/1*aiTPFO7COzuOlxcHbMVYtw.png)](https://medium.com/@angellopozo/ethereum-signing-and-validating-13a2d7cb0ee3)

**Ethereum: Signing and Validating**

A core primitive of Ethereum and other cryptocurrencies is the ability to sign data that can be verified by anyone. This powers the…

medium.com

[![A Brief History of P2P Content Distribution, in 10 Major Steps](https://miro.medium.com/focal/1200/632/51/50/1*qNZwhkmhcqXyRVpFJ1qG5A.jpeg)](https://medium.com/paratii/a-brief-history-of-p2p-content-distribution-in-10-major-steps-6d6733d25122)

**A Brief History of P2P Content Distribution, in 10 Major Steps**

Peer to peer networks build upon decades of cryptographic research and battleground experimentation. It’s tough for any particular…

medium.com

[**9.4 Overlay Networks — Computer Networks: A Systems Approach Version 6.2-dev documentation**](https://book.systemsapproach.org/applications/overlays.html#peer-to-peer-networks)

book.systemsapproach.org

[![Dissecting the Ethereum Networking Stack: Node Discovery](https://miro.medium.com/max/1200/1*tyE85u1O_-srtgbbe0EQtA.png)](https://medium.com/@saibalaji4/dissecting-the-ethereum-networking-stack-node-discovery-4b3f7895f83f)

**Dissecting the Ethereum Networking Stack: Node Discovery**

In the last few days, I got a chance to spin up a Eth2 validator node and dive into the depths of the Ethereum N/w stack using some…

medium.com

[**Elliptic Curve Digital Signature Algorithm Explained | Maxim**](https://www.maximintegrated.com/en/design/technical-documents/tutorials/5/5767.html)

Find a great guide to explain the Fundamentals of an Elliptic Curve Digital Signature Algorithm Authentication System from Maxim Integrated. Learn more today.

www.maximintegrated.com