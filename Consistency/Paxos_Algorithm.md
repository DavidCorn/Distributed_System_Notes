**Paxos Algorithm**
===
**Conceptions and Assumptions**
---
Paxos algorithm is one of the most brilliant ideas (perhaps the most brilliant, according to [Michael Burrows](http://research.google.com/pubs/author24014.html)) talking about how fault-not-so-tolerant system reaches consistency on some value.

Above all, there are some assumptions:
1. Assume that all the processors are **non-evil** processors, which means we consider the system as non-byzantine system;
2. All processes communicate with asynchronous messages;
3. All processes have durable storage available which can be read after a process failure.

Based on the assumptions above, Paxos guarantees that **whatever faults happens in the system, the consistency protocol would never get wrong**.

**Roles in Paxos**
---
 - **Proposers**: They propose a value to have consensus on.
	- **Leader: ** A distinguished proposer, required to make progress.
 - **Acceptors: ** They hear about the proposals and accept them.
 - **Learners: ** They wish to learn about the agreed values.

**One Value Scenario**
---
First we describe the situation on how one value is agreed in Paxos.

1. Proposer send $M_{prepare}$ containing value $V$ to a quorum of Accepters, the proposal contains a globally unique number $N$.
2. Accepter keeps track of its **highest** previous accepted number $N_{prev}$ and its value $V$. When it receives $M_{prepare}$, it compares $N$ and $N_{prev}$, if $N > N_{prev}$, send $M_{promise}$ back;
3. When Proposer receives a quorum of $M_{promise}$, then it send $M_{accept}$ to the quorum of Acceptors.
4. Upon receiving the accept message, if $N > N_{prev}$, it must accept the message. After reaching consistency between acceptors, they send accepted message to the learner.

**Multi-value Scenario**
---
If different proposers send multiple values, what happened? The author of **[Paxos Made Simple](http://research.microsoft.com/en-us/um/people/lamport/pubs/paxos-simple.pdf)** (which is not simple at all...) describes the general phases.

> **Notes: General Phases of Paxos**
> **Phase 1**
> (a) A proposer selects a proposal number n and sends a prepare request with number n to a majority of acceptors.
> (b) If an acceptor receives a prepare request with number n greater than that of any prepare request to which it has already responded, then it responds to the request with a promise not to accept any more proposals numbered less than n and with the highest-numbered pro-posal (if any) that it has accepted.
> **Phase 2**
> (a) If the proposer receives a response to its prepare requests (numbered n) from a majority of acceptors, then it sends an accept request to each of those acceptors for a proposal numbered n with a value v , where v is the value of the highest-numbered proposal among the responses, or is any value if the responses reported no proposals.

**Situation 1:**
If the second proposal receives at the time when the first proposal is accepted, then the Acceptor would send $M_{promise}$ back with its current $N$ and $V$. Upon receiving the $N$ and $V$, the second Proposer then broadcast the message with certain $V$.

**Situation 2: **
If before accepting the value, the proposal receives a proposal with a bigger $N$ (smaller $N$ would be discarded directly), then the pending accepted $V$ would be discarded. The original $M_{accepted}$ would be replaced as $M_{rejected}$.

This [blog](http://codemacro.com/2014/10/15/explain-poxos/) gives a very easy and clear overview of Paxos algorithm. [Wiki](https://en.wikipedia.org/wiki/Paxos_%28computer_science%29) also did a good job on this.