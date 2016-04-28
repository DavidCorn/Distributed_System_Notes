**High-Availability**
===
**Concepts**
---
**Availability** means that machine/system runs continuously. Meanwhile the system would keep the maintenance outage under operator control, keep the costs low and keep the implementation overhead low.
If we define MTTF as Mean time to failure, and MTTR means Mean time to repair, availability is calculated as 
$Av = MTTF/(MTTR + MTTF)$
The closer Av is to one, the stronger availability system is.

**Difference between failure, fault and error**

 - **Failure**: Deviation from specified behavior
 - **Error**: Leading a wrong result
 - **Fault**: Something goes wrong at the lowest level

**Crash failure** means a server halts, but it is working properly before until it halts.

> **Notes: Difference between availability and fault tolerant**
> Availability is about finding a **non-crashed source**
> Fault tolerant is about finding the **correct answer**

**Fault tolerant techniques**
---
1. TMR (Triple Modular Redundancy)
The method uses a **Voter** to collect data from different units, and gives the output which most units agree on. The technique requires that voter is much more reliable than the units, and correct units generate the same outputs. 
The tradeoff of this architecture is that the system is too complicated and expensive.

2. Master-Slave configuration
Master and slave node have the same input, slave would simulate the output of the master. When there's discrepancy between master and slave, both master and slave would shut down.
The tradeoff is that the architecture wont't tell that which one (slave or master) goes wrong.

**Replication and consistency**
---
**Consistency** hereby means that any change to one replica must be reflected at all replicas.
The price of consistency is also obvious. When failure happens, performance costs, and even when there's no failure, performance also costs (e.g. too many replicas)
**Generally, for a given performance level, higher availability can be arrived with weaker consistency.**