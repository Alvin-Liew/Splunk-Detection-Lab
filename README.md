# Splunk-Detection-Lab

## Objective
The Splunk Detection Lab project aimed to establish a controlled environment for simulating and detecting brute force attacks to understand the telemetry that generates, while using atomic red team as well. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security and attack patterns.

### Skills Learned
- Advanced understanding of SIEM concepts and practical application.
- Proficiency in analyzing and interpreting network logs.
- Ability to generate and recognize attack signatures and patterns.
- Enhanced knowledge of network protocols and security vulnerabilities.
- Development of critical thinking and problem-solving skills in cybersecurity.

### Tools Used
- Splunk Security Information and Event Management (SIEM) system for log ingestion and analysis.
- Sysmon deployed on the Windows machine to log detailed system activities, which were then analyzed by Splunk.
- VMware Workstation Pro to create isolated environments for the attack and victim machines, allowing for safe and controlled simulation of cyber attacks and defenses.
- Ubuntu Server served as the operating system for the SIEM where Splunk was installed and used to .
- Windows 2022 Server served as the domain controller which housed Active Directory to store account credentials
- Kali Linux system for brute force attacks on the windows machine
- Windows 10 as the

## Network Diagram

![Untitled Diagram](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/10518666-9e82-469f-ba84-39b784a51846)

## Setup

Created three organizational units as different departments of an organization (IT, HR, and Accounting) and added one user to each department (Eric, Alvin, and Kaelyn). These users are going to be used as the attack target for the brute force attack on our kali linux

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/29e7c697-9508-4e6b-9ee9-ebf4c8df2d31)

Before testing out your new accounts on the windows machine you must first join the domain. If you head to your network adapter option settings and go into your Internet Protocol Version 4 and change the preferred DNS settings to your domain controller's IP. After you head to the computer's settings on the About page and find Advanced system settings => computer name => change. Now you can change the domain and login as an admin to approve the request.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/0775b7ba-c26a-4d40-9e42-0ca340190778)

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/b57d4990-55d4-4c0f-887e-acfe696233ba)

After you set that all up we can now head over to our kali machine to set up the brute force attack. First we need to connect it to our internal network by configuring the ipv4
