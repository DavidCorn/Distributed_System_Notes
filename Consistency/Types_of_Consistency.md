**Types of Consistency**
===
Performance and consistency are always a pair of tradeoff.

**Strict Consistency**
---
If process updates at T1, and then updates at T2, when read data between T1 and T2, following the strict consistency rule, the user would read data at T1.

Strict consistency is not desired and not often used, except in micro-processors.

**Sequential Consistency**
---
Sequential consistency is relatively simple, all the processors agree on one sequential order. It is often used in distributed shared memory and distributed transactions. 

This rule requires that the write operation to be seen by all the processors immediately.

**Causal Consistency**
---
Causal consistency requires write operations in "->" order. Yet concurrent operations could be seen in different order in different processors (different from sequential consistency).

This rule could be **implemented** with vector clock to order updates.

**Eventual Consistency**
---
In this situation, read is assumed to be more than write. Data that is received slightly out of date is acceptable.

The model is described as client-centric model, which means that if the eventual results (writes) are propagated, then the rule is met.

In one sentence, **In the absence of updates, all writes will propagate eventually**.

**Implementation** is quite simple: Give data items a lifetime, after which replica must read the item again

**Entry/weak Consistency**
---
In weak consistency, it only requires that data that enters into critical section is in the same order. Before giving ownership to another process, the data went into the critical section must be brought up to date.