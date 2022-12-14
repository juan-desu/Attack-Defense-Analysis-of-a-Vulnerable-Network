# Blue Team: Summary of Operations

## Table of Contents
* Network Topology
* Description of Targets
* Monitoring the Targets
* Patterns of Traffic & Behavior
* Suggestions for Going Further

### Network Topology

The following machines were identified on the network:
- Hypervisor / Host Machine
  - **Operating System**: Microsoft Windows
  - **Purpose**: Hypervisor / Gateway
  - **IP Address**: 192.168.1.1
- Kali
  - **Operating System**: Linux
  - **Purpose**: Pen Testing
  - **IP Address**: 192.168.1.100
- ELK
  - **Operating System**: Linux
  - **Purpose**: Elasticsearch, Logstash, Kibana Server
  - **IP Address**: 192.168.1.100
- Capstone
  - **Operating System**: Linux
  - **Purpose**: Basic HTTP Server
  - **IP Address** 192.168.1.105
- Target 1
  - **Operating System**: Linux
  - **Purpose**: HTTP Server / Wordpress site
  - **IP Address** 192.168.1.110

### Description of Targets

The target of this attack was: `Target 1` (192.168.1.110).

Target 1 is an Apache web server and has SSH enabled, so ports 80 and 22 are possible ports of entry for attackers. As such, the following alerts have been implemented:

### Monitoring the Targets

Traffic to these services should be carefully monitored. To this end, we have implemented the alerts below:

#### Excessive HTTP Errors

Alert 1 is implemented as follows:
  - **Metric**: http.response.status_code > 400
  - **Threshold**: 5 in the last 5 minute
  - **Vulnerability Mitigated**: With this alert implemented, we can identify attacks and block the ip, change the password, and close or filter port 22  
  - **Reliability**: This alert does not generate lots of false positives. This alert is highly reliable in identifying brute force attacks. 

#### HTTP Request Size Monitor
Alert 2 is implemented as follows:
  - **Metric**: http.request.bytes
  - **Threshold**: 3500 in the last 1 minute
  - **Vulnerability Mitigated**: With this alert implemented, we are able to control the number of http request size through a filter and protect against DDOS attacks.
  - **Reliability**: This alert does not generate lots of false positives so its reliable

#### CPU Usage Monitor
Alert 3 is implemented as follows:
  - **Metric**: system.process.cpu.total.pct
  - **Threshold**: 0.5 in the last 5 minutes
  - **Vulnerability Mitigated**: With this alert implemented, an alert will trigger when the CPU usage rises above 50%
  - **Reliability**: CPU usage is not indicative of an attack given that usage can spike when in normal or intensive use. A low reliability rating must be given to this alarm due to its possibility of false positives.

### Suggestions for Going Further (Optional)
- Each alert above pertains to a specific vulnerability/exploit. Recall that alerts only detect malicious behavior, but do not stop it. For each vulnerability/exploit identified by the alerts above, suggest a patch. E.g., implementing a blocklist is an effective tactic against brute-force attacks. It is not necessary to explain _how_ to implement each patch.

The logs and alerts generated during the assessment suggest that this network is susceptible to several active threats, identified by the alerts above. In addition to watching for occurrences of such threats, the network should be hardened against them. The Blue Team suggests that IT implement the fixes below to protect the network:
- Excessive HTTP Errors
  - **Patch**: TODO: Implement a stronger password policy in the user account settings and require public key authorization.
  - **Why It Works**: With a stronger password and public key authorization it will almost impossible to guess or brute force.
- HTTP Request Size Monitor
  - **Patch**: Implement an IPS (Intrusion Prevention System) or IDS (Intrusion Detection System) on the network.
  - **Why It Works**: Given that DDoS attacks are extremely hard due to their complexity, it's hard defending against it them without proper baselining.
- CPU Usage Monitor
  - **Patch**: Implement a Host Intrusion Prevention System to identify DOS attacks
  - **Why It Works**: The host-based IPS will stop malware from installing by running deep packet analysis
