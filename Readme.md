# TryHackMe CTF - Anthem (Windows Machine Exploitation)

**Objective:** To complete the "Anthem" CTF challenge on TryHackMe, which involves analyzing a vulnerable Windows web server, enumerating hidden information, gaining initial access, and escalating privileges to retrieve flags.

**Skills Demonstrated:**

* CTF Methodology
* VPN Connectivity
* Network Scanning and Service Identification
* Web Content Enumeration (`robots.txt`, Gobuster)
* CMS Identification
* Open-Source Intelligence (OSINT)
* Remote Desktop Protocol (RDP) Usage
* Windows File System Navigation
* Hidden File Discovery
* Windows Privilege Escalation (Access Control List manipulation, identifying sensitive files)

**Tools Used:**

* **OpenVPN:** For connecting to the TryHackMe network.
* **Kali Linux:** The attacking operating system.
* **Nmap:** For network scanning and service enumeration.
* **Firefox:** For web Browse and manual web application analysis.
* **Gobuster:** For directory and file enumeration on web servers.
* **Remmina:** An RDP client for connecting to the Windows target.
* **Windows Command Prompt:** For interacting with the target system via command line.
* **Flameshot:** For capturing high-quality screenshots throughout the project.
* **CherryTree:** For organizing notes, steps, and observations during the assessment.

---

## Project Walk-through:

### Initial Setup and Connectivity

#### VPN Connection:
I established a secure VPN connection to the TryHackMe network using OpenVPN. This allowed my Kali Linux machine to communicate directly with the target CTF machine.

![](screenshots/1.png)

![](screenshots/2.png)


#### Target IP Identification:
The challenge provided the IP address of the target Windows machine: 10.10.146.91.

![](screenshots/3.png)

---

## TASK 1 - Website Analysis


#### Question 1: Let's run nmap and check what ports are open.
I initiated an Nmap scan to identify open ports, their associated services on the target machine.

![](screenshots/4.png)

Answer: (No answer needed, as per CTF instructions)


#### Question 2: What port is for web server?
Based on the Nmap scan results, port 80 was identified as running an HTTP service, indicating a web server.

Answer: 80


#### Question 3: What port is for remote desktop service?
The Nmap scan clearly showed port 3389 running the `ms-wbt-server` service, which corresponds to Remote Desktop Protocol (RDP).

Answer: 3389


#### Question 4: What is a possible password in one of the pages web crawlers check for? (Hint: fill in the gap `****.txt`)
I navigated to `http://10.10.146.91/robots.txt`. Web crawlers use this file to understand which parts of a website should not be crawled. Often, developers inadvertently leave sensitive information here. I found a string that appeared to be a password.

![](screenshots/5.png)

Answer: UmbracoIsTheBest!


#### Question 5: What CMS is the website using?
In `robots.txt`, I observed the `Disallow: /umbraco/` directive. A quick Google search for "umbraco" confirmed that it is a popular Content Management System (CMS).

![](screenshots/6.png)

![](screenshots/7.png)

Answer: Umbraco


#### Question 6: What is the domain of the website?
I accessed the main website at `http://10.10.146.91`. By inspecting the footer or general content of the main page, the domain name `ANTHEM.COM` was clearly visible.

![](screenshots/8.png)

Answer: ANTHEM.COM


I ran Gobuster to discover more hidden directories. The results included a redirect to `/umbraco`, confirming the CMS presence. I then navigated to the `/umbraco` path, which presented an administrator login page.

![](screenshots/9.png)

![](screenshots/10.png)


#### Question 7: What's the name of the administrator? (Hint: Consult the Oracle. (your favorite search engine))
I clicked on the "A cheers to our IT department" article on the website. Within this article, I found a poem describing an individual. I used Google to search for the poem's lines, which revealed the character described as "Solomon Grundy." This was a clever way to hide the administrator's name.

![](screenshots/11.png)

![](screenshots/12.png)

Answer: Solomon Grundy


#### Question 8: Can we find the email address of the administrator? (Hint: There is another email address on the website that should help us figuring out the email pattern used by the administrator.)
I explored other articles on the website and found the "We are hiring" article. Within this article, an email address `JD@anthem.com` was listed. This provided the email naming convention (`[initials]@domain.com`). Applying this pattern to Solomon Grundy, his email would be `SG@anthem.com`.

![](screenshots/13.png)

Answer: SG@anthem.com

---

## TASK 2 - Spot the Flags


#### Question 1: What is flag 1? (Hint: Have we inspected the pages yet?)
I viewed the page source of the "We are hiring" article. The first flag was embedded within an HTML metadata.

![](screenshots/14.png)

Answer: THM{LOL_WHO_US3S_M3T4}


#### Question 2: What is flag 2? (Hint: Search for it)
I viewed the page source of the main website. The second flag was found there.

![](screenshots/15.png)

Answer: THM{G!T_GOOD}


#### Question 3: What is flag 3? (Hint: Profile)
I revisited the "We are hiring" article and looked at the profile section for Jane Doe. The third flag was located within her profile details.

![](screenshots/16.png)

Answer: THM{LOL_WHO_D15}


#### Question 4: What is flag 4? (Hint: Have we inspected all the pages yet?)
I viewed the page source of the "A cheers to our IT department" article. The fourth flag was present within the source code of this page.

![](screenshots/17.png)

Answer: THM{AN0TH3R_M3TA}


After finding these flags, I attempted to log into the Umbraco admin panel using the administrator's email `SG@anthem.com` and the password discovered in `robots.txt`: `UmbracoIsTheBest!`. The login was successful.

![](screenshots/18.png)


I discovered Flag 1 and Flag 4 were also present within the Umbraco confirming their locations and providing additional pathways to discovery.

![](screenshots/19.png)

![](screenshots/20.png)

---

## TASK 3 - Final Stage


#### Question 1: Let's figure out the username and password to log in to the box. (The box is not on a domain)
Based on the previous findings, the most logical username for the machine login was the administrator's initials, `SG`, and the password identified from `robots.txt`, `UmbracoIsTheBest!`.

Answer: (No answer needed, as per CTF instructions)


#### Question 2: Get initial access to the machine, what is the contents of user.txt?
Since RDP (port 3389) was open, I used Remmina, an RDP client on Kali Linux, to connect to the target machine. I used the credentials `SG` and `UmbracoIsTheBest!`. I successfully logged into the Windows desktop. On the desktop, I found a file named `user.txt`. Opening this file revealed the content of the first user flag.

![](screenshots/21.png)

![](screenshots/22.png)

![](screenshots/23.png)

Answer: THM{N00T_NO0T}


#### Question 3: Can we spot the admin password? (Hint: It is hidden)
To find the admin password, I explored the file system. In File Explorer, I enabled "Hidden Items" under the "View" tab to reveal any hidden folders or files. This exposed a hidden folder named `backup`.

![](screenshots/24.png)

![](screenshots/25.png)


Attempting to access a file within this backup folder initially resulted in an "Access Denied" error. To resolve this, I right-clicked the file, went to "Properties," then "Security," and clicked "Edit." I added the `SG` user and granted "Read and execute" permissions. After applying the changes, I could open `restore.txt` and found a new password.

![](screenshots/26.png)

![](screenshots/27.png)

![](screenshots/28.png)

![](screenshots/29.png)

Answer: ChangeMeBaby1MoreTime


#### Question 4: Escalate your privileges to root. what is the contents of root.txt?
With the newly discovered password (`ChangeMeBaby1MoreTime`), I logged into the Windows machine as the Administrator user. On the Administrator's desktop, I found `root.txt`. Opening this file provided the final flag.

![](screenshots/30.png)

![](screenshots/31.png)

![](screenshots/32.png)


Alternatively, the same process could be done via the command line. I opened an elevated Command Prompt (`cmd` as Administrator) and navigated to the `C:\Users\Administrator\Desktop` directory. Using the `type root.txt` command, I displayed the contents of the file, confirming the root flag.

![](screenshots/32.png)

![](screenshots/34.png)

![](screenshots/35.png)

Answer: THM{Y0U_4R3_1337}


I successfully completed all tasks and found all flags in the Anthem CTF challenge, demonstrating my ability to perform reconnaissance, gain initial access, and escalate privileges on a Windows system.

![](screenshots/36.png)

