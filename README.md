## Windows-Forensics - THM ##

### Q1 - Whats the version and year of the windows machine? ###
```Windows Server 2016``` Run ```systeminfo``` in cmd and you can find this information, however, you will only need the OS Name paramaters.
![Q1](https://user-images.githubusercontent.com/18509521/211958673-7b0f3671-7b0b-48da-9113-0e9f22229bd0.png)

### Q2 - Which user logged in last? ###
```Administrator``` Open Event Viewer and go to Windows Logs > Security > "Filter Current Log" Set the event ID for 4624 so we can see successful logins and go the the most recent entry that matches a user logon.
![Q2](https://user-images.githubusercontent.com/18509521/211947634-27e52fd7-056b-4231-bbd7-d45f1d47d5b0.png)

### Q3 - When did John log onto the system last? - Answer format: MM/DD/YYYY H:MM:SS AM/PM ###
```03/02/2019 5:48:32 PM``` You can sift through the event logs until you find the log where user John has logged in or alternatively you can open a command prompt and run ```net user john``` to find the information
![Q3](https://user-images.githubusercontent.com/18509521/211947959-3b263ebe-655f-486e-a46f-8fb5beb85b0b.png)

### Q4 - What IP does the system connect to when it first starts? ###
```10.34.2.3``` As this is connecting every time the machine starts we need to look in the Run registry. The Run registry runs a specified item each time a machine boots up, the RunOnce will run the item once and then delete the key. This registry key can be found at ```HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Run```
![Q4](https://user-images.githubusercontent.com/18509521/211959963-d0ddd3fb-bdcf-48e8-a3c6-778f57abfbe7.png)

### Q5 - What two accounts had administrative privileges (other than the Administrator user)? Answer format: username1, username2 ###
```jenny, guest``` If we run ```net user``` we can see the user listed on the domain, running the command on each user we can enumerate what permissions they have.
![Q5](https://user-images.githubusercontent.com/18509521/211949647-63b561b1-a056-4295-b4f6-a494002d56b1.png)

### Q6 - Whats the name of the scheduled task that is malicous. ###
```Clean file system``` If we open Task Scheduler we can see there are some suspicious tasks in there however one that sticks out is Clean File System as it is running nc.ps1 or a netcat listener on port 1348.
![Q6](https://user-images.githubusercontent.com/18509521/211950664-ddf11f24-7906-4773-8d80-b3e6237aeea7.png)

### Q7 - What file was the task trying to run daily? ###
```nc.ps1``` Can be found in the Actions tab, look at Q6 screenshot

### Q8 - What port did this file listen locally for? ###
```1348``` Can be found in the Actions tab, look at Q6 screenshot

### Q9 - When did Jenny last logon? ###
```Never``` Running ```net user Jenny``` you will be able to find the information
![Q9](https://user-images.githubusercontent.com/18509521/211951163-3f79cca7-603d-4ac0-a7e0-c6de15309985.png)

### Q10 - At what date did the compromise take place? Answer format: MM/DD/YYYY ###
```03/02/2019``` As Task Scheduler history has been disabled and there are no logs in event viewer for when these tasks were created we can reference back to the accounts that were utilised and made from this compromise. If we look at Guest, Jenny and Johnny all the accounts were made on the same day. That is the date we are looking for.

### Q11 - At what time did Windows first assign special privileges to a new logon? Answer format: MM/DD/YYYY HH:MM:SS AM/PM ###
```03/02/2019 4:04:49 PM``` I had to use the hint for this one and am unsure why this is the answer as there are successful logs before this one.
![Q11](https://user-images.githubusercontent.com/18509521/211952835-ecf64ee9-25cf-4782-9a99-ac39b2d69901.png)

### Q12 - What tool was used to get Windows passwords? ###
```mimikatz``` Going back to Task Scheduler we can see a program is running called mim.exe from the /TMP/ directory, if we go to that directory we can find all the files relating to the malicious program. Opening the text file mim-out will show as the program being used.
![Q12](https://user-images.githubusercontent.com/18509521/211954127-b2c5e5fa-4598-4629-8aac-9d0f450c1cee.png)
![Q12-1](https://user-images.githubusercontent.com/18509521/211954175-807f6130-6800-473a-b8ca-2ee006940301.png)

### Q13 - What was the attackers external control and command servers IP? ###
```76.32.97.132```The hosts file is a plain text file used to map host names to IP addresses. On Windows, it is located in ```C:\Windows\System32\drivers\etc``` If we go there we can see the attackers have added in their own IP address and mapped it to google.com
![Q13](https://user-images.githubusercontent.com/18509521/211961399-a59e7875-661b-4f58-87ca-4c65797887e0.png)

### Q14 - What was the extension name of the shell uploaded via the servers website? ###
```.jsp``` Inetpub is the folder on a computer that is the default folder for Microsoft Internet Information Services (IIS). The website content and web apps are stored in the inetpub folder — which keeps it organized and secure. This computer had an exposed IIS server running and the shell got uploaded to it there, that is why it is stored in this folder.
![Q14](https://user-images.githubusercontent.com/18509521/211962232-c6892a4e-50a6-4318-8820-352616e34b2e.png)

### Q14 - What was the last port the attacker opened? ###
```1337``` If we navigate to the windows firewall and check the inbound rules we can see the latest rule that was added which was to allow all outside connections coming in from port 1337.
![Q15](https://user-images.githubusercontent.com/18509521/211962430-ccaf4284-80db-45f0-8a5b-5f460f617dd8.png)

### Q15 - Check for DNS poisoning, what site was targeted? ###
```google.com``` Going back to the hosts file the attackers mapped their ip address to google.com domain
