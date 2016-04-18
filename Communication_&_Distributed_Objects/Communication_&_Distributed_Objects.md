Communication & Distributed Objects
===

Features of various communication paradigms
---

 - **Message Passing**
	 - Basic interprocess communication among distributed system. You need to konw 1) the message; 2) the name of source; 3) the destination process; 4) data type expected for process.
	 - MP is performed using **send(receiver, message)** and **receive(sender, message)** primitives;
	 - Pros and cons of non-blocking send() and receive(): **advantage** is that the CPU will not remain idle, **disadvantage** is that the sender process will never know whether its local process has been cleared or not.
	 - Buffering: Buffering mainly deals with the embarrassed situation that when sender sends a message whose destination process is not initiated yet or not found by now. The buffered messages are buffered until the messages are ready to be processed.
	 - In MP, the explicit movement of data is exposed
 - **Remote Procedure Call**
	 - Higher level of abstraction.
	 - Message passing leaves the programmer with the burden of the explicit control of the movement of data. Remote procedure calls (RPC) relieves this burden by increasing the level of abstraction and providing semantics similar to a local procedure call.
	 - RPC is performed using syntax **call procedure_name()**, **receive procedure_name()**, **reply(caller, result)**.
 - **Distributed Shared Memory**
	 - Two resources for understanding: [slides](http://www.slideshare.net/SheriMeri/message-passing-remote-procedure-calls-and-distributed-shared-memory-as-communication-paradigms-for-distributed-systems-remote-procedure-call-implementation-using-distributed-algorithms), [Message Passing, Remote Procedure Calls and Distributed Shared Memory as Communication Paradigms for Distributed Systems](http://citeseerx.ist.psu.edu/viewdoc/download;jsessionid=3574842F26F1C9865BAC4204F5463D21?doi=10.1.1.95.2490&rep=rep1&type=pdf)

> **Notes: Why not message passing but RPC?**
> 1. Too explicit, though least abstract yet complicated for programmers, programmers should take care of all synchronization codes;
> 2. Programmers may have to code format conversion, flow control and error handling;
> 3. MP is I/O oriented, rather than request/result oriented. RPC hides all MP I/O from programmers. It looks like a procedures call -- which is actually invoking procedures on a server.

----------

Summary of [Birrel84. Implementing Remote Procedure Call]
---
- **Goals of Implementing RPC**
 - Make distributed computation easier. Remove unnecessary difficulties, leaving only fundamental difficulties: timing, independent failure of component, and the coexistence of independent execution environments;
 - Make efficient RPC calls;
 - Make RPC semantics just like local procedure calls;
 - Provide secure RPC communication;

- **Basic structure and transmitting process**
 1. The program structure is based on the concept of stubs;
 2. When the programmer wishes to make a remote procedure call, he makes a **normal local call**, and the local call will be automatically packed in the user-stub, and transmitted to the callee machine (remote machine) through **call procedure_name()** by RPCRuntime, the caller machine (client machine) would then be suspended;
 3. When the RPCRuntime in the Callee machine receives the packet,it would send the packet to the server-stub and unpack the packet, the server would do a **perfect local call** using the unpack arguments;
 4. After executing the data, the server would pack the results and send it back to the caller machine. Procedure here is similar as previous.

``` sequence
Title: Interaction of a simple call
Note over Caller machine: make a local call
Note left of Caller machine: pack the call\n within user-stub
Note left of Caller machine: transmit using\n RPCRuntime
Caller machine->Callee machine: Call packet(packet, dest)
Note left of Caller machine: suspended\n by RPCRuntime
Note right of Callee machine: receive\n by RPCRuntime
Note right of Callee machine: unpack argument\n by server stub
Note over Callee machine: execute local call
Note over Callee machine: return
Note right of Callee machine: pack result\n by stub,\n transmit, call...
Callee machine-->Caller machine: result packet(packet, source)
```

 - **Binding**
	 - Binding with whom? -> Naming
		 - The semantics of an interface name are not dictated by the RPC package;
		 - However the means by which an exporter uses the interface name to locate an exporter are dictated by the RPC package.
	 - how to determine the machine address of the callee? -> Location
		 - [Grapevine distributed database](https://users.soe.ucsc.edu/~sbrandt/221/Papers/Dist/schroeder-tocs84.pdf)
 - **Complecated calls**
	 - Reasonable strategy: Retransmitting
		 - The caller periodically sends probes to the callee, to check if the callee has crashed or if there's some serious communication failure, and to notify the user of an exception. If there's acknowledgement feedback, then the server has no failures.
		 - Retransmitting handles lost packets, long duration calls, and long gaps between calls.
		 - Cons: only detects failure, not callee deadlocks; When there's long duration calls or large gaps between calls, message could be in big numbers.
	 - **Alternative strategy**: to have the recipient of a packet spontaneously generate an acknowledgement if he doesn't generate the next packet significantly sooner than the expected retransmission interval. Like a heartbeat checking method.

Limitation of RPC
---

 - Pointer has no meaning when it refers to different machines, so no shared address space is a problem;
 - Binding data happens at run-time, which would consume time;
 - "Interesting" run-time errors may happen (dynamically linking errors);
- Failure machines are independent, somewhere the communication can be transmitive;
- Security issues;
- Tough to do broadcast. To do broadcast thing in RPC you should do multiple RPCs based on the number of remote machines you want to communicate with.

---

Distributed Objects
---
Distributed Objects are layers between OS and application, it hides the OS and network stack, solves and extends RPC.

CORBA (Common Object Request Broker Architecture)
---
- CORBA is a standard DS mechanism, that makes building DS easier. ORB is a broker that helps figure out how senders match out to receivers.
- CORBA is language and location transparent, which means you can write it in any languages and the client and server don't know each others' locations.
- They use a GIOP (General Inter-ORB Protocol) common communication protocol to talk to each other in different OS and different machines.