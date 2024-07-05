# Splunk-Detection-Lab

## Objective
The Splunk Detection Lab project aimed to establish a controlled environment for simulating and detecting brute force attacks to understand the telemetry that generates, while using atomic red team as well. The primary focus was to ingest and analyze logs within a Security Information and Event Management (SIEM) system, generating test telemetry to mimic real-world attack scenarios. This hands-on experience was designed to deepen understanding of network security and attack patterns.

### Skills Learned
- Understanding how to configure and use Splunk for ingesting and analyzing logs, crucial for monitoring and detecting security incidents within a SIEM system.
- Utilizing VMware Workstation Pro to create isolated virtual environments for simulating attacks and defenses, providing a controlled and safe platform for cybersecurity testing.
- Implementing Sysmon on Windows machines to log detailed system activities, aiding in the thorough analysis of system behavior during simulated attacks.
- Using Kali Linux and tools like Crowbar to simulate brute force attacks, understanding attack methodologies, and observing the generated telemetry in a controlled environment.
- Employing Atomic Red Team to create realistic attack scenarios, generating test telemetry, and analyzing the results to enhance understanding of attack patterns and network security defenses.

### Tools Used
- Splunk Security Information and Event Management (SIEM) system for log ingestion and analysis.
- Sysmon deployed on the Windows machine to log detailed system activities, which were then analyzed by Splunk.
- VMware Workstation Pro to create isolated environments for the attack and victim machines, allowing for safe and controlled simulation of cyber attacks and defenses.
- Ubuntu Server served as the operating system for the SIEM where Splunk was installed and used to .
- Windows 2022 Server served as the domain controller which housed Active Directory to store account credentials
- Kali Linux served as the attack machine for brute force attacks on the target machine
- Windows 10 as the target machine

## Network Diagram

![Untitled Diagram](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/10518666-9e82-469f-ba84-39b784a51846)

## Setup

Created three organizational units as different departments of an organization (IT, HR, and Accounting) and added one user to each department (Eric, Alvin, and Kaelyn). These users are going to be used as the attack target for the brute force attack on our kali linux

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/29e7c697-9508-4e6b-9ee9-ebf4c8df2d31)

Before testing out your new accounts on the windows machine you must first join the domain. If you head to your network adapter option settings and go into your Internet Protocol Version 4 and change the preferred DNS settings to your domain controller's IP. After you head to the computer's settings on the About page and find Advanced system settings => computer name => change. Now you can change the domain and login as an admin to approve the request.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/0775b7ba-c26a-4d40-9e42-0ca340190778)

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/b57d4990-55d4-4c0f-887e-acfe696233ba)

After you set that all up we can now head over to our kali machine to set up the brute force attack. First we need to connect it to our internal network by configuring the ipv4. If you go back to our network diagram you can see that our kali ip is 192.168.10.250 with a netmask of 24 and the gateway ip would be: 192.168.10.1, be sure to set the DNS servers to google's DNS (8.8.8.8).

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/e29a8d0c-f1e4-4767-b456-0b93c1e3568f)

If we head to our terminal we would need to make sure that the configurations are working by typing "ip a". If you notice that our ip hasn't changed and to reflect these changes you would need to disconnect and reconnect to the network inorder for the changes to apply. After that is done we can head to the desktop directory by typing cd desktop and making a new directory "mkdir ad-project" and install crowbar "sudo apt-get install -y crowbar". Crowbar is a tool we are going to use to perform brute force attacks and we can target either our domain controller or our target machine.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/771f4c1d-ee6c-4976-a817-bc1e8b1ee527)

Now that crowbar is installed, we can access our kali wordlist called rockyou that comes with kali linux in "cd /user/share/wordlists/" and unzip the rockyoutxt "sudo gunzip rockyou.txt.gz". Add add it into our directory ad-project "cp rockyou.txt ~/Desktop/ad-project" and go into our "~/Desktop/ad-project".

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/554a2da5-65fb-40fb-bdad-fe5a564f01f4)

Just for the project we are just going to use 20 lines of the wordlist and input it to our password.txt "head -n 20 rockyou.txt > passwords.txt". For the telemetry analysis we should put in the password for the target machine into password.txt so that we understand what the telemetry for a password found is by inputting "nano password.txt" and adding your password at the end of the list.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/164f27f2-166c-4ba5-8388-f5da1e0d1dc4)

Now that our password list is finalized, we need to enable the remote desktop on our target machine by heading back to advanced system settings and going to the remote tab and selecting "Allow remote connections to the computer" and "Users".

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/62883ac7-70c6-43a7-82a9-6e7db79de684)

Now we can implement our brute force attack tool by heading back to our kali linux machine. To activate the tool we need to type "crowbar -b rdp -u "the account you made'' -C passwords.txt -s 192.168.10.100/32. You will notice that the terminal will print "RDP-SUCCESS : 192.168.10.100:3389 - "the account you made":mineacrft775!" implying that the tool was success with the brute force attack.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/84bac861-ed87-4668-be8e-2ac22973e45a)

If you head to your splunk enterprise and search for index="endpoint" within the last 15 minutes in the event code tab you will see that 4625 has a count of 20. Now if we search what value 4625 means we will find that it means an account failed to log on which means that there were 20 failed attempts to login. Below that you will find that the value is 4624 with a count of 1 and that means that there was 1 successful attempt to login.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/d9a05da4-6506-477b-9f3a-f6dcf6c16d42)

If we dig deeper we can find more telemetry that shows us more information about the attack machine:

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/5cc97e53-ec7f-4c77-afdc-7de0ea8fc967)

Now we want to utilize the atomic red team to generate test telemetry to mimic real-world attack scenarios and understand what it would look like. After we installed atomic red team we can look into the folder and find a bunch of files with numbers. If you head to the MITRE | ATT&CK website you can find a bunch of attacks and when you hover over it you will get numbers.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/2cb25f8d-4ee5-49ab-a0f0-b6c3a5a09d2e)

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/d805b2bd-4ffd-4b4b-ab26-6c9f2ba611e6)

Now if you use powershell to initiate one of these attacks we can find that the telemetry will show up in our splunk enterprise.

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/555d6792-bd7d-4a4b-bb62-574885e3f1bc)

![image](https://github.com/Alvin-Liew/Splunk-Detection-Lab/assets/105011531/bc95f626-d3a4-4daa-88cc-04f5c428e4ea)

## Conclusion

The Splunk Detection Lab project provided a comprehensive and practical learning experience in cybersecurity, particularly in understanding and detecting brute force attacks. By creating a controlled environment with VMware Workstation Pro, participants gained hands-on experience in setting up and managing virtual environments, essential for conducting safe cyber attack simulations. Central to the project was using Splunk as a SIEM system for detailed log ingestion and analysis, enhancing proficiency with Splunk and understanding its role in real-world security operations. Deploying Sysmon on Windows machines to log system activities and using Kali Linux tools like Crowbar for brute force attacks offered practical insights into attack methodologies and associated telemetry. The integration of Atomic Red Team for generating test telemetry to mimic real-world attack scenarios further enriched the learning experience, providing a clearer understanding of attack patterns and the importance of robust network security defenses. Overall, the project equipped participants with critical skills in log analysis, virtual environment management, attack simulation, and telemetry analysis, essential for effectively detecting and mitigating cyber threats in real-world settings.
