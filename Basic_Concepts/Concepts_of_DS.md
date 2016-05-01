Concept of DS
=====================

DS Concepts
---------

 - A distributed system is one where several computations coordinate across machine boundaries
 - To coordinate, they must communicate, and must know of each other’s existence
 -  To coordinate, they must have common things (state) to “talk” about

----------


DS Challenges
-------------
 1. Global Resource Access
 2. Concurrency
 3. High Availability / Fault Tolerance (**Inevitable**)
 4. Transparency
 5. Management and Maintenance
 6. Security
 7. List item
 8. Scalability
 9. Timeliness (**Intolerant** for real-time system)
 10. Openness (Publishing key programming interfaces)

> **Note: simple Web-based DS work**

> Client-server
> 
> - Clients: Generalized, large number, consumer of service
> - Protocol: File retrievals, SQL, HTTP over TCP/IP
> - Server: specialized, a provider of services (file services, db services, groupware services, web services)
> - Naming, authentication, authorization, auditing and logging, billing
> - Load balancing
> 
> Difference between consistency and consensus
> 
> Consistency means the content and time ordering should be consistent in different nodes, consensus means that different nodes share an agreement on certain protocol or value.