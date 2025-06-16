# ARP Spoofing Detection

## Overview

The primary goal of this project was to understand how to protect devices on public networks against a variety of attacks, starting with ARP spoofing. This attack is a common form of Man-in-the-Middle (MITM) attack, where an attacker can intercept and manipulate communication between two devices. By simulating the attack in a controlled environment, I gained valuable insights into how to detect and prevent ARP spoofing on real networks. The first step of the project was to create a spoofing attack. Once that was complete, use the knowledge gained to detect a similar attack

## Step 1 : The Attack
I performed a controlled spoofing attack on my home lab network:

** 1. Network Setup
I used two virtual machines: a Kali Linux VM as the attacker and another machine as the target. Both were configured in bridged mode to ensure they were on the same local network and could communicate directly.
![image](https://github.com/user-attachments/assets/0ef16ec6-f58c-49ef-b577-67769b467caf)


** 2. Performing ARP Spoofing
Using the arpspoof utility, I targeted the victim and the gateway by sending forged ARP replies. ARP stands for Address Resolution Protocol. This protocol is used by machines when they are trying to find the location of a certain ip on the network. the location is the MAC address. Every machine has a unique MAC address. ARP spoofing is the act of telling the victim machine that the ip its looking for is located at the MAC address of the attacker's machine. This will allow the attacker to receive the packets that the victim machine is sending over the network.

The question remains: Which ip address does the attacker want to redirect to its own MAC address ? The answer to that is the gateway ip( the router). By redirecting the router ip to the attacker's MAC address, the attacker ensures that everytime the victim machine needs to communicate with the router, the packets go to the attacker, and this can include confidential information such as passwords Here are the steps:

1- On the attacker machine, get the ip of the router by writing the command: ip route. The ip address of your router will be the one right after "default via" ( see yellow 1 on image).

2- On the attacker machine, get the ip address of the victime machine by writing command: nmap -sn [what you find in red 2 on image]. In my example the command would be : nmap -sn 10.0.0.0/24. The ip address of the victim machine would be the one that would correspond to VirtualBox in our case. In a real world scenario, attackers might choose a random victim, or they would have more information on the attacker's device.

![image](https://github.com/user-attachments/assets/7cc271e5-63c7-4f18-95ef-c375c7073fad)

3-We can now execute the two arp spoofing commands. The first arp spoofing command will tell the router that the victim's ip address is located at the attacker's MAC address. The second command will tell the victim machine that the router's ip address is located at the attacker's MAC address. In this way, all packets from and to the router will pass by the attacker's machine. Every command should run in its separate terminal , and should not be stopped until the attack is complete ( see image 1 for a picture example of one of the commands)
command 1: sudo arpspoof -i eth0 -t [router ip] [victim ip]
command 2: sudo arpspoof -i eth0 -t [victim ip] [router ip]

image 1:
![image](https://github.com/user-attachments/assets/931cb220-f413-497a-bdd3-974fedffa201)

4- At this point of the attack, packets are flowing from the router to the attacker, and from the victim to the attacker. If those packets are not forwarded, the victim will not be able to reaching the internet, and that will raise suspicion. To prevent that, we can forward the packets with the following command: echo 1 > /proc/sys/net/ipv4/ip_forward. I use this command in a persistent elevated terminal session because otherwise this command will be denied since it requires admin access. Therefore before running the command, I ran the command sudo -i and entered the password.

Once step 4 of the attack has been completed, the victim machine will be able to browse the internet with no suspicion

5- At this point of the attack, The threat actor is officially spying on the victim. We can test if the attacker can successfully intercept the communication between the victim and the internet by simulating a signin on a http website: http://testphp.vulnweb.com.  When we visit that website using the victim machine , and log in using dummy values, we should be able to view the login form entries by using a packet sniffer like wireshark on the attacker's machine.<br/>
![image](https://github.com/user-attachments/assets/67b6cb22-ba3f-44c2-b2a5-cd16e4f57d10)

Note: On this website, you do not need to create a profile to log in as this is a testing website. Once you have logged in , you can run your packet sniffer and find the login credentials by searching through the http traffic <br/>
![image](https://github.com/user-attachments/assets/1ed531ee-086c-425c-9ab0-aa97e7e39291)

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

### Arp Spoofing Detection tool

## Conclusion
This project provided me with hands-on experience in both performing and defending against ARP spoofing attacks, enhancing my understanding of network security and penetration testing. It also highlighted the importance of encryption and continuous monitoring in securing sensitive data on public networks.


