**Global State**
===
**Deadlock**
---
Deadlock occurs if the wait-for graph has a loop in it. If an observer wants to see the loop, he must have a consistent, global view of the entire system. Phantom deadlocks occurs when the latency in the system causes us to miss critical events.


----------
**Cut**
---
A cut slices through all event histories, and splits each process history into "complete" and "future".
The only thing that needs notice is that cut should be **inconsistent**. 


----------
**Taking snapshots of system state**
---
Chandy-Lamport Snapshot is a distributed algorithm to record a consistent global state for the system. It is extremely useful for analyzing stable properties.

> **Notes: The algorithm**
> The algorithm is divided into two parts: the Marker sending rule and the Marker receiving rule. The special marker message is to specify the state of the system. Every process records state locally.
> 
> - Marker Sending Rule:
> Process starts the algorithm by recording its state, and sends the marker message to every other process except itself
> - Marker Receiving Rule:
> There are two situations:
> 1) If receiving process does not record its state, it will create an empty list for each process, then record the state following the received message. Finally send messages as the Marker Sending Rule.
> 2) If has already recorded, then record the state associate with the sender process
> - Non- Marker Receiving Rule:
> the receiving process place the message at the end of the list.

The algorithm would probably not telling you some states (not recorded), and the final recorded state never is a global state. The final state would happen only if the first marker message is horizontal.

There are some observations about the algorithm: 1) The algorithm will definitely terminate; 2) The recorded state is a globally consistent state, but is not necessarily the system will actually occupied. **But it doesn't matter because no one will observe that anyway.** 3) Any stable properties that are true for the recorded state are also true for all post snapshot states


----------
**Distributed Debugging**
---
If we list all the possible states (lattice), then we can test if some property is possibly true, by **finding a set of states that all linearization will pass through**
