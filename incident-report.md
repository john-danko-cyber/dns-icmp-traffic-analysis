# Cybersecurity Incident Report: Network Traffic Analysis

## Part 1: Provide a summary of the problem found in the DNS and ICMP traffic log

**The UDP protocol reveals that:**
The browser used UDP to send a DNS query to the DNS server at 203.0.113.2, asking for the IP address of yummyrecipesforme.com. That query could not be delivered to the DNS service, so the domain name was never resolved and the website could not load.

**This is based on the results of the network analysis, which show that the ICMP echo reply returned the error message:**
"udp port 53 unreachable." In each attempt, the first two lines of the log show the outgoing UDP DNS request from the client computer at 192.51.100.15 to the DNS server, and the next two lines show the ICMP error the DNS server sent back.

> Note: The template wording says "ICMP echo reply," but the traffic in the log is actually an ICMP Destination Unreachable (Port Unreachable) response, not an echo reply.

**The port noted in the error message is used for:**
Port 53 is used for DNS (Domain Name System) traffic. DNS translates domain names, such as yummyrecipesforme.com, into IP addresses. The request details also point to DNS: the query ID 35084 is followed by a "+" sign, which shows flags set on the UDP message, and "A?" shows the request was a DNS query for the site's A record (its IPv4 address).

**The most likely issue is:**
The DNS server is not responding on UDP port 53. Either the DNS service on that server is down, or something is blocking traffic to port 53, so DNS requests are not being processed.

---

## Part 2: Explain your analysis of the data and provide at least one cause of the incident

**Time incident occurred:**
Approximately 1:24 p.m. The first DNS request in the capture is at 13:24:32.192571 (32.192571 seconds past 1:24 p.m.), and the matching ICMP error follows a few seconds later.

**Explain how the IT team became aware of the incident:**
Several customers contacted the client company to report that they could not reach www.yummyrecipesforme.com and were getting a "destination port unreachable" error when the page tried to load.

**Explain the actions taken by the IT department to investigate the incident:**
The IT team, which provides IT services to the client, began investigating so customers could get back to the website. The analyst reproduced the problem by loading the site while running tcpdump to capture the traffic, then reviewed the UDP DNS requests and the ICMP responses in the resulting log.

**Note key findings of the IT department's investigation (i.e., details related to the port affected, DNS server, etc.):**
The capture showed the client computer at 192.51.100.15 (source port 52444) sending a DNS query for yummyrecipesforme.com to the DNS server at 203.0.113.2 over UDP port 53. The query was sent three times, at 13:24:32, 13:26:32, and 13:28:32, and every attempt got the same ICMP reply from 203.0.113.2: "udp port 53 unreachable" (lengths 254, 320, and 150). This shows the packets were reaching the DNS server's host, but nothing was accepting DNS traffic on UDP port 53. The next step is to find out whether the DNS server itself is down or whether a firewall is blocking traffic to port 53, and to escalate the findings to the security engineers handling the incident.

**Note a likely cause of the incident:**
The DNS server may be down because of a successful Denial of Service (DoS) attack that overwhelmed it, or because of a misconfiguration, such as a firewall rule blocking UDP port 53 or the DNS service not running. Either way, DNS resolution failed, which kept users from reaching the website by its domain name.
