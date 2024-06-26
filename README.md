<p align="center">
<img src="https://i.imgur.com/pU5A58S.png" alt="Microsoft Active Directory Logo"/>
</p>

<h1>Configuring On-premises Active Directory Deployed in the Cloud (Azure)</h1>
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
<img width="758" alt="Screenshot 2024-06-25 at 2 55 09 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/f78772ec-bd8b-4ebd-bf8a-a1cc66cd845f">


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

  - Right click on jane doe -> Properties -> Member Of -> Add... -> Type "domain" -> Check Names -> Select "Domain Admins" -> Click OK until "Apply" is an option -> Apply -> OK -> jane doe is now setup as an admin. 
   <p>
     <img width="752" alt="Screenshot 2024-06-25 at 3 12 19 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/72ddca80-a92f-47de-902f-45f8b74895a4">
<img width="409" alt="Screenshot 2024-06-25 at 3 12 28 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/2bb7c33f-2f07-4787-ba51-b87a3f52ddcc">
<img width="408" alt="Screenshot 2024-06-25 at 3 13 00 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/c0851e06-a832-4be9-a8a8-c7eb2fbdef34">
<img width="458" alt="Screenshot 2024-06-25 at 3 13 41 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/077cde78-e954-4ecf-ae32-8df9106fe51d">
<img width="570" alt="Screenshot 2024-06-25 at 3 13 58 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/0733efc5-bc5d-4750-80e3-5efba5818c28">
<img width="411" alt="Screenshot 2024-06-25 at 3 14 16 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/31cb7ff6-cf3c-4ad9-b0f9-22918d77fd7b">
<p>

  - Log out of DC-1 VM (Virtual Machine) -> Log back in with mydomain.com\jane_admin and the password setup earlier when jane was created. 
<p>
<img width="433" alt="Screenshot 2024-06-25 at 3 22 20 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/83c68cc7-c021-4e25-afee-f432d5c19ba5">
<p>

  - Congratulations! We've completed step 4!

    <p>

    <h3>Step 5: Join Client-1 to your domain (mydomain.com)</h3>
      <p>

  - Within Azure -> Copy DC-1 Private IP address -> Client-1 Network Settings -> Network interface -> DNS servers -> Select "Custom" under "DNS servers" -> Paste DC-1's private IP address (make sure there are no spaces around it) -> Save -> Restart Client-1
 <p>
<img width="1407" alt="Screenshot 2024-06-25 at 3 33 08 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/36efbd31-193a-4fe7-90af-38417a8c5fa5">
<img width="277" alt="Screenshot 2024-06-25 at 3 34 17 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/0e3335e8-cc38-4205-b882-ab83d0b29837">

 <img width="789" alt="Screenshot 2024-06-25 at 3 34 27 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/e529fefa-5de7-4867-85da-3b2e41ea0323">
<img width="356" alt="Screenshot 2024-06-25 at 3 35 24 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/177e3b7e-fb91-462d-a906-162b8461cec1">
<img width="585" alt="Screenshot 2024-06-25 at 3 37 30 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/e447e98a-bf40-4da3-ae5d-91a42a81998f">
<img width="605" alt="Screenshot 2024-06-25 at 3 38 43 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/717645a8-8ef9-44ed-93f3-a9cfbeea111c">

 <p>
   
  - Log into Client-1 VM -> Right click on Start Menu -> Select "System" -> Rename this PC (advanced) -> Change... -> Select "Domain" under "Member of" and type "mydomain.com" -> OK -> Input jane_admin information -> Restart computer through prompts

    <p>
<img width="307" alt="Screenshot 2024-06-25 at 3 28 46 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/e58484d1-95bd-4e17-a514-f2e1bc263481">
<img width="1264" alt="Screenshot 2024-06-25 at 3 29 45 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/02785026-e3bc-4d07-b747-a629d5577523">
<img width="411" alt="Screenshot 2024-06-25 at 3 30 35 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/a087138f-4dea-4702-a1ee-9fbfc54a2e55">

<img width="320" alt="Screenshot 2024-06-25 at 3 48 03 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/25557bde-ca55-4bde-ae46-85707620c784">
<img width="456" alt="Screenshot 2024-06-25 at 3 48 49 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/5acba9c6-a17a-4362-9016-912cec078cb5">
<img width="351" alt="Screenshot 2024-06-25 at 3 50 38 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/3e298f07-c460-439e-a088-8a8ad4e17872">
<p>

  - Congratulations! We've completed step 5!

  <p>

<h3>Step 6: Setup Remote Desktop for non-administrative users on Client-1</h3>
 
  - Log into Client-1 VM using jane_admin login -> Right click on Start Menu -> System -> Remote Desktop -> Select users that can remotely access this PC -> Add... -> Type "domain users" -> Check Names -> OK through the menus
    <p>

    <img width="302" alt="Screenshot 2024-06-25 at 3 57 56 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/064de821-0241-4986-881b-e5278148184c">

    <img width="931" alt="Screenshot 2024-06-25 at 3 58 16 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/a74bfae6-c2c6-4694-b802-a4318805f34f">
<img width="579" alt="Screenshot 2024-06-25 at 3 58 29 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/dfb121f4-ce34-4f2e-b68b-feffb766804f">
<img width="371" alt="Screenshot 2024-06-25 at 3 58 43 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/ec3455de-c3bd-466d-b37c-3c68239e2b45">
<img width="454" alt="Screenshot 2024-06-25 at 3 58 54 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/627b7982-7a8d-46eb-8b89-00bf2641fde0">
<img width="456" alt="Screenshot 2024-06-25 at 3 59 00 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/3cca3ab7-7b21-4780-8abf-63874aca23e6">
<p>

- Congratulations! We've completed step 6!

  <p>

  <h3>Step 7: Create additional users and attempt to log into client-1 with one of the users</h3>

<p>

  - Log into DC-1 as jane_admin (if not already) -> Type "powershell_ise" in search bar -> Right click on it and select "Run as administrator"
    <p>
    <img width="831" alt="Screenshot 2024-06-25 at 4 09 11 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/76f070f8-48a9-4af3-b3fd-13ad3902f6eb">

<p>

  - Create new script -> Copy the code in this link: https://github.com/joshmadakor1/AD_PS/blob/master/Generate-Names-Create-Users.ps1 -> Paste code into file -> Adjust number of accounts to create to "1000" (It can be adjusted to however many desired). -> Run script 
<p>
 
<img width="299" alt="Screenshot 2024-06-25 at 4 17 03 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/068f1476-b641-48cc-8e77-b70213370288">
<img width="1082" alt="Screenshot 2024-06-25 at 4 22 30 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/d880510c-a818-4055-888b-8e6cec29472d">

<img width="810" alt="Screenshot 2024-06-25 at 4 18 45 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/27fe1739-5093-4e8b-9309-8428640ac286">
<img width="741" alt="Screenshot 2024-06-25 at 4 19 48 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/68dca98b-4ae3-4a77-bf43-30526a3a9347">
<p>

  - Open Active Directory Users and Computers again -> _EMPLOYEES -> Choose any of the made employees -> Double click on them and copy their Display name
    <p>
    <img width="831" alt="Screenshot 2024-06-25 at 4 23 35 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/3f5fb80a-927d-4110-bcb7-c034f2e1df86">
<img width="918" alt="Screenshot 2024-06-25 at 4 25 02 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/d8bedfb4-0739-473a-b6e8-f007ebd0898d">
<img width="407" alt="Screenshot 2024-06-25 at 4 25 37 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/87868aa1-cc1d-4f63-bf59-83833275443e">

<p>

  - Log into Client-1 using employee's display name and "Password1" for the password (in the code that is the password that was set for all of them)

    <p>
<img width="433" alt="Screenshot 2024-06-25 at 4 28 10 PM" src="https://github.com/samparkercc/Configuring-On-premises-Active-Directory-within-Azure-VMs/assets/171518500/bca3ab39-5d0b-45e0-a0a0-ab55ffdb0b68">
<p>

  - If successfully logged in - Congratulations! We've not only completed step 7, but also the this lab! Great job! 
    
                  
