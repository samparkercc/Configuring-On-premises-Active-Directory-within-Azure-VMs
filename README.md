<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>On-premises Active Directory Deployed in the Cloud (Azure)</h1>
This tutorial outlines the implementation of on-premises Active Directory within Azure Virtual Machines.<br />



<h2>Environments and Technologies Used</h2>

- Microsoft Azure (Virtual Machines/Compute)
- Remote Desktop
- Active Directory Domain Services
- PowerShell

<h2>Operating Systems Used </h2>

- Windows Server 2022
- Windows 10 (21H2)

<h2>High-Level Deployment and Configuration Steps</h2>

- Step 1: Setup Virtual Machines within Azure 
- Step 2: Ensure Connectivity between the client and Domain Controller
- Step 3: Install Active Directory
- Step 4: Create an Admin and Normal User Account in AD
- Step 5: Join Client-1 to your domain (mydomain.com)
- Step 6: Setup Remote Desktop for non-administrative users on Client-1
- Step 7: Create additional users and attempt to log into client-1 with one of the users

<h2>Deployment and Configuration Steps</h2>

<p>
<h3>Step 1: Setup Virtual Machines within Azure</h3>
<p>
  
  - Resource group -> Create new -> "AD-Lab" (AD = Active Directory)
  
  - Virtual machine name -> "DC-1" (DC = Domain Controller)
    
  - Image -> Windows Server (any windows server should suffice)
    
  - Size -> Standard 2vcpus (at least 2vcpus, for speed)

  - Create virtual machine
  <p>
  <img width="846" alt="Screenshot 2024-06-18 at 2 00 09 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/a98cc52d-62d4-4353-b767-457402a04e2b">
<img width="777" alt="Screenshot 2024-06-18 at 2 01 25 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/9ec63591-259c-4f64-91ac-b7381e011be3">
<p>

  - Resource group -> Select, "AD-Lab"

  - Virtual Machine name -> "Client-1"

  - Image -> Windows 10 Pro

  - Size -> Standard 2vcpus

  - Licensing (bottom of Basics page) -> Check box next to, "I confirm I have an eligible Windows...etc."

  - Select, "Networking" (third option to the right of "Basics" page)

  - Virtual Network -> Select "DC-1-vnet." (choose the virtual network we created with the last virtual machine)

  - Create virtual machine
<p>
    <img width="790" alt="Screenshot 2024-06-18 at 2 14 56 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/f4261765-b763-45a5-9ae6-05b41df18390">
<img width="765" alt="Screenshot 2024-06-18 at 2 15 06 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/4f83a893-b6d9-4950-af34-74b930ff1b7c">
<img width="452" alt="Screenshot 2024-06-18 at 2 15 17 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/519eb97c-682c-4c73-ad26-529471bd68b0">
<img width="790" alt="Screenshot 2024-06-18 at 2 20 40 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/278e3f0f-4dac-46bf-a7dd-c306555d535a">
<p>
  
  - Virtual machines -> DC-1 -> Networking -> Network Settings -> Network interface/IP Configuration 
  <p>
  <img width="633" alt="Screenshot 2024-06-18 at 2 29 35 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/bbbdf30a-5875-4dd4-b3c9-eb2901cf48a1">
<img width="990" alt="Screenshot 2024-06-18 at 2 30 27 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/88834c2b-1424-45f9-9377-c9b741ec805f">
<p>

  - Settings -> IP Configuration -> ipconfig1 -> Allocation -> Set to, "Static" (this will keep the IP address from changing) -> Save
<p>
<img width="1350" alt="Screenshot 2024-06-18 at 2 31 15 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/815630b3-8b66-44b0-9f5d-f2719841adeb">
<img width="581" alt="Screenshot 2024-06-18 at 2 31 27 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/83706cab-7f74-4152-a013-01bf0882e6b1">
<p>

  - Now double check to see if both virtual machines are within the same virtual network.

  - If they are in the same network, great job! We completed step 1! 
<p>
<img width="1415" alt="Screenshot 2024-06-18 at 3 03 42 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/00b11419-e58b-4de1-b92a-547d88d094a4">
<img width="1415" alt="Screenshot 2024-06-18 at 3 03 50 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/d2450ce9-9b9b-47f5-8cd9-a4a7a163b19c">
<p>
<h3>Step 2: Ensure Connectivity between the client and Domain Controller</h3>
<p>

  - Use remote desktop to access each virtual machine

  - Copy public IP address -> Remote Desktop -> Sign in with credentials setup when creating the virtual machines
<p>
    <img width="1415" alt="Screenshot 2024-06-18 at 3 03 42 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/5cc50ddd-682f-4d9f-b93b-67b315e667b0">
<img width="1415" alt="Screenshot 2024-06-18 at 3 03 50 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/585daa4d-5b94-460f-a628-aff78333006c">
<p>

  - Once successfully accessed the virtual machines, then go to DC-1 and copy the private IP address
<p>
<img width="1415" alt="Screenshot 2024-06-18 at 3 24 17 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/965130f0-2cf9-4393-8a24-f62f30757459">
<p>

  - Go to Client-1 -> Open Command Prompt -> Type, "ping -t (paste DC-1's private IP address)"
<p>
    <img width="978" alt="Screenshot 2024-06-18 at 3 29 49 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/cdd21ada-515a-41a2-8f5f-5b513230ea61">
<p>

  - The request will time out because DC-1's firewall is still up.

<p>
<img width="978" alt="Screenshot 2024-06-18 at 3 32 37 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/68fa69eb-3ccd-4fe4-9645-dade6179dcda">
<p>

  - DC-1 -> Type, "wf.msc" in search bar (wf = Windows Firewall) 

  - Click on, "wf.msc"
<p>
<img width="833" alt="Screenshot 2024-06-18 at 3 34 47 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/1677a8a1-2a05-46ce-9a86-bb02889bc8f9">
<p>

  - Inbound Rules -> Select and enable:

        Core Networking Diagnostics - Domain (under Profile) - ICMPv4 (under Protocal)
        Core Networking Diagnostics - Private (under Profile) - ICMPv4 (under Protocal)
<p>
<img width="1436" alt="Screenshot 2024-06-18 at 3 40 00 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/48cbcab9-83cd-4f40-840f-b27f349adc5a">
<img width="1069" alt="Screenshot 2024-06-18 at 3 40 15 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/06baaa0b-ba5e-4233-8b47-f4fde818c8d8">
<img width="1072" alt="Screenshot 2024-06-18 at 3 40 26 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/bd1bd2a2-e225-42f9-aa9b-a348645fa10d">
<p>

  - After this, return to Client-1 and ping should work now.
<p>
    <img width="974" alt="Screenshot 2024-06-18 at 3 40 37 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/f4520b6d-d75c-4a80-8ac4-29b41156f033">
<p>

  - Press, "Control -> C" then it should stop.
<p>
<img width="974" alt="Screenshot 2024-06-18 at 3 49 58 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/b18d9e8c-b0aa-4ae1-9c3f-14cb0a11ec1f">
<p>

Great job! We have completed Step 2! 
<p>
<h3>Step 3: Install Active Directory</h3>
<p>

  - Go to Server Manager -> Add roles and features -> Go through install wizard choosing "Next" until you arrive at "Server Roles"

  - Select, "Active Directory Domain Services" -> Add Features -> Continue through install wizard choosing "Next" -> Install at last page
<p>
    
 
<img width="784" alt="Screenshot 2024-06-18 at 3 57 02 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/0391aefc-91ce-4a9b-b789-3d45d9f02610">
<img width="414" alt="Screenshot 2024-06-18 at 3 56 55 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/100080c7-dad4-4840-b893-dca0b5c65fe3">


  - Flag within Server Manager -> Promote this server to a domain controller -> Add a new forest -> Put mydomain.com as root domain name -> Setup password -> Go through install wizard -> Install
<p>
<img width="1440" alt="Screenshot 2024-06-18 at 4 03 59 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/4e990152-1b5a-4056-bf6a-087dac04725c">


<p>

  - Once kicked out of virtual machine, log back into it using the root domain name you setup and your username. For example, "mydomain.com\labuser" for username and then the password you created 
    <p>
    <img width="679" alt="Screenshot 2024-06-18 at 4 08 27 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/917355e6-6f61-48fb-a74b-e4bff35a07e7">
    <img width="431" alt="Screenshot 2024-06-18 at 4 18 59 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/0a23f829-b082-460e-b0dc-87beec67da56">

<p>

  - Once logged into server manager, congratulations! We've completed Step 3!
    <p>
    <h3>Step 4: Create an Admin and Normal User Account in AD</h3>
    <p>

    - Type, "active directory users and computers" in search bar -> Active Directory Users and Computers
    <p>
      <img width="828" alt="Screenshot 2024-06-18 at 4 24 02 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/e282aa01-42b7-4acf-bbcc-5df648e94155">
<p>

  - Right click on, "mydomain.com" (or whatever name you used for it) -> New -> Organizational Unit -> "_EMPLOYEES"

  - Repeat steps and name it "_ADMINS"
<img width="690" alt="Screenshot 2024-06-18 at 4 24 53 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/80beae03-da65-4fe4-9cf6-219bd9afa7e0">
<img width="436" alt="Screenshot 2024-06-18 at 4 25 17 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/4896024b-f51b-491b-bc20-740060fd434b">
<img width="435" alt="Screenshot 2024-06-18 at 4 25 44 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/6a4b9f23-8d4b-457b-ae06-d82292de76fb">
<p>

  - Go to _ADMINS -> New -> User -> jane (first name) doe (last name) -> jane_admin (username) -> Set password -> Make sure the only box checked is, "Password never expires"
<p>
<img width="734" alt="Screenshot 2024-06-18 at 4 33 33 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/32c29cea-9445-416f-b85b-53f898111efa">
<img width="435" alt="Screenshot 2024-06-18 at 4 33 54 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/59caf681-fb4c-460e-8fb9-0af4faff9b96">
<img width="434" alt="Screenshot 2024-06-18 at 4 34 23 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/3d4fa27a-f846-4ee2-8919-a4cfa6166d5f">
<p>

  - 
    
    
