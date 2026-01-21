<h1>SOC165 - Possible SQL Injection Payload Detected</h1>


<h2>Description</h2>
This lab reviews a possible SQLi payload alert triggered by SOC165 - Possible SQL Injection Payload Detected. The HTTP request contains multiple URL-encoded SQL keywords injected into a query parameter, which is suspicious behavior. As well as the classic OR 1=1 SQL statement was detected and triggered the security rule. Unfortuntely, this possible attack was also allowed. Lets take a closer look and see whats going on.
<br />


<h2> Utilities Used</h2>

- <b>LetsDefend SIEM</b> 
- <b>LetsDefend Email Security</b>
- <b>LetsDefend EDR</b>
- <b>URL Decoder</b>
- <b>VirusTotal</b>

<h2>Environments Used </h2>

- <b>Windows 10</b> (22H2)

<h2>Alert walk-through:</h2>

<p align="center">
Go to investigation channel in Monitoring to get the context of the alert: <br/>
<img src="https://i.imgur.com/oihPZFe.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
After taking ownership of the case, very simply look at the rule name and examine the direction of the traffic thats occuring between both devices:  <br/>
<img src="https://i.imgur.com/JWl6L2J.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
The specific pattern/signature that the rule is detecting is "OR 1=1" mentioned in Alert Trigger Reason. There are also several inbound HTTP request being made from an external IP address : <br/>
<img src="https://i.imgur.com/ivKMMAB.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/VUjwazI.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
the first HTTP request to the server at Feb 25, 2022, 11:30 AM looks normal, with no injection attempts present in its parameters. The response status and size also appear normal (status: 200 / size: 3547). But on Feb 25, 2022, 11:34 AM, which corresponds to the alert, the request contains URL-encoded SQL keywords, and both the response size and status change drastically (status: 500 / size: 948), indicating a likely classic error-based SQL injection attempt:  <br/>
<img src="https://i.imgur.com/mbPE9vf.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/KnfCMlH.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
DigitalOcean is the owner. This is inbound traffic. It is a Pool addresses and its a web hosting cloud provider. IP address is listed as malicious by VT security vendors. Traffic is not coming from company network, its external. Hostname: WebServer1001. The owner is webadmin (admin account) last login was on Feb, 10, 2022, 11:12 PM: <br/>
<img src="https://i.imgur.com/AcLpWk1.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/EGu4iTw.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Now we'll examine the encoded SQL payloads embedded in the URL parameter with URL Decoder :  <br/>
<img src="https://i.imgur.com/RjCFTPV.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
When decoded, we see the SQL payload is a classic error-based SQLi attack " OR 1 = 1 -- -. This is typically used to bypass the need of user credentials to potentially gain unathorizated access into a system. This is why the alert rule was triggered.  <br/>
<img src="https://i.imgur.com/YIz96Pw.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
The traffic was malicious because it contained the SQLi payload:  <br/>
<img src="https://i.imgur.com/sCujGob.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was a SQLi:  <br/>
<img src="https://i.imgur.com/u1dOxvU.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Email Security was checked to determine whether this alert was triggered by a scheduled pen test or a security simulation. After searching the inbox for the hostname, IP address, and username, no emails were found validating this activity as planned work:  <br/>
<img src="https://i.imgur.com/TowIxaS.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<img src="https://i.imgur.com/9LaX3Hl.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was an inbound HTTP request. Internet to Company Network:  <br/>
<img src="https://i.imgur.com/h3wEizW.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
check if the attack was successful:  <br/>
<img src="https://i.imgur.com/toHZfZt.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Although the SQLi payloads requests were successfully sent and allowed by the device, after reviewing the web server’s endpoint security data (including running processes, network actions, terminal and browser history) no further actions were observed on the server :  <br/>
<img src="https://i.imgur.com/Pv9jQqb.png" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This attack was unsuccessful. Although it was successfully delivered, there was no successful system exploitation:  <br/>
<img src="https://i.imgur.com/ClHmwIZ.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Add the artifacts that were examined in the investigation :  <br/>
<img src="https://i.imgur.com/dqGOhDU.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
This was an unsuccesful attack, so no further escalation is needed :  <br/>
<img src="https://i.imgur.com/wjPCpa8.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
write your analyst notes from the case. click confirm and youre finished! Next closing the case..:  <br/>
<img src="https://i.imgur.com/nemvEaV.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
Lastly, the alert triggered was a True Positive because the security system correctly identified this as unplanned malicious SQL payloads being delivered to the web server :  <br/>
<img src="https://i.imgur.com/rNJ1O68.jpeg" height="80%" width="80%" alt="Disk Sanitization Steps"/>
<br />
<br />
</p>

<!--
 ```diff
- text in red
+ text in green
! text in orange
# text in gray
@@ text in purple (and bold)@@
```
--!>
