## External Connection of the 5G Core

When working with Docker containers, each application or service is usually isolated in its own internal network.  
This is useful for security and organization, but it creates a limitation: services can only communicate within that restricted network.

In the case of the **5G Core**, we need **all modules (AMF, SMF, UPF, NRF, etc.) to communicate with each other** reliably, and in addition, some of them must be accessible externally — for example, by a base station (**gNodeB**) or by a simulated **UE (User Equipment)**.

If each container were on a different network or only had private addresses, this communication would be impossible.  
Therefore, the solution was:

1. **Create a dedicated Docker network**  
   This network functions like a “virtual switch,” allowing all containers to connect.

2. **Move all containers to this network**  
   This way, they all share the same IP address space.

3. **Assign fixed (public) IP addresses to each container**  
   Previously, each container had only a private IP address, which was automatically assigned.  
   Now, we manually define IP addresses within the new network, ensuring that each Core module can be uniquely identified and accessed.

4. **Result**  
   - The Core operates as if it were on a real public network.  
   - UEs and the gNodeB can reach the Core services directly through the new IPs.  
   - Communication between internal modules also becomes stable, since the addresses no longer change every time the containers are restarted.
---

**Files**

- [setup guide](setup-guide.md)
