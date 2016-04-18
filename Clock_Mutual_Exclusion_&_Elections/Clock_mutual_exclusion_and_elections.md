Clock, mutual exclusion and elections
===

Part 1 Time and Clock
---
Lamport's Logical Clock concerns the **events order** in system, it could let us know the order of events within 1) same thread; 2) different processes in same host; 3) different computers.

The core concepts of logical clock is described as "**happened before**" (causally), and the relationship is transitive. For example, if `"a->b"`, and `"b->c"`, then `"a->c"` must be true.

If we define the timestamp of a specific event a as `Ci(a)`, i means the index of process `Pi`, if `"a->b"`, then `"Ci(a) < Ci(b)"`.

Calculating `Ci`: if the previous timestamp of the process is Tm, then the current timestamp should be `Ck:=max{Ck,Tm}+1`, Ck in the bracket means the sender's latest timestamp.

The **ugly** example below clearly illustrate the algorithm:
```sequence
Note over A: 1
Note over A: 2
A->B: send(Ca[2])
Note over B: 3
Note over B: 4
Note over A: 3
B->A: send(Cb[4])
Note over A: 5
```
Such logical clock mechanism would clearly identify the events ordering when sending a message. However, the events ordering could not be deducted by reversely comparing the timestamp, to be more clear, `"C(a) > C(b)"` can't tell `b->a`, as you can see in the sequence above.

In order to solve that problem, we introduce **vector clock**.

We define `Pi` has timestamp `Ci=(Ci[1], Ci[2],...,Ci[n])`. When a process execute a local message, it simply updates its own timestamp (e.g. by adding 1) `Ck:=Ck+1`; if a process receives a remote message, the timestamp that doesn't represent its own would update with the larger one between the received timestamp and its previous timestamp. its own timestamp would update like this: `Ck:=max{Ck,Tm}+1`.

```sequence
Note over A: (1,0,0)
Note over B: (0,1,0)
Note over C: (0,0,1)
A->B: send(C[1,0,0])
Note over B: (1,2,0)
Note over A: (2,0,0)
Note over C: (0,0,2)
C->A:send(C[0,0,2])
Note over A: (3,0,2)
Note over C: (0,0,3)
C->B: send(C[0,0,3])
Note over B: (1,3,3)
```
> **Note**: How vector clock overcome the reverse comparison limitation of logical clock?

> - When it comes to **comparison**, only if all the elements in the timestamp vector is smaller or equal than the compared vector, can we say that the event happens before the compared event, and **vice versa**!


----------


Part 2 Mutual Exclusions
---
**Strategy**

 - Centralized ME
 A process Pi sends a request message to a coordinator process, asking for granted permission to access the coordinator. When process Pi receives the granted message and executes in the critical session, it would send release message to the coordinator.
 - Distributed ME
	 - Token-based algorithm
The processes only have to know about their successors. We put the processes into the ring. When process needs critical session, it would send token into the token ring. If current process needs CS, holds token and execute. Else passes the token to its successor.
> **Notes:** Problem of token-based algorithm
>  - The algorithm is not fault tolerant. What if the token is lost? What if the token is duplicated? Which one goes first? Block situations?
>  - The algorithm would cause performance problems such as synchronization delay and response delay by passing tokens to successors one by one.

	 - Ricart & Agrawala algorithm (Improvement of Lamport's algorithm)
The algorithm uses logical clock to track down events order, broadcasting event timestamps. When the receiver receives multiple timestamps, it would compare the timestamps, making the larger one to stay in queue and forwarding "OK" message to the small timestamp sender.
> **Note:** Problems of Ricart & Agrawala algorithm
> - Still not fault tolerant because of broadcasting, what if one receiver fails down?
> - Also, the algorithm is only optimal with respect number of messages. If the message get to be flooding, the network will be burdened because of broadcasting.


	 - Maekawa's algorithm
Maekawa's algorithm overcomes flooding message problem in Ricarts & Agrawala's algorithm by configuring **Request Sets**, it also introduces concepts of "vote" to records whether the process has granted some other process to enter the critical session.
> **Four properties**
> - **Pairwise Non-null Intersection Property** 
> (**required**)
> It guarantees that no two groups would enter CS at the same time. (CS is for critical session)
> rest of the rules are not mandatory, but will let the algorithm be more efficient
> - **Self-contain property**
> One less messages need to send through network per group
> - **Equal-effort property**
> Which means that each group has same amount of members. Used for balancing the load of each nodes.
> - **Equal-responsibility property**
> Which means that every node is in the same number of groups. Used for relieving burdens.

 - **Leader Election**
	 - Ring-based elections
Any Pi begins election by sending election messages to its successor. It will find the largest process id and select it as the leader. If leader is selected, it would send "victory" message to other processes.

	 - Bully algorithm
Happens when Pi detects failure of coordinator:
Bully algorithm also relies on the process id. After a process broadcasts election messages to others, the processes with smaller id do not response, meanwhile the processes with larger id response with answering messages, and start other elections
To the sender itself, if it doesn't receive answering messages, it broadcasting victory message. If it receives answering messages, it waits for victory messages. If it doesn't receive victory messages after a while, it would restart election.

