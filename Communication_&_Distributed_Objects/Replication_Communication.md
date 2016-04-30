**Replication Communication**
===
**State Machine Approach**
---
State machine consists of state variables and commands. State variable means an encoding of state, command means functions that can be performed.

Anything that can be structured as procedures and procedure calls can be structured as state machines and requests.

> **Notes: Ordering with Paxos**
> Paxos allows replicas to come to consensus on the order of requests;
> it makes a proposal that Nth command number should have value V (function and parameters), and state machine can execute commands 1..n if no gaps
> **What about gaps?**
> If leader fails, then gaps need to be filled. Either by learning previous values, or finding out that no one has proposed that value

State machine is a good conceptual model for other ds concepts and protocols, and failure masking using voting. The results are distinguishable from a non-faulty server.

Then disadvantage of state machine is about overhead, hard to understand concurrent requirements and too much redundancy.

**Primary Back-up Approach**
---
**Basic prototype**

Client send request through front end, to the primary node of the remote replica manager, then the primary **atomically** executes the request. If it changes any state, then it sends the updated states, response, request id to the backup nodes. Backup nodes send ACK when finish executing, and primary node send back response to the front end, which response to the client.

**Some Optimizations**

Client could send to any remote node through front end. If backup node receives the request, it forward to the primary node first, the rest steps are the same.

**Reading Optimization**

In order to offload read workload from primary node, read request could send to any of the nodes and does not need to forward to the primary node.

**Primary Migration**

Particularly in mobile computing, nodes could be disconnected from others. In order to solve that problem, we allow migration from backup to primary. 

Prior to disconnection, the node becomes primary from backup. Then while in disconnecting period, data could still be updated and transmitted, (old) data could still be read from other processes. When recovery, back-to-live node would get the updated states.

**MapReduce**
---
**Execution**

1. User program invokes MapReduce library to perform task;
2. Library forks processes on several machines, one master and several workers;
3. Input data splits into many pieces, each piece contains 16-64MB, and one piece will be handled by one map. Input data is located in HDFS.
4. Master assigns some workers to do the map job, and some workers to do the reduce job, and between the map and reduce job, partition function partitions the intermediate data for ordering.

**Fault Tolerant**

What happens when a worker dies?

1. If a map worker dies (periodically pinged by master), master masks the map task as idle and reassigns the task, intermediate data may get lost.
To be more specific, the machine would reset to its initial state, and they are ready to be scheduled to other workers. Even for completed map workers do they need to reassign, because **the data is stored on their machines and is hence unusable.**

2. If a reducer dies (periodically pinged by master), it may not be any trouble because a reducer might have already completed the calculation, and doesn't need to be reassigned.

3. Master would make checkpoints of its data structures periodically. If a master dies, it would start a new copy from its last checkpoint status. Google MapReduce doesn't implement any other protocols, as master failure is rare.
4. Map workers write intermediate data to local disks, and reduce workers remotely read (RPC) data and write to output files.

Reference : http://map-reduce.wikispaces.asu.edu/
