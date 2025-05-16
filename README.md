<!--
Practing with DNS

Windows 2022 Server
<img src="https://github.com/user-attachments/assets/4391ac8b-a4ba-42a5-8707-73f55ac21c8d" height="40%" width="40%" />

Windows 10 Pro
<img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="40%" width="40%" />
-->


<p align="center">
<img src="https://github.com/user-attachments/assets/bc37714a-9c61-45de-8a98-315d4ecc85af" alt="Microsoft Active Directory Logo"/>
</p>

# Create CNAME and A-Records On A Windows 2022 Server Domain Controller

- This chapter will make use of the Active Directory infrastructure we created in the [Active Directory Home Lab at this link](https://github.com/ian-bates-it/ian-bates-it/blob/main/README.md#active-directory-home-lab).
  - We'll need Active Directory running on a virtual machine. In this case, on a Windows Server 2022 Datacenter Azure edition virtual machine acting as the Domain Controller.
  - We'll need a client machine joined to the domain. In this case, a Windows 10 Pro (21H2) virtual machine acting as the client machine. 
 
- Active Directory Domain Services requires a DNS server to be installed on the network. We installed the DNS Server when we [installed Active Directory and turned a Windows 2022 Server VM into a Domain Controller in Chapter 3 of the Active Directory Home Lab series which you can see at this link.](https://github.com/ian-bates-it/Install-Active-Directory-on-Windows-2022-Server?tab=readme-ov-file#active-directory-domain-services)

- In `Part 1` we'll view the local DNS cache on the Windows 10 Pro Client VM with `ipconfig /displaydns > cache.txt`
- In `Part 2` we'll create an A-Record in the `hosts.txt` file on the Windows 10 Pro Client VM, mapping our loopback address to a hostname (127.0.0.1 `host.file.server`).
- In `Part 3` we'll use the DNS Manager in the Windows 2022 Server Domain Controller to create an A-Record mapping hostname `domain-controller-server` to IP address 10.0.0.5.
- In `Part 4` we'll use the Windows 10 Pro Client VM to ping the A-Record we created in `Part 3`, `ping domain-controller-server`.
- In `Part 5` we'll use the DNS Manager in the Windows 2022 Server Domain Controller to edit the IP address mapped to our hostname `domain-controller-server` that we set up in `Part 3` above.
    - The purpose of this is to replicate a situation where an end-user is trying to access a resource where the DNS has changed at the DNS server level and the local Client DNS cache is outdated.
    - We will update the A-Record mapping for hostname `domain-controller-server` from its original `10.0.0.5` to one of google's servers at `8.8.8.8`.
- In `Part 6` we'll confirm that the Windows 10 Pro Client VM has an outdated DNS mapping for our A-Record (`domain-controller-server`)
    - The local DNS cache for the Client VM will show that `domain-controller-server` is still pointing to the old IP address of `10.0.0.5`
    - We will then flush the DNS cache for the Client VM (`ipconfig /flushdns`) to update the hostname's new A-Record (which is now 8.8.8.8).
    - The purpose of this `Part 6` is to identify and resolve the situation where an end-user is trying to access a resource where the DNS has changed at the DNS server level and the local Client DNS cache is outdated.
- In `Part 7` we will use the DNS Manager in the Windows 2022 Server Domain Controller to create a CNAME record.
    - In this example I will create the following DNS CNAME mapping in DNS Server: (`get-help` => www.StackOverflow.com)
- In `Part 8` we will use the Windows 10 Pro Client VM to observe the new CNAME record (`get-help`) with `nslookup` and `ping`


<!--
- The order of operations for a ping request is
  1. Check the local DNS Cache. (Fastest)
  2. Check the local Host file. (Medium Speed)
  3. Check the DNS Server. (Slowest)
- So we will check the local DNS cache on the Windows 10 Pro client VM with `ipconfig /displaydns`
    - Then we will clear the DNS cache with `ipconfig /flushdns`
- Then we will map a hostname (host.file.server) to our loopback address (127.0.0.1) in the Windows 10 Pro client VM's `hosts.txt` file.

- Then we will create a new A-Record on the Windows 2022 Server DNS Manger (`domain-controller-server.IanBates.com`).
    - Then will ping the new A-Record on the Windows 10 Pro Client VM.
 
- Then in `Part 3` of this chapter, 

The A-Record converts a human readable hostname to an IP address. 
-->

---
<br />


<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Windows Administrative Tools > DNS Manager

<!--
- Active Directory DNS Manager


<!--
- Active Directory Domain Services
-->

<h2>Operating Systems Used </h2>

- Windows Server 2022 Datacenter Azure edition (Domain Controller)
- Windows 10 Pro (21H2) (Client)

<h2>High-Level Configuration Steps</h2>

<!--
- Part 1: [View Windows 10 Pro VM Local DNS Cache](https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#view-windows-10-pro-vm-local-dns-cache)
- Part 2: [Add a DNS Record to the local hosts file](https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#add-a-dns-record-to-the-local-hosts-file)
- Part 3: [Edit a DNS A-Record On The Domain Controller](https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#edit-a-dns-a-record-on-the-domain-controller)
- Part 4: [Create a CNAME Record on Windows 2022 Server Domain Controller and ping it from our Windows 10 Pro Client](https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#create-a-cname-record-on-windows-2022-server-domain-controller)
- Part 5: Ping the CNAME Record on the Windows 10 Pro Client
-->

<table>
  <thead>
        <tr>
            <th width="100px" align="center">
              Part
            </th>
            <th align="center">
              Description
            </th>
            <th align="center">
              Virtual Machine Used
            </th>
        </tr>
  </thead>
  <tbody>
        <tr>
          <td width="100px" align="center">Part 1:</td>
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-1-view-local-dns-cache--in-the-client-vm">
            View the Local DNS Cache on the Client VM
            <br />
            (`ipconfig /disdplaydns`)
          </a></td>
          <td align="center">
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-1-view-local-dns-cache--in-the-client-vm">
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="40%" width="40%" />
              <br />
              Windows 10 Pro (Client) VM
            </a>
          </td>
        </tr>        
        <tr>
          <td width="100px" align="center">Part 2:</td>
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-2-add-an-a-record--in-hoststxt-file--on-the-client-vm-">
            Add a local A-Record to the `hosts.txt` file on the Client VM
            <br />
            (`127.0.0.1` => `host.file.server`)
          </a></td>
          <td align="center">
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#add-a-dns-record-to-the-local-hosts-file">
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="40%" width="40%" />
              <br />
              Windows 10 Pro (Client) VM
            </a>
          </td>
        </tr>
        <tr>
          <td width="100px" align="center">Part 3:</td>      
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-3-create-a-record-in--dns-manager--on-domain-controller">
            Create an A-Record Using DNS Manager on the Domain Controller
            <br />
            (`domain-controller-server` => 10.0.0.5)
          </a></td>            
          <td align="center">
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-3-create-a-record-in--dns-manager--on-domain-controller">
              <img src="https://github.com/user-attachments/assets/4391ac8b-a4ba-42a5-8707-73f55ac21c8d" height="40%" width="40%" />
              <br />
              Windows 2022 Server 
              <br />
              (Domain Controller) VM
            </a>
          </td>
        </tr>
        <tr>
          <td width="100px" align="center">Part 4:</td>      
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-4-ping-a-record--domain-controller-server--from-the-client-vm">
            Ping the new A-Record Created in the Controller from the Client VM
            <br />
            (ping domain-controller-server)
          </a></td>      
          <td align="center">            
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-4-ping-a-record--domain-controller-server--from-the-client-vm">
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="40%" width="40%" />
              <br />
              Windows 10 Pro (Client) VM
              (Domain Controller) VM
            </a>            
          </td>
        </tr> 
        <tr>
          <td width="100px" align="center">Part 5:</td>      
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-5-edit-a-record--domain-controller-server--in-dns-manager">
            Edit the A-Record in DNS Manager on the Domain Controller
            <br />
            (change A-Record `domain-controller-server` from 10.0.0.5 to 8.8.8.8)
          </a></td>      
          <td align="center">            
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-5-edit-a-record--domain-controller-server--in-dns-manager">
              <img src="https://github.com/user-attachments/assets/4391ac8b-a4ba-42a5-8707-73f55ac21c8d" height="40%" width="40%" />
              <br />
              Windows 2022 Server 
              <br />
              (Domain Controller) VM
            </a>            
          </td>
        </tr>
        <tr>
          <td width="100px" align="center">Part 6:</td>      
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#ping-the-new-host-record-domain-controller-server-from-the-client-virtual-machine">
            Update the local DNS cache on the Client VM for subdomain `domain-controller-server`
            <br />
            (ipconfig /flushdns)
          </a></td>      
          <td align="center">            
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#ping-the-new-host-record-domain-controller-server-from-the-client-virtual-machine">
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="40%" width="40%" />
              <br />
              Windows 10 Pro (Client) VM
            </a>            
          </td>
        </tr>  
        <tr>
          <td width="100px" align="center">Part 7:</td>      
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-7-create-cname-record-get-help-in--the-domain-controller">
            Create a CNAME Record on Windows 2022 Server Domain Controller
            <br />
            (`get-help` => www.StackOverflow.com)
          </a></td>      
          <td align="center">            
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-7-create-cname-record-get-help-in--the-domain-controller">
              <img src="https://github.com/user-attachments/assets/4391ac8b-a4ba-42a5-8707-73f55ac21c8d" height="40%" width="40%" />
              <br />
              Windows 2022 Server 
              <br />
              (Domain Controller) VM
            </a>            
          </td>
        </tr>
        <tr>
          <td width="100px" align="center">Part 8:</td>      
          <td align="center"><a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-8-examine-the-new-cname--get-help-in-the-client-vm">
            Run `nslookup` and `ping` on the CNAME Record Created in the Controller from the Client
            <br />
            (nslookup get-help)
          </a></td>      
          <td align="center">            
            <a href="https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#part-8-examine-the-new-cname--get-help-in-the-client-vm">
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="40%" width="40%" />
              <br />
              Windows 10 Pro (Client) VM
            </a>            
          </td>
        </tr>    
  </tbody>
</table>


<!--
https://github.com/ian-bates-it/Create-CNAME-and-A-Records-On-Domain-Controller?tab=readme-ov-file#edit-a-dns-a-record-on-the-domain-controller
-->


<h2>Prerequisites</h2>

1. Complete [Chapter 1 of Active Directory Home Lab series, Creating a Windows 10 Pro and Windows 2022 Server Virtual Machines in Azure.](https://github.com/ian-bates-it/Azure-Virtual-Machine-Setup)

2. Complete [Chapter 2 of the Active Directory Home Lab series, Configuring the Windows 10 Pro (Client) to use the static private IP of our Windows 2022 Server Domain Controller as its DNS Server.](https://github.com/ian-bates-it/Azure-Controller-Client-Configuration)

3. Complete [Chapter 3 of the Active Directory Home Lab series, Installing Active Directory on a Windows 2022 Server VM and promoting it to a Domain Controller.](https://github.com/ian-bates-it/Install-Active-Directory-on-Windows-2022-Server)

<!--
4. Complete [Chapter 4 of the Active Directory Home Lab series, Create Organizational Units and Users in Active Directory.](https://github.com/ian-bates-it/Active-Directory-Users-And-Computers)
-->

4. Complete [Chapter 5 of the Active Directory Home Lab series, Joining a Windows 10 Pro Client to a Windows 2022 Server Domain Controller.](https://github.com/ian-bates-it/Join-A-Client-To-A-Domain)


<br />
<br />

---



<table>
        <tr>
            <th width="auto">
              <h1>Part 1: View Local DNS Cache <br /> in the Client VM</h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="60%" width="60%" />
            </th>
        </tr>
</table>


<h2>View Windows 10 Pro VM Local DNS Cache</h2>


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



<br />
<br />

---

<table>
        <tr>
            <th width="auto">
              <h1>Part 2: Add an A-Record <br /> in `hosts.txt` file <br /> on the Client VM </h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="60%" width="60%" />
            </th>
        </tr>
</table>

<h2>Add a DNS Record to the local hosts file</h2>

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

<h3>Add an A-Record To The Local Hosts.txt File</h3>

- The local `hosts` file is where we can map ip address to a host name.
- Add a new hostname and map it to the loopback address (`127.0.0.1`)
- I will use `host.file.server` as the name of my new DNS record.
- Save the `hosts.txt` file.

  <img src="https://github.com/user-attachments/assets/25fe894c-1312-4c24-8391-557dc2ee44d8" height="70%" width="70%" />



---
<br />

<h3>ping the new host name</h3>

- Before editing the `hosts.txt` file, pinging `host.file.server` will return an error (`Ping request cound not find host` as shown below.

  <img src="https://github.com/user-attachments/assets/5fa8da57-599d-40e9-b4a5-2f4fa7605c5f" height="90%" width="90%" />



<br />

- After updating the local `hosts.txt` file, we can now ping our new host name (`host.file.server`) with 0% packet loss as shown below:
- 
<br />

  <img src="https://github.com/user-attachments/assets/3d483f52-4167-4947-8037-351c3e940b0b" height="50%" width="50%" />


<br />
<br />

- The ping order of operations runs as follows.
  1. Check the local DNS Cache.
  2. Check the local Host File. (`Where our mainserver record exists`)
  3. Check the DNS Server.  


---
---
<br />


<table>
        <tr>
            <th width="auto">
              <h1>Part 3: Create A-Record in <br /> DNS Manager <br /> on Domain Controller</h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/4391ac8b-a4ba-42a5-8707-73f55ac21c8d" height="60%" width="60%" />
            </th>
        </tr>
</table>



<h2>Create a DNS A-Record On The Domain Controller</h2>



---
<br />
<h3>Log into the Windows 2022 Server Domain Controller</h3>


- Using Remote Desktop, log in with the public IP address for the Windows 2022 Server as shown below.

  <img src="https://github.com/user-attachments/assets/ffe9d33a-159b-4191-b8c6-213463f36c78" height="70%" width="70%" />


---
<br />
<h3>Open Windows Administrative Tools > DNS Manager</h3>

- We will create a DNS A-Record on the Windows 2022 Server Domain Controller and point it to the Windows 2022 Server's Private IP Address (`10.0.0.5`)
- Open `DNS` under `Windows Administrative Tools` as shown below.
- The `DNS Manager` was automatically installed when we [installed the Active Domain Controller the part of the documentation at this link](https://github.com/ian-bates-it/Install-Active-Directory-on-Windows-2022-Server).


  <img src="https://github.com/user-attachments/assets/ef7ede9a-b09c-4416-a5bd-ca6495630e58" height="60%" width="60%" />



---
<br />
<h3>Open Forward Lookup Zones in DNS Manager</h3>

1. Click on the DNS Server.
2. Click on `Forward Lookup Zones`
3. Click on the Domain Name (in this example, `IanBates.com`).
4. View the existing DNS records as shown below.


  <img src="https://github.com/user-attachments/assets/4776bddf-81ed-412e-80bd-9dd74be89ad0" height="70%" width="70%" />


---
<br />
<h3>New Host A Record</h3>

- Right-click in the `Domain Manager`
- Select `New Host (A or AAAA)...` as shown below.
  <img src="https://github.com/user-attachments/assets/bfbb83a5-788e-4b0b-9593-6979be9eaa3c" height="70%" width="70%" />


---
<br />
<h3>Create New A-Record For Desired Host Name</h3>

1. Enter the desired host name to serve as a subdomain. In this example, I will use the host name `domain-controller-server`, so the new A-record will point to the fully qualified domain name of `domain-controller-server.IanBates.com.`
2. Enter the Windows 2022 Server's private IP address. In my example, my server's private IP address is `10.0.0.5`.
3. Click `Add Host` as shown below.

  <img src="https://github.com/user-attachments/assets/c30d2d5b-8344-47bc-ae4f-ab520b17a620" height="60%" width="60%" />


---
<br />
<h3>Now the new A-Record Exists. (`domain-controller-server.IanBates.com`)</h3>

- We can now view the new A-Record entry that we created in the `DNS Manager` as shown below.


**Confirmation Message**

  <img src="https://github.com/user-attachments/assets/1b250144-6f4b-42a3-875c-9289a941be92" height="40%" width="40%" />


**DNS Manager**

  <img src="https://github.com/user-attachments/assets/dc9d0114-6de0-4cd4-a059-871f5669358c" height="70%" width="70%" />


<br />
<br />

---

<table>
        <tr>
            <th width="auto">
              <h1>Part 4: ping A-Record <br /> (`domain-controller-server`) <br /> from the Client VM</h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="60%" width="60%" />
            </th>
        </tr>
</table>


<h3>Ping The New Host Record (`domain-controller-server`) From The Client Virtual Machine</h3>

- From the Windows 10 Pro Client VM, we can ping the new Host Record in PowerShell.
- Run the command `ping domain-controller-server` and view the connection with 0% packet loss as shown below. The `ping` results successfully returns the correct IP address we set up above (`10.0.0.5`).
- We are able to get a response from our DNS Server, since it now knows the host record we created (`domain-controller-server`).

  <img src="https://github.com/user-attachments/assets/aa047de8-5e98-4ee6-9af7-627d5044e7f3" height="60%" width="60%" />



<br />
<br />

---

<table>
        <tr>
            <th width="auto">
              <h1>Part 5: Edit A-Record <br /> (`domain-controller-server`) <br /> In DNS Manager</h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/4391ac8b-a4ba-42a5-8707-73f55ac21c8d" height="80%" width="80%" />
            </th>
        </tr>
</table>

<h2>Edit a DNS A-Record On The Domain Controller</h2>


**Purpose Of This Exercise**
The purpose of this exercise is to replicate a situation where you have an end-user who is trying to access a resource where the DNS changed at the DNS server level, but their local machine is still looking for the old IP address saved in the DNS cache, they will need to have thier DNS cache cleared/updated.



---
<br />
<h3>Edit the IP Address Of The New Host Record (`domain-controller-server`) in DNS Manager</h3>

- On the Windows 2022 Server Domain Controller, open the DNS Manager.

1. Double-click the host record we created (`domain-controller-server`)
2. Edit the IP address from `10.0.0.5` to one of Google's IP address `8.8.8.8`
3. Click `OK` to save.

  <img src="https://github.com/user-attachments/assets/437249e0-2fe3-4475-bd61-633373c75f72" height="80%" width="80%" />


**DNS Changes have been saved**

  <img src="https://github.com/user-attachments/assets/f3b2c947-eb52-459a-94c8-a8b144591ac2" height="60%" width="60%" />






<br />
<br />

---

<table>
        <tr>
            <th width="auto">
              <h1>Part 6: Update DNS Cache <br /> For A-Record <br /> (`domain-controller-server`) <br /> in the Client</h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="60%" width="60%" />
            </th>
        </tr>
</table>

<h3>Ping the Host Record From The Client VM</h3>

- On the Windows 10 Pro VM Client open PowerShell.
- Run the command `ping domain-controller-server`
- The client VM will retrive the DNS information from the `local cache` so it has the old dns information for `domain-controller-server` which is `10.0.0.5`.
- The client VM does not have the updated IP address for `domain-controller-server` which is now 8.8.8.8 as shown below.

  <img src="https://github.com/user-attachments/assets/6631b0bc-7b56-41d8-a78a-f0ed465ae8b1" height="60%" width="60%" />


---
<br />
<h3>View the Client Local DNS Cache (`ipconfig /displaydns`) </h3>

- On the Windows 10 Pro VM Client, we want to view the local DNS cache.
- In the PowerShell terminal, run the command `ipconfig /displaydns > outdated-cache.txt`
- Then use the command `notepad outdated-cache.txt` to view the local DNS cache as shown below. 

  <img src="https://github.com/user-attachments/assets/a13f3bcc-23a9-4d33-b083-19f900759645" height="60%" width="60%" />



---
<br />
<h3>View Local DNS Cache</h3>

- In the text file `outdated-cache.txt` we can see the IP address record for our host name `domain-controller-server` is still pointing to the original IP address (`10.0.0.5`). 

  <img src="https://github.com/user-attachments/assets/d81df949-d7e2-4543-8231-44df0ee3f14c" height="60%" width="60%" />


---
<br />
<h3>Flush DNS (`ipconfig /flushdns`) </h3>

- To force the Windows 10 Pro VM client to retrieve the updated DNS from DNS Server instead of its `local cache` we can use the command `ipconfig /flushdns`.
- On the Windows 10 Pro VM Client open PowerShell.
- Run the command `ipconfig /flushdns` as shown below.
- This will flush the local DNS cache so that the next time we attempt to ping our host name (`domain-controller-server`) it will go to the DNS Server and get the latest IP address information which in this example would be `8.8.8.8`).

  <img src="https://github.com/user-attachments/assets/06a690d5-f340-419f-a90d-27cb57ceee11" height="60%" width="60%" />


---
<br />
<h3>Check The Local DNS Cache After DNS Flush</h3>

- To check the local DNS cache after we flushed the cache, run the command `ipconfig /displaydns`
- Now, the entry for `domain-controller-server` does not exist so it will be forced to go out to the DNS server to get the latest information instead of using an old record in its local cache.
- Notice that the DNS cache is able to find the entry for `host.file.server` that we created in the local `hosts.txt` file above.
- This is because it will check its DNS cache first, then it will check the `hosts.txt` file which is were the record for `host.file.server` resides that we created above.

  <img src="https://github.com/user-attachments/assets/12484479-5bbd-41af-948f-9f91cc2753a5" height="60%" width="60%" />


---
<br />
<h3>Ping The Host Record `domain-controller-server` Again From The Client</h3>

- Now run the command `ping domain-controller-server` from the Windows 10 Pro VM Client.
- It will check the DNS Server to get the updated IP address for `domain-controller-server` which is `8.8.8.8` as shown below.


  <img src="https://github.com/user-attachments/assets/ff71d53e-a1e7-4a15-b0b4-96d56dda82a4" height="60%" width="60%" />


- The local computer checked the local DNS cache because it is fastest. Nothing was in there because we flushed it.
- Next, the local computer will check the local hosts.txt file. There is no entry for `domain-controller-server`.
- So, finally, the local computer will check the DNS server to get the updated IP address of `8.8.8.8`.



<br />
<br />

---

<table>
        <tr>
            <th width="auto">
              <h1>Part 7: Create CNAME Record (`get-help`) in <br /> the Domain Controller</h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/4391ac8b-a4ba-42a5-8707-73f55ac21c8d" height="60%" width="60%" />
            </th>
        </tr>
</table>

<h2>Create a CNAME Record on Windows 2022 Server Domain Controller</h2>

- A CNAME maps one human-readable name to another human-readable name.
- In this example, I will map the host name `get-help` to `www.stackoverflow.com/`.


---
<br />

<h3>DNS Manager > `New Alias (CNAME)`</h3>

- On the Windows 2022 Server VM, open `DNS Manager`.

1. Navigate to `Forward Lookup Zones` > `Domain` (_In this example, IanBates.com_)
2. Right-click and select `New Alias (CNAME)...` as shown below.

  <img src="https://github.com/user-attachments/assets/c71ae113-8fbc-4c9b-a15c-563e56e33cc3" height="90%" width="90%" />


---
<br />

<h3>Add CNAME Information</h3>

1. Enter the name for the CNAME record. Here I used `get-help`
2. Enter the Fully Qualified Domain Name (FQDN) for the target host. Here I went with `www.StackOverflow.com`.
3. Select `OK` as shown below.


  <img src="https://github.com/user-attachments/assets/e9dc8ea5-6d48-43db-8b8d-f6f756b614ad" height="40%" width="40%" />



---
<br />

<h3>View the new CNAME in DNS Manager</h3>

- We can see our new CNAME record listed in the DNS records of the DNS Manager as shown below.

  <img src="https://github.com/user-attachments/assets/ac869fd2-9284-4609-8a07-d9e866955ec8" height="50%" width="50%" />




<br />
<br />

---

<table>
        <tr>
            <th width="auto">
              <h1>Part 8: Examine the new CNAME <br /> (`get-help`) in the Client VM</h1>
            </th>
            <th>
              <img src="https://github.com/user-attachments/assets/252c9ea9-5aaf-4c0a-9f04-758124b08087" height="60%" width="60%" />
            </th>
        </tr>
</table>

<h3>Ping the CNAME from the Windows 10 Pro Client</h3>

- From the Windows 10 Pro Client VM, open PowerShell.
- Ping the CNAME created above by running the command `ping get-help` as shown below.
- We are able to ping the CNAME `get-help` which returns the IP address for the great site, wwww.StackOverflow.com as shown below with 0% packet loss.


  <img src="https://github.com/user-attachments/assets/8c24f2a5-de9b-4507-9717-c8823e60c970" height="50%" width="50%" />



---
<br />

<h3>Use `nslookup` On Our CNAME `get-help`</h3>


- In the PowerShell terminal on the Windows 10 Pro Client VM, run the command `nslookup` our CNAME `get-help`.
- Notice that our CNAME is returned as an Alias as shown below.

  <img src="https://github.com/user-attachments/assets/816470fe-21ac-4182-a444-9518fbcdcb84" height="50%" width="50%" />


---



