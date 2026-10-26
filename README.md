# DNS and ICMP Network Traffic Analysis

## Project Overview

This project documents a guided lab from the Google Cybersecurity Certificate, "Analyze network protocol and network traffic." The lab provides a scenario and a tcpdump log captured by a cybersecurity analyst at an IT services company. I reviewed the scenario, analyzed the DNS and ICMP traffic in the log, and wrote an incident report explaining why customers could not reach a client's website.

The analysis showed that DNS lookups for the site were failing because the DNS server returned "udp port 53 unreachable" for every request.

---

## Scenario

Customers of a client company started reporting that they couldn't load www.yummyrecipesforme.com and were getting a "destination port unreachable" error. To investigate, the analyst in the scenario loaded the site while capturing traffic with tcpdump. The capture showed the client computer at 192.51.100.15 sending DNS lookups over UDP to the DNS server at 203.0.113.2 on port 53, and every lookup came back with an ICMP error saying "udp port 53 unreachable." Security engineers in the scenario were handling the incident. My part was to review the provided log, analyze the traffic, and write up the incident report.

---

## Tools Used

- tcpdump log (network protocol analyzer capture provided in the lab)
- Google Cybersecurity Certificate incident report template

---

## Key Findings

- The client computer at 192.51.100.15 sent a UDP DNS query to 203.0.113.2 on port 53 asking for the A record (IPv4 address) of yummyrecipesforme.com. The "+" after query ID 35084 shows flags set on the request, and "A?" marks it as an A-record lookup.
- The query was sent three times, at 13:24:32, 13:26:32, and 13:28:32, starting around 1:24 p.m.
- Every attempt got an ICMP response from 203.0.113.2: "udp port 53 unreachable" (lengths 254, 320, and 150).
- Port 53 is DNS, so the site's name could not be resolved and the browser never got as far as sending HTTPS traffic.
- Next steps: check whether the DNS server is down or a firewall is blocking port 53.
- Likely causes: a DoS attack that took the DNS server down, or a misconfiguration such as a firewall rule blocking UDP port 53.

**A note on the ICMP message:** the report template asks about an "ICMP echo reply," but the traffic in the log is actually an ICMP Destination Unreachable (Port Unreachable) message, not an echo reply. I kept the template wording in the report and added a note about it.

### Tying it to the TCP/IP model

- **Application layer:** DNS is the service that failed. No name resolution meant no website.
- **Transport layer:** the DNS queries used UDP to destination port 53, which is where the failure shows up.
- **Internet layer:** IP delivered the packets between 192.51.100.15 and 203.0.113.2, and ICMP carried the error back. The server host was reachable even though the DNS service was not.
- **Network access layer:** traffic was moving on the wire, since the capture shows packets in both directions.

---

## Project Documentation

### [Incident Report](incident-report.md)
The completed Google incident report template covering the problem summary and the analysis of the cause.

### [tcpdump Log](tcpdump-log.txt)
The packet capture lines from the lab, transcribed as text.

### [Report Template](images/report-template.png)
The blank template used for the report.

---

## Skills Demonstrated

- Reading and interpreting tcpdump output
- Reading DNS and ICMP traffic
- Understanding UDP and port numbers
- Applying the TCP/IP model to troubleshooting
- Incident documentation

---

## What I Learned

- How to read a tcpdump line: timestamp, source and destination IP and port, flags, and query type.
- An ICMP error can show that a host is up while the service on it is not.
- When DNS fails, everything that depends on a domain name fails with it, even if the web server itself is fine.
- Template wording is not always technically exact, so it is worth checking what the packets actually say.

---

## Disclaimer

This is a guided lab completed as part of the Google Cybersecurity Certificate. The company, website, and IP addresses are part of a training scenario, not a real production environment.

This project is based on a scenario from the Google Cybersecurity Professional Certificate (Coursera). The write-up and analysis are my own.
