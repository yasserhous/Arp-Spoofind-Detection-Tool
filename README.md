# ARP Spoofing Detection

## Overview

The primary goal of this project was to understand how to protect devices on public networks against a variety of attacks, starting with ARP spoofing. This attack is a common form of Man-in-the-Middle (MITM) attack, where an attacker can intercept and manipulate communication between two devices. By simulating the attack in a controlled environment, I gained valuable insights into how to detect and prevent ARP spoofing on real networks. The first step of the project was to create a spoofing attack. Once that was complete, use the knowledge gained to detect a similar attack

## Step 1 : The Attack
I performed a controlled spoofing attack on my home lab network:

** 1. Network Setup
I used two virtual machines: a Kali Linux VM as the attacker and another machine as the target. Both were configured in bridged mode to ensure they were on the same local network and could communicate directly.
![image](https://github.com/user-attachments/assets/0ef16ec6-f58c-49ef-b577-67769b467caf)


** 2. Performing ARP Spoofing
Using the arpspoof utility, I targeted the victim and the gateway by sending forged ARP replies. This redirected the victim's network traffic to the attacker, positioning the Kali machine as a man-in-the-middle.

** 3. Packet Forwarding and NAT Configuration
To allow traffic to continue flowing through the attacker, I enabled IP forwarding (echo 1 > /proc/sys/net/ipv4/ip_forward) and made the necessary NAT adjustments using iptables to route the traffic correctly.

** 4. Capturing Sensitive Data
The target machine accessed a test login form over HTTP (unencrypted). On the attacker VM, I used Wireshark to monitor network traffic and applied filters to capture HTTP POST requests. Following the HTTP stream revealed the credentials submitted by the victim.

** 5. Outcome
The spoofing was successful. The credentials submitted via the insecure site were clearly visible on the attacker's machine, demonstrating the critical risk of ARP spoofing on unsecured networks.

### Tools Used

Kali Linux: Chosen for its built-in penetration testing tools like Wireshark (for packet capture) and arpspoof (for performing the ARP spoofing attack).

arpspoof: Used to send ARP packets to the target and the router, redirecting the network traffic through the attacker's machine.

Wireshark: Utilized to monitor and capture network traffic, especially focusing on HTTP POST requests to steal credentials.

iptables & NAT configuration: Ensured traffic forwarding and seamless internet access for the target machine while maintaining control over the traffic.

### Challenges

Network configuration issues: The initial issue of not being able to see devices on the network due to the VM being in NAT mode was resolved by switching to Bridge mode.

Network connection instability: The target phone often blocked the attack due to suspicious behavior, requiring me to constantly reconnect the device to the network.

Packet forwarding and NAT rules: Proper setup of traffic forwarding and configuring the NAT table was essential to ensure the target's internet connection remained active during the attack.

### Attack Verification

The success of the ARP spoofing attack was verified by:

Monitoring network traffic on Wireshark, where I could see the packets from the target machine being captured.

Successfully intercepting sensitive data (such as credentials) from the victim machine by capturing unencrypted HTTP POST requests.

### Risks of ARP Spoofing
ARP spoofing in public networks poses serious risks, including:

Data interception: Sensitive data like login credentials can be intercepted, leading to identity theft, fraud, or impersonation.

Denial of Service (DoS): The attack can cause network disruption if not mitigated.

MITM attacks: The attacker can inject malicious content or alter communications between devices.\

### Defensive Measures

To protect against ARP spoofing, I recommend:

Using VPNs on public networks to ensure all data is encrypted.

Implementing ARP spoofing detection tools that monitor the network for suspicious activity, such as multiple MAC addresses mapping to the same IP address.

Configuring static ARP entries in critical systems to avoid relying on dynamic ARP resolutions.

## Conclusion
This project provided me with hands-on experience in both performing and defending against ARP spoofing attacks, enhancing my understanding of network security and penetration testing. It also highlighted the importance of encryption and continuous monitoring in securing sensitive data on public networks.


