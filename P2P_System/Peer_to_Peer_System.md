**Peer to Peer System**
===
**History P2P System**
---
**1st Generation: Napster**
Napster is the first generation of music online download company, the architecture of Napster is centralized. The most important part is the index server. The index server stores lists of peers. When a user wants to search for a file, it sends file location request to the index server, and obtain a list of peers with the file, towards which it will send request. Finally the target file is delivered back to the user.
Another and the final critical step is, users who gain a file now being responsible for storing and distributing the file (update index to the index server).

**2nd Generation: Gnutella**
Gnutella designed as a P2P architecture in reaction to issues facing Napster. It is fully decentralized and fairly anonymous. In order to join the network, user must find at least one peer in the network. Communicating with those peers can the new user has knowledge of many peers.
In order to prevent from message flooding when querying, Gnutella came up with a limited-scope flooding protocol, that puts a **"peer count" on depth of query**.

**3rd Generation: Supernodes**
The representative platforms are **Skype** and **KaZaa**. Supernodes evolve from ordinary nodes, and may be used for routing and searching. Limitations are: 1) searching and routing within the network are often unreliable, because network is very dynamic, even supernodes come and go as well. Also the bandwidth cost grows exponentially with the size of network (overwhelm slow nodes results in dropped queries and replies); 2) Protocols have evolved, limiting querying speed and tiered system of supernodes.

----------

**DHT (Distributed Hash Table)**
---
DHT has property like a normal traditional hash table, which contains (key, value) pairs that stores file name (key) and IP address (value). All the index of files combined into a very large hash table. In order to store the large table, it is divided into different small chunks and distributed to every participated nodes in the system. The distributing rules depends on specific system. Common rules are **CAN**, **Chord**, **Pastry** and **Tapestry**.

In traditional hash table, removing a bucket forces remapping all the entire keyspace, whereas in DHT, consistent-hashing determines that removing a node does not rearrange the whole keys.

----------
**Different DHT Rules**
---
**Content Addressable Network**

The CAN architecture is a d-dimensional toroidal coordinate space. It is partitioned into "rectangular" zones, each peer owns one zone. When adding a new node, a random space gets split, while all the other nodes are not affected. Node peers have knowledge of their neighboring nodes (sharing the same boundaries).

The network is quite fault-tolerant and scalable. If one node goes down, a lot of alternative routes could be provided. Every peer has replica for availability.

**Chord**

The keyspace architecture of [Chord](http://blog.csdn.net/wangxiaoqin00007/article/details/7374833) is a ring. Every node knows its predecessor and successor. It introduces a finger table which contains at least 2^(i - 1) after itself. Searching begins from its successor, when its successor does not contains the resource, search from the furthest node in the finger table. Iterate these steps.

After maximum of O(logN) number of nodes that must be contacted can we find the final resource.

>**Notes: Adding a node**
>Every node within Chord will automatically run a heartbeat check to its finger table and its successor to check if its successor's predecessor is itself or has been changed, which we called it stabilization.
>So there are four steps referring to node joining:
>`Join()`
>`Stabilize()`
>`Notify(O)`
>`Fix_fingers()`

**Pastry**
The keyspace architecture of Pastry is a modular ring with size of 2^128, the joining and departing rules are like in Chord. 


----------
**Summary of [Pastry](http://research.microsoft.com/en-us/um/people/antr/PAST/pastry.pdf)**
---
 - Pastry performs application-level routing and object location in a potentially very large overlay network of nodes connected via the Internet. 
 - Every node has a unique identifier (nodeId). When presented with a message and a key, the key would match the closest nodeId among all currently alive Pastry nodes. Each node keeps track of its immediate neighbors in the nodeId space, and notifies applications of new node arrivals, node failures and recoveries.
 - Pastry takes network locality into account by introducing into leaf table.
 - The experimental in the paper takes up to 100,000 nodes to confirm Pastry's scalability, efficiency, self-organize and adapt to node failures, and its good network locality properties.
 - The number of expected routing steps is $log_2bN$, and [L/2] adjacent nodes won't fail simultaneously.
 - Joining and departing mechanism is similar to [Chord](http://blog.csdn.net/wangxiaoqin00007/article/details/7374833).
> **NOTES: Design of routing**
> 1. Given a message, the node first checks to see if the key falls within the leaf set, if so, message would be forwarded to the closest node.
> 2. If there's no match, check the routing table. The message is forwarded to a node that shares a common prefix with the key by at least one more digit. If the routing table is empty or the associated node is unreachable, forward the message (at least) to the node with same length of nodeId, and is numerically closer to the key then the present nodeId.
> The procedure would converge in every iteration, because in each step, **the message is forwarded to a node that either shares a longer prefix with the key, or same length prefix but numerically closer to the key than the local node.**