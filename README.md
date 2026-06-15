# ClearMed Clinic: Endpoint Security Assessment and Hardening

> **Category:** Cybersecurity
> 
> **Status:** Live
> 
> **Course:** Cisco Endpoint Security · Junior Cybersecurity Analyst Career Path
> 
> **Author:** Ozioma Inya         <!-- REPLACE with your name -->
> **Date:** August, 2025              <!-- REPLACE e.g. "April, 2025" -->

---

## Overview

A comprehensive endpoint security assessment and hardening exercise for
ClearMed Clinic, a healthcare clinic that experienced a near-miss ransomware
incident. Covers threat and vulnerability classification, network security controls, TCP/IP and
application layer attacks, wireless security, ACLs and port security, Windows
and Linux OS hardening, endpoint protection tools, and cybersecurity principles.

---

## Client Brief

| Detail | Info |
|---|---|
| Client | ClearMed Clinic |
| Industry | Healthcare |
| Site | Single-site clinic: 30 staff including medical, administrative, and IT |
| Task | Full endpoint security assessment and hardening |

**Incident Trigger:** A phishing email targeting a receptionist workstation almost
delivered ransomware to the clinic's network. The file was caught by the ISP's spam
filter rather than any internal control. Management commissioned this assessment to
understand the clinic's exposure and implement proper controls before a real incident.

---

## Objectives

- [x] Classify threats, vulnerabilities, and attacks relevant to a healthcare clinic
- [x] Configure network security controls on clinic router and switch
- [x] Identify and document TCP/IP foundation attacks affecting clinic infrastructure
- [x] Identify and document application and service attacks on clinic systems
- [x] Configure and verify wireless security for staff and guest Wi-Fi
- [x] Implement ACLs and port security on clinic network infrastructure
- [x] Assess and document Windows workstation hardening measures
- [x] Assess and document Linux server hardening measures
- [x] Define an endpoint protection strategy covering antivirus, EDR, and patching
- [x] Apply defence-in-depth principles across the clinic environment

---

## Tools & Technologies

| Tool | Purpose |
|---|---|
| Cisco Packet Tracer | Network topology, ACLs, port security, wireless |
| Cisco IOS CLI | Router and switch security configuration |
| Windows OS (documented) | Workstation hardening assessment |
| Linux CLI (documented) | Ubuntu server hardening assessment |
| ICMP Ping | ACL and connectivity verification |

---

## Skills Demonstrated

`Threat Classification` `Vulnerability Assessment` `Attack Taxonomy`
`Network Security Controls` `TCP/IP Attack Mitigation` `Application Layer Security`
`Wireless Security` `WPA2 Enterprise` `ACLs` `Port Security`
`Windows Hardening` `Windows Event Viewer` `Local Security Policy`
`Linux Hardening` `UFW Firewall` `File Permissions` `Service Management`
`Antivirus` `EDR` `HIPS` `Patch Management` `Defence in Depth`
`CIA Triad` `Security Policies` `Cisco IOS CLI` `Packet Tracer`

---

## Network Topology

```
  [Internet]
       |
  [Clinic-R1: S0/0/0 WAN / G0/0: 192.168.1.1]
       | (ACLs applied)
  [Clinic-SW1: 192.168.1.2]
    /      |        |        \
[Win-WS] [Linux]  [Admin]  [WAP]
.1.10/20  .100     .50       |
(port sec)          Staff VLAN 10: 192.168.10.x (WPA2, hidden)
                    Guest VLAN 20: 192.168.20.x (isolated)
```

### Device and Security Table

| Device | IP Address | Role | Security Control |
|---|---|---|---|
| Clinic-R1 | 192.168.1.1 | Gateway / firewall | ACLs applied |
| Clinic-SW1 | 192.168.1.2 | Access layer switch | Port security |
| Win-WS | 192.168.1.10 | Windows workstation | Hardened |
| Linux Server | 192.168.1.100 | Ubuntu patient DB server | Hardened |
| Admin-PC | 192.168.1.50 | Admin workstation | SSH management |
| Staff Wi-Fi | 192.168.10.x | Staff wireless VLAN 10 | WPA2, hidden SSID |
| Guest Wi-Fi | 192.168.20.x | Patient waiting room VLAN 20 | Isolated |

---

## Assessment Parts

### Part 01: Threat and Vulnerability Assessment

**Why healthcare is targeted:** 
Patient records, ransomware operational pressure,
regulatory consequences of breach (GDPR, NHS requirements).

**Threat categories for ClearMed:**
- Ransomware via phishing -- near-miss confirmed active targeting
- Phishing targeting non-technical staff (receptionist)
- Insider threat via USB, personal email, weak passwords

**Vulnerability categories identified:**
- Software: outdated Windows, unpatched applications
- Configuration: no ACLs, no port security, default credentials
- Human: no security awareness training, no phishing simulation

---

### Part 02: Clinic Network Security Configuration

```
-- Device hardening applied to Clinic-R1
Clinic-R1(config)# enable secret ClearMed!R1
Clinic-R1(config)# service password-encryption
Clinic-R1(config)# no cdp run

-- SSH v2 configured
Clinic-R1(config)# ip domain-name clearmed.local
Clinic-R1(config)# crypto key generate rsa
How many bits in the modulus [512]: 1024
% Generating 1024 bit RSA keys, keys will be non-exportable...[OK]
Clinic-R1(config)# ip ssh version 2
Clinic-R1(config)# line vty 0 15
Clinic-R1(config-line)# transport input ssh
Clinic-R1(config-line)# exec-timeout 5 0
```

---

### Part 03: TCP/IP Foundation Attacks

| Attack | Method | Mitigation Applied |
|---|---|---|
| IP Spoofing | Forged source IP headers | Ingress filtering ACL (RFC 2827) |
| ARP Poisoning | Gratuitous ARP redirecting traffic | HTTPS encryption, static ARP for critical hosts |
| TCP SYN Flood | Half-open connection exhaustion | TCP intercept, rate limiting on WAN |
| DNS Spoofing | False DNS record injection | DNSSEC, trusted resolvers, staff training |

---

### Part 04: Application and Service Attacks

| Attack | Clinic Impact | Mitigation |
|---|---|---|
| Phishing / Spear Phishing | Near-miss delivery vector | Email filtering, DMARC/DKIM/SPF, training |
| Ransomware | Patient records encrypted, operations halted | App whitelisting, least privilege, offline backups |
| SQL Injection | Patient database compromise | Parameterised queries, WAF, input validation |
| HTTP (unencrypted) | Credentials captured in transit | HTTPS everywhere, HSTS |

---

### Part 05: Wireless Security Configuration

```
STAFF NETWORK:
  SSID:           ClearMed-Staff (hidden)
  Security:       WPA2-PSK AES
  VLAN:           10
  Client access:  Clinic LAN permitted

GUEST NETWORK:
  SSID:           ClearMed-Guest (visible)
  Security:       WPA2-PSK AES
  VLAN:           20 (isolated)
  Client access:  Internet only -- clinic LAN BLOCKED
  Client isolation: Enabled
```

---

### Part 06: ACLs and Port Security

```
-- Block guest from clinic LAN
ip access-list extended GUEST_RESTRICTION
  deny ip 192.168.20.0 0.0.0.255 192.168.1.0 0.0.0.255
  permit ip 192.168.20.0 0.0.0.255 any

-- Restrict clinic outbound to HTTPS and DNS only
ip access-list extended CLINIC_OUTBOUND
  permit tcp 192.168.1.0 0.0.0.255 any eq 443
  permit udp 192.168.1.0 0.0.0.255 any eq 53
  permit tcp 192.168.1.0 0.0.0.255 any eq 80
  deny ip any any log

-- Port security on workstation ports
switchport port-security maximum 1
switchport port-security mac-address sticky
switchport port-security violation restrict
```

---

### Part 07: Windows Hardening

| Control | Setting | Purpose |
|---|---|---|
| Password minimum length | 12 characters | Brute force resistance |
| Account lockout | 5 attempts / 30 min | Credential attack prevention |
| Windows Defender | Active, real-time on | Malware detection |
| Windows Firewall | All profiles active | Inbound connection control |
| Event ID 4688 | Enabled via GPO | Process creation logging |
| USB AutoRun | Disabled via GPO | Removable media malware prevention |
| Windows Update | Automatic | Patch management |

**Finding:** Event ID 4625 showed 47 failed logon attempts on the near-miss
workstation the night before the phishing email, an evidence of prior access attempt.

---

### Part 08: Linux Server Hardening

```
-- SSH hardening (/etc/ssh/sshd_config)
PermitRootLogin no
PasswordAuthentication no

-- UFW firewall
sudo ufw default deny incoming
sudo ufw allow from 192.168.1.0/24 to any port 22
sudo ufw allow from 192.168.1.x to any port 3306
sudo ufw enable

-- File permission corrections
chmod 750 /var/patient-records/
chmod 600 /etc/db-credentials.conf

-- Disabled unnecessary services
sudo systemctl disable cups
sudo systemctl disable avahi-daemon
sudo systemctl disable bluetooth
```

**Finding:**
Server had not been patched in 4 months. Unattended-upgrades
enabled for security updates going forward.

---

### Part 09: Endpoint Protection Strategy

| Layer | Tool | Purpose |
|---|---|---|
| EPP | Windows Defender / ClamAV | Known malware detection |
| EDR | Managed EDR via MSSP | Behavioural detection and forensics |
| HIPS | Windows Defender exploit protection | Block suspicious process behaviours |
| Patch management | WSUS / unattended-upgrades | Close known vulnerabilities |

---

### Part 10: Defence in Depth

**Five layers applied:**
- Perimeter: Clinic-R1 ACLs to block external threats at network edge
- Network: Clinic-SW1 port security and VLANs to limit lateral movement
- Endpoint: Windows Defender, HIPS, app control to prevent malware execution
- Data: Encryption at rest and in transit to protect records if endpoint compromised
- Human: Staff training, phishing simulation to address social engineering vector

**Ongoing security practices:**
- Quarterly: phishing simulation, firewall log review, vulnerability scan
- Annually: penetration test, policy review, training refresh
- Continuous: patch cycle, EDR monitoring, backup testing

---

## Results

| # | Result | Status |
|---|---|---|
| 1 | Clinic-R1 and Clinic-SW1 fully hardened with SSH v2 | OK |
| 2 | ACL GUEST_RESTRICTION blocking guest from clinic LAN | OK |
| 3 | ACL CLINIC_OUTBOUND permitting HTTPS/DNS only | OK |
| 4 | Port security active on all workstation ports | OK |
| 5 | Guest clients confirmed unable to reach clinic LAN | OK |
| 6 | Windows Event Viewer identified 47 prior failed logons | OK |
| 7 | Linux server permissions corrected, services disabled | OK |
| 8 | CLINIC_OUTBOUND blocked DNS because UDP 53 missing from ACL | WARNING |
| 9 | Fixed by adding permit udp any eq 53 to ACL | FIXED |
| 10 | Guest isolation failed because ACL was applied on wrong interface direction | WARNING |
| 11 | Fixed by moving ACL to correct interface | FIXED |

---

## Lessons Learned

**1. A near-miss is a gift. Most organisations never get one**

ClearMed had no controls that would have stopped the phishing email internally.
The near-miss created the organisational will to invest in security.

**2. Enable logging before you need it**

Event ID 4625 revealed a prior access attempt. Without logging enabled,
this evidence would not have existed for investigation.

**3. Linux servers need hardening too**

World-readable permissions, unnecessary services, and a 4-month patch gap
were all found on the Ubuntu server. Default installation is a starting point.

**4. Defence in depth accepts that each layer will sometimes fail**

No single control stops every attack. Multiple independent layers mean
an attacker must defeat all of them simultaneously.

**5. Guest Wi-Fi isolation is non-negotiable in healthcare**

Patients connecting to the same network as clinical workstations is
eliminated completely by VLAN isolation -- a zero-cost control.

---

## Repository Structure

```
endpoint-security-assessment/
|-- index.html                         <- Full project write-up (live via GitHub Pages)
|-- README.md                          <- This file
|-- clearmed-clinic.pkt                <- Cisco Packet Tracer project file
|-- topology.png                       <- Full clinic network topology
|-- acl-verification.png               <- show ip access-lists hit counts
+-- port-security.png                  <- show port-security on Clinic-SW1
```

---

## Links

- [Full Project Write-up](https://oziomainya.github.io/endpoint-security-assessment)
- [Portfolio](https://oziomainya.github.io)

<!-- REPLACE [YOUR_GITHUB_USERNAME] and [YOUR_PORTFOLIO_URL] with your real details -->

---

*Ozioma Inya · [LinkedIn ↗](https://linkedin.com/in/ozioma-inya-a46327304) · [Github ↗](https://github.com/oziomainya)*
<!-- REPLACE the placeholders above with your real details -->
