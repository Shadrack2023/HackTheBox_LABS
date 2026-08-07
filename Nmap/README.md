> Writeup · part of **[htb-walkthroughs](../README.md)** · [⬇ original PDF](Nmap.pdf)

---

# **NETWORKING** 

INTRODUCTION: 

Enumeration is the process simply put of, finding as many ways as possible to attack a particular machine/network. Consequently, I want to work on the breadth of my enumeration and exhausting all possible attack vectors. Ultimately in pentesting you are trying to find all possible vulnerabilities in a network, not just one. 

![](images/Nmap-HTB.pdf-0001-03.png)

TTL can help us determine the operating system, and by default, window OS = 128 

linux OS = 64 

mac OS = 64 

Having this knowledge, I managed to determine the OS as windows. 

![](images/Nmap-HTB.pdf-0001-08.png)

I ran an nmap scan of all the ports, and here was the output as shown below. 

![](images/Nmap-HTB.pdf-0001-10.png)

![](images/Nmap-HTB.pdf-0001-11.png)

To enumerate the hostname, I ran a nmap script for hostname discovery just as shown in the image below. 

1/8 

![](images/Nmap-HTB.pdf-0002-00.png)

![](images/Nmap-HTB.pdf-0002-01.png)

2/8 

![](images/Nmap-HTB.pdf-0003-00.png)

![](images/Nmap-HTB.pdf-0003-01.png)

Running an nmap script to enumerate port 80, I found a directory /robots.txt just as shown below. 

3/8 

![](images/Nmap-HTB.pdf-0004-00.png)

visiting that path url I find this flag. 

![](images/Nmap-HTB.pdf-0004-02.png)

![](images/Nmap-HTB.pdf-0004-03.png)

From my initial nmap scan to find all tcp ports on the target, I found port 31337 to be the highest port. 

4/8 

![](images/Nmap-HTB.pdf-0005-00.png)

![](images/Nmap-HTB.pdf-0005-01.png)

![](images/Nmap-HTB.pdf-0005-02.png)

5/8 

![](images/Nmap-HTB.pdf-0006-00.png)

So I decided to use a different approach to find this flag. Instead of running an nmap scan, I used dig to dig for dns server version just as shown in the image below. 

![](images/Nmap-HTB.pdf-0006-02.png)

![](images/Nmap-HTB.pdf-0006-03.png)

This task hinted at large amounts of data and so a full port scan (-p-) reveals port 50000. Above we set up a netcat listener between DNS port 53 and this new mysterious port 50000. Let the netcat listener run for a second or two and the flag presents itself with a successful 220 request. 

6/8 

![](images/Nmap-HTB.pdf-0007-00.png)

![](images/Nmap-HTB.pdf-0007-01.png)

7/8 

<u>https://academy.hackthebox.com/achievement/1287818/19</u> 

## CONCLUSION 

This module required a lot of outside research, but I feel it’s part of the job. It’s not a memory of everything game, but knowing where to look for the tool you need to do the job you want. 

8/8
