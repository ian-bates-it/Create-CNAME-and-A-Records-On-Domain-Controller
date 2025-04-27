# Create CNAME and A-Records On A Windows 2022 Server Domain Controller



---
---


X

---
<br />

<h3>Log into the Windows 10 Pro Client</h3>

- Remote into the Windows 10 Pro virtual machine.
- Log in with an account in the `Domain Admins` OU. Here is an [example of how to add a user to the `Domain Admins` organizational unit](https://github.com/ian-bates-it/Active-Directory-Users-And-Computers?tab=readme-ov-file#step-2-add-user-as-a-member-of-domain-admins).



  <img src="" height="80%" width="80%" />


---
<br />

<h3>View the DNS Cache</h3>

- Open a PowerShell Terminal.
- run the command `ipconfig /displaydns > cache.txt`
- This will create a text file `called cache.txt` which will display the DNS cache for our Windows 10 Pro virtual machine as shown below.

PowerShell Command Example: 
  <img src="" height="50%" width="50%" />


cache.txt example of a DNS cache:
  <img src="" height="80%" width="80%" />



---
<br />

<h2>Add a record to the local hosts file</h2>




---
<br />

<h3>ping the new record name</h3>

- I will use `mainserver` as the name of my new DNS record.
- Before editing the `hosts.txt` file, pinging `mainserver` will return an error as shown below.

  <img src="" height="50%" width="50%" />




