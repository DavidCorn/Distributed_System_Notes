**File System**
===

**File System vs. database**
---
The true difference between file system and database is naming and encapsulation. Besides, files are more easy to make, more flexible to use.

If the searchtousage ratio is low and the temporal locality is high, then file system is recommended. Otherwise database is recommended.

P.S. The architecture is another difference.

> **Notes: Properties of DFS**
> - Persistence
> - Use same interface as local files
> - Allow for sharing

----------
**Mechanism for Distributed System**
---
**Caching**

DS caching cares about data (files), metadata (information about data), location info, etc. 

**Mount Points**

Mount points is a Natural mechanism to use when mounted subtrees and remote file directories.

Designing DFS mapping: File name -> File id -> file

----------
**Network File System**
---
NFS is designed for stateless server, client-server architecture communicates between each other through RPC. Pathname cannot be interpreted at server.

NFS cache is just like local file system cache.

----------
**Andrew File System**
---
It is originally designed to support campus-wide sharing. The most important factor is scalability. There are several assumptions:

1. Files are usually small, mostly less then 10 KB;
2. Reading is 6 times more than Writing;
3. Sequential access is common;
4. Most files are written/read by only one single user.

The design strategy abstract could be described as: whole file caching, whole file serving, Unix API provided.

----------
**Google File System**
---
[GFS](http://static.googleusercontent.com/media/research.google.com/en//archive/gfs-sosp2003.pdf) is designed to share large data (files) among large number of users, the paper illustrates lots of details about GFS properties. Basically,

GFS client sends a searching request to GFS master. Within GFS master stores the file namespace, which is in another way, metadata. The GFS master won't store much detailed information, and we must minimize its **involvement** in reads and writes so that it does not become a bottleneck. **Client never reads and writes data through the master. Instead, it asks the master which chunkservers it should contact**. Caching (in client) is necessary.

When client gets the chunk locations, it directly reaches out to the closest chunk replica, and fetches data back from them. Chunk size have been chosen as 64MB. It is designed like that because: 

1.  It reduces clients’ need to interact with the master because reads and writes on the same chunk require only one initial request to the master for chunk location information.
2.  Since on a large chunk, a client is more likely to perform many operations on a given chunk, it can reduce network overhead by keeping a persistent TCP connection to the chunk server over an extended period of time.
3.  It reduces the size of the metadata stored on the master.


To be clear, metadata is stored in memory of master. **Chunk servers need not cache data**, because chunks are stored as local files and so Linux's buffer cache already keeps frequently accessed data in memory.

When client tries to reach replicas: 

1. Client pushes data to all the replicas (first to the closest replica, then to all the others). By decoupling the data flow from the control flow, we can improve performance by scheduling expensive data based on network topology.
2. Once all replicas receive the data, the client sends a write request to the primary replica, which assigns the task to other replicas. After receiving ACK from other replicas, primary replica sends ACK or any errors back to the client.
(Default chunk replication is three)

