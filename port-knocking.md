# SadServers Challenge: "Taipei" Come a-knocking

### Scenario Information
*   **Name:** "Taipei": Come a-knocking
*   **Level:** Easy
*   **OS:** Debian 11
*   **Access:** No root (sudo)
*   **Time Limit:** 15 minutes

### Question / Challenge
There is a web server on port `:80` protected by **Port Knocking**. Find the single "knock" (sending a SYN packet to one specific port, not a sequence) required to open the firewall so you can `curl localhost`.

**Success Criteria:** 
Executing `echo $(curl localhost)` must return a message with the MD5 hash: `fe474f8e1c29e9f412ed3b726369ab65`.

---

### Solution Steps

#### 1. Identify the Trigger Port
Since this is an "Easy" level with a single-port knock, an **nmap** scan of all ports is the most efficient way to "trip" the sensor. As `nmap` probes each port, it will eventually hit the secret trigger.

```bash
# Scan all ports (1-65535) on localhost
nmap -p- localhost
Use code with caution.

Note: The nmap scan acts as the "knock" itself by sending SYN packets to the closed ports.
2. Verify Port 80 Access
Once nmap hits the correct port (typically 1234 in this scenario), the knockd daemon dynamically updates the firewall (iptables) to allow traffic to port 80.
bash
# Test the connection
curl localhost
Use code with caution.

Output: Who is there?
3. Confirm Success with MD5
To ensure the output matches the challenge requirements:
bash
echo $(curl localhost) | md5sum
Use code with caution.

Result: fe474f8e1c29e9f412ed3b726369ab65 -
Key Concepts
Port Knocking: A security method where ports are kept closed by a firewall and only opened after a specific connection attempt (the "knock") is detected.
SYN Scan: nmap uses SYN packets by default, which is exactly what the knockd service looks for to trigger its rules.
Dynamic Rules: The firewall rule created by the knock is often temporary (e.g., 10-60 seconds) or specific to the source IP.


