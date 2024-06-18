<h1>Applying OS Hardening Techniques</h1>

<h2>Description</h2>
In this project, I will assume the role of a cybersecurity analyst working for a company that hosts the cooking website yummyrecipesforme.com. Visitors to the website experience a security issue when loading the main webpage. My job is to investigate, identify, document, and recommend a solution to the security problem. When investigating the security event, I will review a tcpdump log. Then, I will write an incident report for the security event.

<h2>Scenario</h2>
I am a cybersecurity analyst for yummyrecipesforme.com, a website that sells recipes and cookbooks. A former employee has decided to lure users to a fake website with malware. 
<br />
<br />
The baker executed a brute force attack to gain access to the web host. They repeatedly entered several known default passwords for the administrative account until they correctly guessed the right one. After they obtained the login credentials, they were able to access the admin panel and change the website’s source code. They embedded a javascript function in the source code that prompted visitors to download and run a file upon visiting the website. After embedding the malware, the baker changed the password to the administrative account. When customers download the file, they are redirected to a fake version of the website that contains the malware. 
<br />
<br />
Several hours after the attack, multiple customers emailed yummyrecipesforme’s helpdesk. They complained that the company’s website had prompted them to download a file to access free recipes. The customers claimed that, after running the file, the address of the website changed, and their personal computers began running more slowly. 
<br />
<br />
In response to this incident, the website owner tries to log in to the admin panel but is unable to, so they reach out to the website hosting provider. I and other cybersecurity analysts are tasked with investigating this security event.
<br />
<br />
To address the incident, I create a sandbox environment to observe the suspicious website behavior. I run the network protocol analyzer tcpdump, then type in the URL for the website yummyrecipesforme.com. As soon as the website loads, I am  prompted to download an executable file to update my browser. I accept the download and allow the file to run. I then observe that my browser redirects me to a different URL, greatrecipesforme.com, which contains the malware.  
<br />
<br />
A senior analyst confirms that the website was compromised. The analyst checks the source code for the website. They notice that javascript code had been added to prompt website visitors to download an executable file. Analysis of the downloaded file found a script that redirects the visitors’ browsers from yummyrecipesforme.com to greatrecipesforme.com. 
<br />
<br />
The cybersecurity team reports that the web server was impacted by a brute force attack. The disgruntled baker was able to guess the password easily because the admin password was still set to the default password. Additionally, there were no controls in place to prevent a brute-force attack. 
<br />
<br />

<h2>tcpdump Traffic Log</h2>
<p align="center">
<img src="https://i.imgur.com/lPNZbXz.png" height="70%" width="70%" alt="tcpdump Log"/> </p>

<h2>tcpdump Traffic Log Analysis</h2>

<p align="center">
<img src="https://i.imgur.com/0zu1vNB.png" height="70%" width="70%" alt="tcpdump Log 1"/> </p>
This section of the DNS and HTTP traffic log file shows that the source computer (your.machine.52444) uses port 52444 to send a DNS resolution request to the DNS server (dns.google.domain) for the destination URL (yummyrecipesforme.com). Next, a reply comes back from the DNS server to the source computer with the IP address of the destination URL (203.0.113.22). Here we can see that the browser is directing users to the correct website.
<br />
<br />

<p align="center">
<img src="https://i.imgur.com/KKrMWKs.png" height="70%" width="70%" alt="tcpdump Log 2"/> </p>
This section shows that the source computer (your.machine.36086) is sending a connection request (Flags[S]) using port 36086 directly to the destination (yummyrecipesforme.com.http). The .http suffix is the port number, which is commonly associated with port 80. The reply shows the destination acknowledging it received the connection request (Flags[S.]).
<br />
<br />

<p align="center">
<img src="https://i.imgur.com/Udq5QBH.png" height="70%" width="70%" alt="tcpdump Log 3"/> </p>
This section shows that the browser is requesting data from yummyrecipesforme.com with the HTTP:GET method using HTTP protocol version 1.1. This could be the download request for the malicious file. 
<br />
<br />

<p align="center">
<img src="https://i.imgur.com/MCQl9Cy.png" height="70%" width="70%" alt="tcpdump Log 4"/> </p>
This section shows a sudden change happening in the logs. After the user downloads and runs the file, logs show that the traffic appears to be routed from the source computer to the DNS server again using port .52444 (your.machine.52444 > dns.google.domain) to make another DNS resolution request. However, this time the DNS server routes the traffic to a new IP address (192.0.2.172) and its associated URL (greatrecipesforme.com.http). The traffic changes to a route between the source computer and the spoofed website (outgoing traffic: IP your.machine.56378 > greatrecipesforme.com.http) (incoming traffic: greatrecipesforme.com.http > IP your.machine.56378). The port number (.56378) on the source computer has changed again when redirected to a new website (greatrecipesforme.com) over HTTP.


<h2>Security Incident Report</h2>

<h3>Identifying the network protocol involved in the incident</h3>
The network protocol involved in the incident is the Hypertext transfer protocol (HTTP). Since the issue was with accessing the web server for yummyrecipesforme.com, we know that requests to web servers for web pages involve HTTP traffic. Also, when I ran tcpdump and accessed the yummyrecipesforme.com website, the corresponding tcpdump log file showed the usage of the HTTP protocol when contacting the spoofed website. The malicious file is observed being transported to the users’ computers using the HTTP protocol at the application layer.

<h3>Documenting the incident</h3>
Several customers contacted the website's helpdesk stating that when they visited the website, they were prompted to download and run a file that allegedly contained access to new recipes. The customers stated that after running the file, their personal computers began to run slower. Next, the website owner attempted to log into the web server's admin panel but noticed that he was locked out of his account.
<br />
<br />
I then used a sandbox environment to open the website so that I would not risk impacting the company's network. Then, I ran tcpdump to capture the network traffic packets produced by interacting with the website. I was then prompted to download a file claiming it would provide access to free recipes, accepted the download and ran it. The browser then redirected me to a fake website (greatrecipesforme.com).
<br />
<br />
Next, I inspected the tcpdump log and observed that the browser initially requested the IP address for the yummyrecipesforme.com website. Once the connection with the website was established over the HTTP protocol, I downloaded and executed the file. The logs showed a sudden change in network traffic as the browser requested a new IP address for the greatrecipesforme.com URL. The network traffic was then rerouted to the new IP address for the greatrecipesforme.com website.
<br />
<br />
The senior cybersecurity professional analyzed the source code for the websites and the downloaded file. The analyst discovered that an attacker had manipulated the website to add code that prompted the users to download a malicious file disguised as a browser update. Since the website owner stated that they had been locked out of their administrator account, the team believes the attacker used a brute force attack to access the account and change the admin password. The execution of the malicious file compromised the end users’ computers.

<h3>Recommending remediations for brute force attacks</h3>
The first recommendation for preventing future brute-force attacks is to implement a strong password policy that includes a requirement for longer and more complex passwords. This is because passwords that are 15 characters or longer offer greater protection against brute-force attacks. The password policy will also prohibit users from using previous and default passwords. This is because the vulnerability that led to this attack was the threat actor's ability to use default passwords to log in and reset the admin's password. The second recommendation is to implement two-factor authentication (2FA). This is because 2FA requires authentication via a password and an additional authentication method by confirming a one-time passcode (OTP) sent to another device. Once the user confirms their identity through their login credentials and the OTP, they will gain access to the system. This will help prevent a malicious actor from gaining access to the system through a brute-force attack since they will require an additional method of authentication. 
