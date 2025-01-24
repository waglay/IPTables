# IPTables
I have tried to understand the working of iptables first to go tackle the ufw-docker problem.

## What I have learnt
- This is used to manage the internet traffic in the linux server.
---
1. There are three types of tables in IP tables:
   - Filter Table (firewall)
   - NAT table
   - Mangle table
2. Each table have chains which can be seen as processes they perform.
   So Filter Table has :
   - Input chain (incoming traffic to the server)
   - Output chain (outgoing traffic from the server)
   - Forward chain (using the router as a pivot to forward requests)

   NAT table has :
   - Output chain
   - Prerouting chain (pre routing is after the packets enter the NAT)
   - Postrouting chain (post routing is before it leaves the NAT)

   Mangle table contains all the chains

3. Each chains contain rules :
   The rules are set sequentially and the incoming packet is tested through all the rules.

4. A target is set, which means what is going to happen to the blocked packet afterwards:
   - they are accept (accepts and lets the service in)
   - reject (drops the packet and sends the user feedback)
   - block (entirely drops the packet as if it was not there or ever sent to the server).
5. If the packet does not match the rules in the chain, it will accept the default rules and if there are no default rules the server will accept the packet.
6. apt purge ufw. It is similar to remove but it removes everything including configurations and all.

7. Some commands in iptables as per my learning
   - Flush the iptable rules: ```iptables -F```
   - List out all the tables: ```iptables -L / iptables -L —line-numbers```
   - use ```man iptables``` for all the docs
   - By default the iptables that we are going to use is the filter tables
   - So syntax of iptables: ```iptable —policy INPUT ACCEPT```
8. So in input chain or any other chain, we can either append or put the rule on the top or bottom
   - so if we want to append it on the top we use -I
   - If we want to append we use -A
   - -s = source
   - -j = target
   - -p is for port
   - --dport = destination port
   - --sport = source port
9. So syntax is ```iptables -I/-A INPUT -s (ip address) -j (target) either accept, drop or reject```
10. We can also specify the entire subnet. eg instead of -s 192.168.33.10 we can use -s 192.168.33.0/24
11. to delete the rules:
```Iptables -D (delete) INPUT (name of the chain) 1 (number)``` which can be accessed by using ```iptables -L —line-numbers```.
12. to save the rule: ```sudo /sbin/iptables-save```
13. One thing I have learnt is that when installing applications, we cannot do so if we drop the input chain. I thought it was managed by the outgoing but no.
14. That happens as the outgoing is allowed the verification sort of request sent back will be blocked by the input chain. To enable it simply use:
    ```sudo iptables -A INPUT -m conntrack --ctstate ESTABLISHED,RELATED -j ACCEPT```
    Not only this there are several cases and it is really vast.
15. To check which ports are exposed to the world, use:``` ss -ltpn ```(ss is socket stats )
16. One of the solutions I have found to solve the ufw docker issue is to map the port to localhost only as in -p 127.0.0.1:80:80 by default it exposes to all the ports on the internet.
17. I tried but failed to test it as I am in a local environment.
**Note:** I learnt about it from [YouTube](https://www.youtube.com/watch?v=6Ra17Qpj68c).
# I also went through ufw and tries to solve the problem as per [chai feng](https://github.com/chaifeng/ufw-docker)

- I did everything that was mentioned in the repo but as I was doing it on virtual box I cannot tell the effectiveness.
- I also did a lot of testing myself if I could use the iptables to work with docker but failed.
- I tried ufw and tried to get to the problem statement, replicated the scenario but as I didnot had public ip access, I couldnot try.
- Overall I better understood the firewalls core iptables and hopefully the solution will work.
