<h1>Bandit – Over the Wire Walkthrough</h1>
<h2>Level 1 to 5</h2>
All the Bandit levels works on SSH shells.
SSh is a network layer protocol which allows remote access of devices, manage servers and transfer files over an unsecured network.<br>
All the labs are based on ssh. We need to login in via ssh to solve all the levels.<br>
Each level has the password for the next level.<br>
Before accessing the next level, remember to logout from the current session using <code>exit</code> command.<br><br>

<code>Syntax: ssh -p {port_number} username@hostname</code>

Level 0:
In this challenge, I had to connect to <code>bandit.labs.overthewire.org</code> on port 2220 using ssh. <br>
Username: bandit0<br>
Password: bandit0<br>
<img alt="image" src="images/Bandit1.png"/><br>
Once I entered the password I was in.<br><br>

Level 0 -> Level 1<br>
The Level description said that the password for the next level was stored in <code>‘readme’</code>.<br>
So I listed all the files in the current directory using <code>ls</code> command and got the password using <code>cat</code> which is used to show the contents of a file.<br>
<img alt="image" src="images/Bandit2.png"/><br><br>

Level 1 -> Level 2<br>
This level says that the password is stored in -. It was located in the home directory so I listed all the contents of this directory using <code>ls</code>.<br>
On using the google search for dashed filename, I found that:<br>
“This type of approach has a lot of misunderstanding because using - as an argument refers to STDIN/STDOUT i.e dev/stdin or dev/stdout .So if you want to open this type of file you have to specify the full location of the file such as ./- .For eg. , if you want to see what is in that file use <code>cat ./-</code>“<br>
So the command was: <code>cat ./-</code><br>
<img alt="image" src="images/Bandit3.png"/><br><br>

Level 2 -> Level 3<br>
This level says: “The password for the next level is stored in a file called<br>
 <code>--spaces in this filename--</code> located in the home directory”<br>
This was confusing then I figured out that “--spaces in this filename--” is actually the name of the file. 
So I did the same thing I did in the last level but to deal with the spaces in the name, I used “ ”:<br>
<img alt="image" src="images/Bandit4.png"/><br><br>

Level 3 -> Level 4<br>
This level said that the password was stored in a hidden file in a directory <code>inhere</code>.<br>
Hidden directories are not listed when <code>ls</code> is used.<br>
So, I used <code>ls -la</code> to list all the hidden directories and files.<br>
<img alt="image" src="images/Bandit5.png"/><br><br>

Level 4 -> Level 5<br>
The password for this was stored in a **human-readable** format and in one of the files of inhere.<br> 
Checked every file and got the password.
<img alt="image" src="images/Bandit6.png"/><br><br>
<img alt="image" src="images/Bandit7.png"/><br><br>

Level 5 -> Level 6<br>
For the next level, the password was stored in a file which was:<br>
•	Human-readable format<br>
•	1033 bytes in size<br>
•	Non executable<br>
All can be used by adding flag to find command.<br>
Flag for human-readable format: <code>-readable</code><br>
Flag for 1033 bytes size: <code>-size 1033c</code><br>
Flag for non executable: <code>! -executable</code><br>
After combining everything, we get the file the password is stored in.<br>
Notice that file2 does not have *execute (x)* in its permissions.<br>
<img alt="image" src="images/Bandit8.png"/><br><br>


