# Create CNAME and A-Records On A Windows 2022 Server Domain Controller



---
---


X

---
<br />

<h3>Log into the Windows 10 Pro Client</h3>

- Remote into the Windows 10 Pro virtual machine.
- Log in with an account in the `Domain Admins` OU. Here is an [example of how to add a user to the `Domain Admins` organizational unit](https://github.com/ian-bates-it/Active-Directory-Users-And-Computers?tab=readme-ov-file#step-2-add-user-as-a-member-of-domain-admins).

- Use the public IP address of the Windows 10 Pro virtual machine to remotely connect to the VM as shown below

  <img src="https://github.com/user-attachments/assets/eefdb1a9-702d-4a8d-963c-c851c50f2e3a" height="60%" width="60%" />



---
<br />

<h3>View the DNS Cache</h3>

- Open a PowerShell Terminal.
- run the command `ipconfig /displaydns > cache.txt`
- This will create a text file `called cache.txt` which will display the DNS cache for our Windows 10 Pro virtual machine as shown below.
- You can open the `cache.txt`

**PowerShell Command Example: **

  <img src="https://github.com/user-attachments/assets/610fc292-1568-4d21-88b8-ece6442c7287" height="50%" width="50%" />

<br />

**cache.txt example of a DNS cache:**

  <img src="https://github.com/user-attachments/assets/7284741f-56fa-4209-be8b-621b71cdd2be" height="50%" width="50%" />


---
---
<br />

<h2>Add a DNS Record to the local hosts file</h2>



---
<br />

<h3>Open a Text File As Administrator</h3>

- Open a `NotePad` text file 

  <img src="https://github.com/user-attachments/assets/045bec7a-2183-4a6e-88c1-1a2f48a93ab4" height="40%" width="40%" />

- Click File > Open
- Select `All File` Types.

  <img src="https://github.com/user-attachments/assets/0ff241bb-611b-4138-b0c8-9825b72fd6cb" height="30%" width="30%" />


<br />

<h4> In `Windows > System 32 > drivers > etc` open the `hosts.txt` file.</h4>

  <img src="https://github.com/user-attachments/assets/93c9f685-251f-46e5-bb93-3bd43cdc21f4" height="60%" width="60%" />




---
<br />

<h3>Add DNS Record To The Local Hosts.txt File</h3>

- The local `hosts` file is where we can map ip address to a host name.
- Add a new hostname and map it to the loopback address (`127.0.0.1`)
- I will use `host.file.server` as the name of my new DNS record.
- Save the `hosts.txt` file.

  <img src="https://github.com/user-attachments/assets/25fe894c-1312-4c24-8391-557dc2ee44d8" height="70%" width="70%" />



---
<br />

<h3>ping the new host name</h3>

- 
- Before editing the `hosts.txt` file, pinging `host.file.server` will return an error (`Ping request cound not find host` as shown below.

  <img src="https://github.com/user-attachments/assets/5fa8da57-599d-40e9-b4a5-2f4fa7605c5f" height="50%" width="50%" />



<br />
- After updating the local `hosts.txt` file, we can now ping our new host name (`host.file.server`) with 0% packet loss as shown below:

  <img src="https://github.com/user-attachments/assets/3d483f52-4167-4947-8037-351c3e940b0b" height="50%" width="50%" />


<br />

- The ping order of operations runs as follows.
1. Check the local DNS Cache.
2. Check the local Host File. (`Where our mainserver record exists`)
3. Check the DNS Server.  


---
---
<br />

<h2>Add a DNS Record to the local hosts file</h2>



---
<br />

<h3>Add the new </h3>



  <img src="" height="50%" width="50%" />


