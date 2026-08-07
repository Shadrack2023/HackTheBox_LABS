> Writeup · part of **[htb-walkthroughs](../README.md)** · [⬇ original PDF](GettingStartedWith.pdf)

---

GETTING STARTED WITH HTB 

# **shujaa** 

INTRODUCTION 

This has been a great module especially when it comes to giving me a general picture of what is required for any pentesting or redteaming. 

I have also understood some cybersecurity terms like Risk management and Impact. 

Q & A 

![](images/Getting-Started-With-HTB.pdf-0001-06.png)

![](images/Getting-Started-With-HTB.pdf-0001-07.png)

![](images/Getting-Started-With-HTB.pdf-0001-08.png)

Now this is how I went about to find the flag. # I ran an nmap scan as shown below 

1/24 

![](images/Getting-Started-With-HTB.pdf-0002-00.png)

# From the output, ftp=21, ssh=22, http=80, SMB=445,139 telenet=2323,. with this information I know I had somewhere to begin my enumeration. 

# From the nmap scan, ftp service running on port 21 was configured to allow anon login, and with this I abused it to gain access to the target via this port. 

2/24 

![](images/Getting-Started-With-HTB.pdf-0003-00.png)

# As you can see I managed to download a login.txt that could of importance later-on. Using cat cmd to read the content in the login.txt, I found login credentials as shown below 

![](images/Getting-Started-With-HTB.pdf-0003-02.png)

# Unfortunately when I tried to ssh using this credentials, it failed. 

3/24 

- # Checking the smb for anything fancy, I managed to list shares and. The service allowed anonymous login. 

# I managed to access users shares as the admin using the credentials I got initially just as shown in the image below. 

![](images/Getting-Started-With-HTB.pdf-0004-02.png)

![](images/Getting-Started-With-HTB.pdf-0004-03.png)

# we found some sensitive information from the passwords.txt downloaded from the target machine, with this, increased my attack surface on the target machine. 

# Since there was a service running on port 80, I checked for hidden directories by bruteforcing for hidden directories using the gobuster tool. 

4/24 

![](images/Getting-Started-With-HTB.pdf-0005-00.png)

# Visiting the page we are presented with a login page. Checking the source code of /robots.txt, I found forgotten sensitive information that might be of use in bypassing the login page. 

![](images/Getting-Started-With-HTB.pdf-0005-02.png)

# After a successful login, I was presented with the flag as shown below. 

5/24 

![](images/Getting-Started-With-HTB.pdf-0006-00.png)

![](images/Getting-Started-With-HTB.pdf-0006-01.png)

![](images/Getting-Started-With-HTB.pdf-0006-02.png)

![](images/Getting-Started-With-HTB.pdf-0006-03.png)

![](images/Getting-Started-With-HTB.pdf-0006-04.png)

![](images/Getting-Started-With-HTB.pdf-0006-05.png)

6/24 

![](images/Getting-Started-With-HTB.pdf-0007-00.png)

![](images/Getting-Started-With-HTB.pdf-0007-01.png)

Now this is how I went about this task. I began my enumeration by running an nmap and here is the output. 

7/24 

![](images/Getting-Started-With-HTB.pdf-0008-00.png)

ssh and http were the only services running. 

# Using cURL tool against the target, I got some information that might help us further during this enumeration stage. 

![](images/Getting-Started-With-HTB.pdf-0008-03.png)

# I bruteforced for hidden directories and here got some of great interest 

8/24 

![](images/Getting-Started-With-HTB.pdf-0009-00.png)

# I found a source code that was a prove that there was a user called admin 

9/24 

![](images/Getting-Started-With-HTB.pdf-0010-00.png)

# Having the a valid username but no password, I tried to do a dictionary attack on the login page using hydra. But before then, I made a pass.txt using CEWL tool to come up with passwords related to the site. 

![](images/Getting-Started-With-HTB.pdf-0010-02.png)

# Now I initiated the dictionary attack as shown below and BOOM! I found valid pass and username 

10/24 

![](images/Getting-Started-With-HTB.pdf-0011-00.png)

# I managed to upload a .php file that will give us a reverse shell on the target 

![](images/Getting-Started-With-HTB.pdf-0011-02.png)

# Here is a prove that our .php file was uploaded successfully 

11/24 

![](images/Getting-Started-With-HTB.pdf-0012-00.png)

# I managed to receive a reverse shell as shown below 

12/24 

![](images/Getting-Started-With-HTB.pdf-0013-00.png)

13/24 

![](images/Getting-Started-With-HTB.pdf-0014-00.png)

# Thats how I got the user.txt 

![](images/Getting-Started-With-HTB.pdf-0014-02.png)

here is how i solved this. 

14/24 

![](images/Getting-Started-With-HTB.pdf-0015-00.png)

15/24 

![](images/Getting-Started-With-HTB.pdf-0016-00.png)

![](images/Getting-Started-With-HTB.pdf-0016-01.png)

![](images/Getting-Started-With-HTB.pdf-0016-02.png)

16/24 

![](images/Getting-Started-With-HTB.pdf-0017-00.png)

# Using the credentials given, I managed to ssh into the target machine as user1. 

![](images/Getting-Started-With-HTB.pdf-0017-02.png)

#  This is how I escalated my privilege from user1 to user2 as shown in the image above. 

![](images/Getting-Started-With-HTB.pdf-0017-04.png)

# Generating the private key using ssh-keygen, I managed to ssh to the target as user2 as shown below. 

17/24 

![](images/Getting-Started-With-HTB.pdf-0018-00.png)

![](images/Getting-Started-With-HTB.pdf-0018-01.png)

18/24 

and this is how i got the flag. 

![](images/Getting-Started-With-HTB.pdf-0019-01.png)

## # KNOWLEDGE CHECK 

![](images/Getting-Started-With-HTB.pdf-0019-03.png)

Now this is how i solved this last section 

# I was given a target and began my enumeration by running an nmap scan on the target 

![](images/Getting-Started-With-HTB.pdf-0019-06.png)

port 80 is open to mean there is an http service running on ther browser. 

# Also bruteforced for directories that might be of importance to me during this enumeration stage. Here were the results 

19/24 

![](images/Getting-Started-With-HTB.pdf-0020-00.png)

visiting /admin, I'm presented with a login page with which I dont have valid credentials to let me in. Fortunately I found username and a hashed password_sha-1. 

![](images/Getting-Started-With-HTB.pdf-0020-02.png)

# Using hashcat, I managed to crack the password  as shown in the image below. 

20/24 

![](images/Getting-Started-With-HTB.pdf-0021-00.png)

#username= admin & password= admin. Boom! we are in. 

# I used metasploit fm to further my attack on the target and this is the final result on how I got to the user.txt as shown in the image below. 

21/24 

![](images/Getting-Started-With-HTB.pdf-0022-00.png)

![](images/Getting-Started-With-HTB.pdf-0022-01.png)

This is how i got shell access and captured the root.txt flag. 

22/24 

![](images/Getting-Started-With-HTB.pdf-0023-00.png)

THE END!!! 

23/24 

![](images/Getting-Started-With-HTB.pdf-0024-00.png)

<u>https://academy.hackthebox.com/achievement/1287818/77</u> 

CONCLUSION 

This was a moderate but so informative module that has taught a lot of techniques required to conduct a pentest 

24/24
