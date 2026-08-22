# Fool's Mate - TryHackMe Writeup
This writeup covers my process of finding the vulnerability in a chess training platform.<br><br>
Tools Used:<br>
•	Nmap<br>
•	Burp Suite<br><br>

Starting with an nmap scan:<br><br>
<code>nmap -sC -sV 10.49.150.13</code><br><br>
<img alt="image" src="images/FoolsMate1.png"/><br><br>
The website is a chess platform which is supposed to train the users to play chess.<br><br>
<img alt="image" src="images/FoolsMate2.png"/><br><br>
This is a mate in one, but if I move my rook to a8, it won’t accept its defeat!<br><br>
<img alt="image" src="images/FoolsMate3.png"/><br><br>
Hilarious! I intercepted this request on burp suite but it could not capture it. So I played some other move with the rook, sent it to the repeater and changed the request to a8.<br><br>
<img alt="image" src="images/FoolsMate4.png"/><br><br>
Got the flag!<br>
This challenge was a very short one and I enjoyed it.
