# Project 5: TryHackMe CTF - Anthem (Windows Machine Exploitation)

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
Screenshot: OpenVPN connection command.
Screenshot: OpenVPN successful connection message.

#### Target IP Identification:
The challenge provided the IP address of the target Windows machine: 10.10.146.91.

---

## TASK 1 - Website Analysis

#### Question 1: Let's run nmap and check what ports are open.
I initiated an Nmap scan (`nmap -sV -sC 10.10.146.91`) to identify open ports, their associated services, and common script vulnerabilities on the target machine.

Screenshot: Nmap scan in progress.
Screenshot: Nmap scan results showing open ports.
Answer: (No answer needed, as per CTF instructions)

#### Question 2: What port is for web server?
Based on the Nmap scan results, port 80 was identified as running an HTTP service, indicating a web server.

Screenshot: Nmap results highlighting port 80.
Answer: 80

#### Question 3: What port is for remote desktop service?
The Nmap scan clearly showed port 3389 running the `ms-wbt-server` service, which corresponds to Remote Desktop Protocol (RDP).

Screenshot: Nmap results highlighting port 3389.
Answer: 3389

#### Question 4: What is a possible password in one of the pages web crawlers check for? (Hint: fill in the gap `****.txt`)
I navigated to `http://10.10.146.91/robots.txt`. Web crawlers use this file to understand which parts of a website should not be crawled. Often, developers inadvertently leave sensitive information here. I found a string that appeared to be a password.

Screenshot: Browser displaying robots.txt content.
Answer: UmbracoIsTheBest!

#### Question 5: What CMS is the website using?
In `robots.txt`, I observed the `Disallow: /umbraco/` directive. A quick Google search for "umbraco" confirmed that it is a popular Content Management System (CMS).

Screenshot: robots.txt showing umbraco directive.
Screenshot: Google search results for umbraco.
Answer: Umbraco

#### Question 6: What is the domain of the website?
I accessed the main website at `http://10.10.146.91`. By inspecting the footer or general content of the main page, the domain name `ANTHEM.COM` was clearly visible.

Screenshot: Website footer showing ANTHEM.COM.
Answer: ANTHEM.COM

I ran Gobuster (`gobuster dir -u http://10.10.146.91 -w /usr/share/wordlists/dirbuster/directory-list-2.3-medium.txt`) to discover more hidden directories and files. The results included a redirect to `/umbraco`, confirming the CMS presence. I then navigated to the `/umbraco` path, which presented an administrator login page.

Screenshot: Gobuster scan results showing /umbraco redirect.
Screenshot: Browser showing /umbraco login page.

#### Question 7: What's the name of the administrator? (Hint: Consult the Oracle. (your favorite search engine))
I clicked on the "A cheers to our IT department" article on the website. Within this article, I found a poem describing an individual. I used Google to search for the poem's lines, which revealed the character described as "Solomon Grundy." This was a clever way to hide the administrator's name.

Screenshot: Website article with the poem.
Screenshot: Google search results for the poem identifying Solomon Grundy.
Answer: Solomon Grundy

#### Question 8: Can we find the email address of the administrator? (Hint: There is another email address on the website that should help us figuring out the email pattern used by the administrator.)
I explored other articles on the website and found the "We are hiring" article. Within this article, an email address `JD@anthem.com` was listed. This provided the email naming convention (`[initials]@domain.com`). Applying this pattern to Solomon Grundy, his email would be `SG@anthem.com`.

Screenshot: "We are hiring" article showing JD@anthem.com.
Answer: SG@anthem.com

---

## TASK 2 - Spot the Flags

#### Question 1: What is flag 1? (Hint: Have we inspected the pages yet?)
I viewed the page source of the "We are hiring" article. The first flag was embedded within an HTML comment.

Screenshot: View page source of "We are hiring" article showing Flag 1.
Answer: THM{LOL_WHO_US3S_M3T4}

#### Question 2: What is flag 2? (Hint: Search for it)
I viewed the page source of the main website (`index.html`). The second flag was also found within an HTML comment or metadata.

Screenshot: View page source of main website showing Flag 2.
Answer: THM{G!T_GOOD}

#### Question 3: What is flag 3? (Hint: Profile)
I revisited the "We are hiring" article and looked at the profile section for Jane Doe. The third flag was located within her profile details.

Screenshot: Jane Doe's profile in the article showing Flag 3.
Answer: THM{LOL_WHO_D15}

#### Question 4: What is flag 4? (Hint: Have we inspected all the pages yet?)
I viewed the page source of the "A cheers to our IT department" article. The fourth flag was present within the source code of this page.

Screenshot: View page source of "A cheers to our IT department" article showing Flag 4.
Answer: THM{AN0TH3R_M3TA}

After finding these flags, I attempted to log into the Umbraco admin panel using the administrator's email `SG@anthem.com` and the password discovered in `robots.txt`: `UmbracoIsTheBest!`. The login was successful.

Screenshot: Umbraco login page with credentials.
Screenshot: Successfully logged into Umbraco dashboard.

Interestingly, I discovered Flag 1 and Flag 4 were also present within the Umbraco dashboard or related content, confirming their locations and providing additional pathways to discovery.

Screenshot: Flag 1 found within Umbraco dashboard.
Screenshot: Flag 4 found within Umbraco dashboard.

---

## TASK 3 - Final Stage

#### Question 1: Let's figure out the username and password to log in to the box. (The box is not on a domain)
Based on the previous findings, the most logical username for the machine login was the administrator's initials, `SG`, and the password identified from `robots.txt`, `UmbracoIsTheBest!`.

Answer: (No answer needed, as per CTF instructions)

#### Question 2: Get initial access to the machine, what is the contents of user.txt?
Since RDP (port 3389) was open, I used Remmina, an RDP client on Kali Linux, to connect to the target machine. I used the credentials `SG` and `UmbracoIsTheBest!`. I successfully logged into the Windows desktop. On the desktop, I found a file named `user.txt`. Opening this file revealed the content of the first user flag.

Screenshot: Remmina RDP connection setup.
Screenshot: Windows desktop after RDP login.
Screenshot: user.txt file on desktop.
Screenshot: Contents of user.txt.
Answer: THM{N00T_NO0T}

#### Question 3: Can we spot the admin password? (Hint: It is hidden)
To find the admin password, I explored the file system. In File Explorer, I enabled "Hidden Items" under the "View" tab to reveal any hidden folders or files. This exposed a hidden folder named `backup`.

Screenshot: File Explorer with "Hidden Items" enabled.
Screenshot: Hidden backup folder.

Attempting to access a file within this backup folder initially resulted in an "Access Denied" error. To resolve this, I right-clicked the file, went to "Properties," then "Security," and clicked "Edit." I added the `SG` user and granted "Read and execute" permissions. After applying the changes, I could open `restore.txt` and found a new password.

Screenshot: Access denied error for the backup file.
Screenshot: File properties Security tab.
Screenshot: Adding SG user in permissions.
Screenshot: Granting "Read and execute" permissions.
Screenshot: Contents of restore.txt revealing the password.
Answer: ChangeMeBaby1MoreTime

#### Question 4: Escalate your privileges to root. what is the contents of root.txt?
With the newly discovered password (`ChangeMeBaby1MoreTime`), I logged into the Windows machine as the Administrator user via RDP. On the Administrator's desktop, I found `root.txt`. Opening this file provided the final flag.

Screenshot: RDP login as Administrator with new password.
Screenshot: Administrator desktop with root.txt.
Screenshot: Contents of root.txt.

Alternatively, the same process could be done via the command line. I opened an elevated Command Prompt (`cmd` as Administrator) and navigated to the `C:\Users\Administrator\Desktop` directory. Using the `type root.txt` command, I displayed the contents of the file, confirming the root flag.

Screenshot: Elevated Command Prompt.
Screenshot: Command Prompt showing cd to desktop and type root.txt.
Answer: THM{Y0U_4R3_1337}

I successfully completed all tasks and found all flags in the Anthem CTF challenge, demonstrating my ability to perform reconnaissance, gain initial access, and escalate privileges on a Windows system.






