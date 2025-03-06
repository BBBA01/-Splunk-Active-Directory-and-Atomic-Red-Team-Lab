# -Splunk-Active-Directory-and-Atomic-Red-Team-Lab

This project was done using VirtualBox and 4 virtual machines in a NAT network. The main components of the project are:

Windows Server 2022 (AD-DC10)
Install Active Directory services on a Windows 2022 server and create users.
Install and configure Splunk Universal Forwarder.
Install and configure Sysmon to forward events to Splunk.
Splunk Server
Install and configure Splunk to receive logs from the Windows server and Windows workstation.
Use Splunk to identify attacks in forwarded logs.
Windows 10 Workstation (Target-PC)
Add the Windows 10 workstation to the Active Directory domain.
Install and configure Splunk Universal Forwarder.
Install and configure Sysmon to forward events to Splunk.
Install Atomic Red Team to simulate MITRE ATT&CK techniques.
Kali Linux
Conduct brute force attack on Windows 10 workstation using Crowbar.
Network Topology
![Screenshot 2025-03-06 173757](https://github.com/user-attachments/assets/d748156a-7574-4161-bf69-39d43eb6fca4)


Project Highlights
Splunk Server
Splunk was installed and configured on a VM running Ubuntu Server 24.04.1. A static IP of 10.0.10.10 was set by modifying the /etc/netplan/50-cloud-init.yaml file. NOTE: Indentations in the yaml file are important!

image

The static IP address is applied to the server:

image

Splunk was configured to listen on port 9997 and the Splunk interface to be accessed via a web browser at 10.0.10.10 on port 8000. An "endpoint" index was created to receive logs forwarded from the Active Directory server and the Windows workstation.

image

Active Directory Server
Active Directory services were installed and a domain created (lab.local). A "Workstations" organizational unit was created and the Windows 10 workstation added to it.

image

Organizational units were created for IT and HR and two users added to each unit.

image

Kali Linux Brute Force Attack
RDP was enabled on the Windows 10 workstation and from Kali Linux a brute force attack was conducted against the Windows 10 workstation using Crowbar. The attack targeted the user Mary Weaver (mweaver). Twenty passwords were extracted to passwords.txt from the rockyou.txt passwords list and used as the list for the brute force attack. In order to register a successful attack, Mary Weaver's password was added to passwords.txt.

image

Splunk registered 23 events related to mweaver:

image

Windows event code 4625 is for failed logon attempts. Splunk recorded 20 failed login attemtps for mweaver's account, which is the number of incorrect passwords contained in the passwords.txt file:

image

Atomic Red Team Simulated Attacks
Atomic Red Team is a library of simple, focused tests mapped to the MITRE ATT&CK matrix. It is typically used to test whether an organization's security controls are detecting attacks.

In this lab, Atomic Red Team was installed on the Winodws 10 workstation and a couple of attacks run and detected in Splunk.

Create Local Account (T1136.001)
One technique attackers use to establish persistence on a compromised host is to create new local accounts. This technique, T1136.001 in the MITRE framework, is described here. The Atomic Red Team simulates this technique in various ways, depending on the system and OS. In this lab, Atomic Red Team used "NewLocalUser" as the account to be created:

image

We can see this event recorded in Splunk:

image

File and Directory Permissions Modification (T1222.001)
In order to evade access control lists and access protected files, attackers may attempt to modify file or directory permissions. MITRE describes this technique (T1222.001) here.

In the Atomic Red Team simulation, one of the files which it attempts to modify is T1222.001_attrib as seen here:

image

If we search for this string in Splunk, we can identify the events:

image
