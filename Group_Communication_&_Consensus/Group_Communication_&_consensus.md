Group Communication & Consensus
===

Part 1: Group Communication
---
 - **Multicast routing: two approaches**
	- Flooding: Messages sent to neighbors, percolating from sender throughout the network. In this approach we only need local knowledge at each other.
		- If it is played in an uncontrolled flooding way, then problem would be if two router forward message to each other, there would be an endless retransmission.
		-> One solution is that we add a unique sequence number when sending a message. Each message would only forward message it has never seen before.
		-> If you don't want to use sequence number, then you can choose **reverse path forwarding**. Messages are forwarded to all neighbors, but only if it arrived on shortest path back to source. To be clearer, messages would only get forwarded only when messages are coming from sender directly.
		-> More efficiently, **pruning control** could send messages upstream if no attached hosts are in the mcast group. This will prevent unnecessary message multicasting.
	- Spanning tree: Messages will be sent along predetermined paths, the shortcoming is that it requires complex maintenance of routing tree.
 - **Multicast communication**
	- Reliable multicast
Reliable multicast builds on top of basic multicast, the general mechanism is that if a message is delivered to any process in group, then it will be delivered to all process in group
**Hold-back queue**: messages should be delivered in certain order
	 - Multicast ordering requirements:
**Unordered**:  rare
**FIFO Order**: If a correct process multicast m1 -> multicast m2, then every correct process delivers m1 before m2. Implemented by sequence number.
**Causal Order**: Stronger version of FIFO Order, implemented by timestamp.
**Total Order**: implemented by ISIS algorithm

> **Notes: Difference between FIFO and Causal**
> According to the professor, FIFO means that the messages are put into the hold-back queue in the order that they are received by the node. The node may see the message in non-causal order. Causal order means that the node sorts the messages in order according to its causality (in certain circumstances, the timestamp).

ISIS Algorithm
> • Each Process keeps integers A and P
- A is largest agreed sequence # it has seen in the group - P is the largest sequence # it has proposed
• Receivers reply with a proposed sequence #, Max(A,P)+1 (and put in hold-back queue)
• Psender collects proposals from each receiver and multicasts a reply with largest one (now an agreed #)
• Receivers then insert message in hold-back queue sorted by smallest agreed sequence #
- Messages at head with agreed sequence # are delivered


----------

Part 2 Consensus
===

**Byzantine Generals**
Byzantine general problem describes a situation that some processes could be malicious processes. There are two different simple situations:

 1. Commander lies. If the commander lies, then different Lieutenants may get different commands and cannot get consensus on the order.
 2. Lieutenant lies. If Lieutenant lies, then the message sending between different Lieutenants may be inconsistent.

**Solution:** 
So the question relies on how to reach consensus among Lieutenants. If the number of Lieutenants (N) is more than the faulty Lieutenants (f), then it would be sufficient to reach consensus.
`N ≥ 3f + 1`
