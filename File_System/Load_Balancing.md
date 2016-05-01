**Load Balancing**
===
Process scheduling means which process should work on which processor.

Load balancing is a strategy for how to do process scheduling. There are several policy that should be considered:

1. **Transfer Policy**: Decide whether it has to be transferred?
2. **Selection Policy**: Which process should I choose to make a move?
3. **Location Policy**: How to choose source destination?

If we want to transfer a process from one processor (sender) to another processor (receiver), then the process must be initiated either by sender or by the receiver. 

----------
**Sender-Initiate Algorithms**
---
The load distribution facilitates migration from a **heavily-loaded** sender to a **lightly-loaded** receiver. So in that case, the algorithm is better used in a **lightly-loaded** system, because light processors are relatively easy to be found.

A sender can use a transfer policy that initiates the algorithm when detecting its queue length exceeded a certain threshold upon the arrival of a new process. In that case, the sender would probe suitable receivers until it find one.

----------
**Receiver-Initiated Algorithm**
---
It is reasonable to have a receiver-initiated algorithm since there's a sender-initiate algorithm. Similarly, the processor activates the pull operation when its queue length falls below a certain threshold. A lightly loaded processor pull from the heavily loaded sender.

Based on that, the algorithm is better used in a **heavily loaded** system, where a sender can be found easily. 

The receiver-initiated algorithm is more stable than the sender-initiated algorithm. That is because in low traffic system, although the migration initiation might be plenty, the degradation of performance due to the additional network traffic is not significant. 

Combination of sender-initiated algorithm and receiver-initiated algorithm seems logical. Each node may dynamically play the role of either sender or receiver. Probing thus becomes unnecessary.