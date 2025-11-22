# Bash-Network-Automation-Day-11-
Automate and manage network tasks efficiently with powerful Bash scripts for diagnostics, configuration, and monitoring.

---
```
ifconfig
```
- Error :-bash: ifconfig: command not found
```
sudo apt update
sudo apt install net-tools
ifconfig eth0
```
- eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1400
        inet 172.29.204.130  netmask 255.255.240.0  broadcast 172.29.207.255
        inet6 fe80::215:5dff:fecd:ba58  prefixlen 64  scopeid 0x20<link>
        ether 00:15:5d:cd:ba:58  txqueuelen 1000  (Ethernet)
        RX packets 519  bytes 507301 (495.4 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 230  bytes 16730 (16.3 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```
ifconfig eth0 up
```
- Error:SIOCSIFFLAGS: Operation not permitted
```
sudo ifconfig eth0 up
```
```
route -n
```
- Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         172.29.192.1    0.0.0.0         UG    0      0        0 eth0
172.17.0.0      0.0.0.0         255.255.0.0     U     0      0        0 docker0
172.29.192.0    0.0.0.0         255.255.240.0   U     0      0        0 eth0

```
lp a               # Show IP addresses
ip addr show       # Show all IP addresses for all interfaces
ip route show      # Show routing table (default gateway, routes)
route-n            # Show old-style routing table in numeric form
ip link show       # Show network interface status (UP/DOWN)
```
---
###### Write a script to configure static IP, gateway, DNS automatically:
```
#!/bin/bash

# Usage: sudo ./configure_network.sh eth0 192.168.1.100 255.255.255.0 192.168.1.1 8.8.8.8
INTERFACE=$1
IP=$2
NETMASK=$3
GATEWAY=$4
DNS=$5

echo "Configuring $INTERFACE with static IP $IP..."

nmcli con mod $INTERFACE ipv4.addresses "$IP/$NETMASK"
nmcli con mod $INTERFACE ipv4.gateway "$GATEWAY"
nmcli con mod $INTERFACE ipv4.dns "$DNS"
nmcli con mod $INTERFACE ipv4.method manual
nmcli con up $INTERFACE

echo "Network configuration applied!"
```
###### Automate Ping & Traceroute Analysis with Logging
```
#!/bin/bash

HOSTS=("google.com" "yahoo.com" "example.com")
LOGFILE="network_analysis.log"

for HOST in "${HOSTS[@]}"
do
  echo "Pinging $HOST..." | tee -a $LOGFILE
  ping -c 4 $HOST | tee -a $LOGFILE
  echo "Traceroute to $HOST..." | tee -a $LOGFILE
  traceroute $HOST | tee -a $LOGFILE
  echo "--------------------------" | tee -a $LOGFILE
done

echo "Diagnostics complete. See $LOGFILE for details."
```
######  Parse Routing Tables & Generate Formatted Reports
```
import subprocess

def parse_routes():
    result = subprocess.check_output(['netstat', '-rn']).decode()
    report = []
    for line in result.split('\n'):
        if line.startswith('Destination') or line.strip() == "":
            continue
        fields = line.split()
        if len(fields) >= 8:
            report.append({
                'Destination': fields[0],
                'Gateway': fields[1],
                'Genmask': fields[2],
                'Iface': fields[-1]
            })
    print("{:<15} {:<15} {:<15} {:<10}".format('Destination', 'Gateway', 'Genmask', 'Iface'))
    for entry in report:
        print("{:<15} {:<15} {:<15} {:<10}".format(entry['Destination'], entry['Gateway'], entry['Genmask'], entry['Iface']))

if _name_ == "_main_":
    parse_routes()
```
######  Test Service Availability Across Multiple Hosts
```
#!/bin/bash

HOSTS=("192.168.1.10" "192.168.1.20")
PORT=80

for HOST in "${HOSTS[@]}"
do
  echo -n "Testing $HOST:$PORT... "
  timeout 2 bash -c "echo > /dev/tcp/$HOST/$PORT" 2>/dev/null
  if [ $? -eq 0 ]; then
    echo "Available"
  else
    echo "Unavailable"
  fi
done
```
---
### Task:
####  Package Management
---
###### Task Description:
- A package‑audit demonstration showed stale package indexes that were never refreshed. Trainers want a detection of outdated APT metadata files.

---
###### Overview
- This script finds stale APT index files (older than 7 days) and saves them for review.
You’ll verify the output and document your environment for submission.
---
###### Find Stale Index Files
```
find /var/lib/apt/lists -type f -mtime +7 > s28_q3_staleindex.txt
```
 - find /var/lib/apt/lists: Search inside the APT lists folder.
 - -type f: List only files.
---
###### Verify Output File
```
cat s28_q3_staleindex.txt
```
- cat Shows File Content
---
###### Document Your System
```
hostname
whoami
```
 - hostname - Shows your hostname
 - whoami - Shows your username
---
###### Explanation
 - I ran the required command to find all APT index files older than 7 days and saved them in s28_q3_staleindex.txt.
----
###### Clean Outdated Metadata
```
sudo apt clean
sudo rm -rf /var/lib/apt/lists/*
sudo apt update
```
- This Command Clean The Outdated metadata
---
