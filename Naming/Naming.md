**Naming**
===
Naming is a mechanism to make machine a human readable identifier. URN (Uniform resource name) has sets of rules to define that. 

**Navigation**
---
**Conception:** Searching for final resolution of a name by traversing realms.

> **Notes: Types of navigation**
> 
>  - Multicast navigation: Client send request to different namespaces, once it finds the target, the namespace response with the answer.
>  - Iterative navigation: Every time client send request to namespaces, it replies with a namespace whether it's the target or not.
>  - Server controlled navigation: Server may take over clients job to perform navigation on behalf of client. The advantage of doing that is reducing the bandwidth, and results can be cached.
>   - Non-recursive server controlled: local namespace help client to do the multicast job;
>   - Recursive server controlled: Client sends request to local namespace, local namespace sends request to nearby namespace, nearby namespace sends request to another namespace... When target namespace is found, replies with the answer through the sending route back to the client.

**Domain Name System**
---
DNS is the most successful distributed system. It scales to millions of computers, and its name structure reflects administrative structure of internet. Its main job is to resolve domain names to IP addresses.

We need a "Yellow page" for resources in the network, which can be searched via "attribute" not just name.

> **Notes: Why not DNS?**
> DNS holds some descriptive data, but which is not quite complete, and also, more importantly, DNS isn't organized to search it.

**Methods:** Discovery Service (X.500, LDAP). Building dictionary for attributes... Examples are: Jini discovery service, Bonjour