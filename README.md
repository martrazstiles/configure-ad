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

- Step1: Create VM for Domain controller(DC) and client-1. 
- Step 2: Configure DC private IP address to static, set client-1 DNS setting to DC private IP address
- Step 3: Install Active Directory, create Domain, configure for Domain admin and users.
- Step 4: Setup remote desktop for non-admin users on client-1 and attempt connections.
<h2>Lab Visual Diagram</h2>
<P>
  <img width="548" alt="image" src="https://github.com/user-attachments/assets/9653c620-73bd-4805-be60-734e519b9a65" />
</P>
<h2>Virtual Machine Prerequisets</h2>

- Make sure both VM are in same virtual network not default network
- Both in the same region (more efficient)
- Make sure one has a server image for operating system, this will be DC-1

 
<h2>Deployment and Configuration Steps</h2>

<p>
<img width="929" alt="image" src="https://github.com/user-attachments/assets/839c63d4-f634-4e3d-af9b-8e3266e5b035" />
</p>
<p>
After we create our virtual machines, we need to set DC-1 server's ip address as "static" meaning it wont change. For this we would: click on virtual machines inside azure -> choose server (DC-1) -> Network settings -> IP configurations -> ipconfig1 -> static. (NOTE: in order for this test to run smoothly we will have to disable microsoft firewall by logging into DC-1).
</p>
<br />

<p>
<img width="675" alt="image" src="https://github.com/user-attachments/assets/075a7dc9-87d4-4410-8865-45a4d4785ccf" />
<img width="537" alt="image" src="https://github.com/user-attachments/assets/cf28d994-ae2f-46b4-b01c-29d410864785" />
</p>
<p>
In order to change the DNS server for client-1 we need to go to our virtual machines and click on client-1. From here we: click on Network settings -> DNS servers (under settings option) -> custom -> enter private address of DC-1 (for this example). This will reroute traffic to DC-1 instead of the default gateway given by azure. To confirm this we can log into client-1 and execute the command [ipconfig /all] and [ping 10.0.0.4] on powershell, in order to confirm the DNS configuration and connection.
</p>
<br />

<p>
<img width="728" alt="image" src="https://github.com/user-attachments/assets/51957803-4e27-48a2-9545-db88f5379e0a" />
<img width="926" alt="image" src="https://github.com/user-attachments/assets/19c8d62b-5562-478f-b790-a986699caf63" />
<img width="299" alt="image" src="https://github.com/user-attachments/assets/9b968918-69ed-45fa-97a3-02e8927a65d2" />

</p>
<p>
In order to make DC-1 into a domain controller, we need to go to server manager and click on add roles and services. From here we caan click next until we reach the page shown in the first image (server roles) and select "Active Directory Domain Services". We can then click next until given to the option to install. After installation we can click on the flag show in image two ( upper right of page) and configure our domain name. Click Deployment Configuration -> Add new forest -> enter root domain name.You can then click next until installation. After this is completed your VM will restart prompting you to enter your information again. This time we will put our domian name to specify where we want to log in, in this case we would log in as "mydomain\Kev" to enter in that domain as that user.
</p>
<p>
  <img width="800" alt="image" src="https://github.com/user-attachments/assets/e9d2f78f-e97e-48b2-8a8c-51adcab07966" />
<img width="698" alt="image" src="https://github.com/user-attachments/assets/b4df4a58-5ef7-4217-b0d1-aa6703b045a2" />
<img width="820" alt="image" src="https://github.com/user-attachments/assets/5609d043-e2a3-4b56-8b57-199483032a11" />
</p>
<P>
  We now want to create a two folders, one for employees and another for admins, and create a domian admin as well. For the folders we can click the search bar and enter Active Directory Users and Computers and click on it -> right click on "mydomain.com" -> click new -> Organizational Unit -> enter file names ( in this case we create two, one for _EMPLOYEES and _ADMINS). Now in order to create a domain admin we can right click the _ADMINS folder -> New -> User. Fill out the information, in this case I created a domian admin named "Jane_admin", you will be clicked next then prompted for a password. Once completed, in order to make this user a domain admin we need to right click on the user ( in this case Jane Doe) -> properties -> Member of -> add -> enter "Domain Admins" -> check names -> ok -> apply then ok. This will add Jane doe as part of the Domain Admins group officially.
</P>
<p>
  <img width="937" alt="image" src="https://github.com/user-attachments/assets/05ca39e0-ec39-4a39-b8ee-85e907cc4ff1" />
<img width="595" alt="image" src="https://github.com/user-attachments/assets/01447dc2-4bec-4521-9c66-b291ecb1b7f0" />
</p>
<p>
  Next thing we want to do is add Client-1 to our domian to do so we log into it with our first user ( in my case it was "labuser"), go into settings -> Rename this PC (advanced) -> change -> click on Domain, enter domain ( in this case: mydomain.com) -> ok. This will add Client-1 to the mydomain.com but to make sure we will go back to the Domain controller. From here we can search for Active Directory Users and Computers -> click on Computers folder -> Client-1 should be present.
</p>
<p>
  <img width="907" alt="image" src="https://github.com/user-attachments/assets/55578047-c3c0-424d-b3e1-8d58da78d882" />
</p>
<p>
  In this part we want to give remote access to Client-1 to all users in the domain. To do so, we must log in as the admin (in this case Jane_admin) into Client-1. From here we can go to settings -> setting up remote desktop (right side of page) -> Select users that can remotely access this PC -> add "Domain users" -> ok. Now we can go create some users that will be able to access Client-1 remotely.
</p>
<p>
  <img width="829" alt="image" src="https://github.com/user-attachments/assets/35d5e7c0-ed11-4d8b-a89a-40019cf1f756" />
<img width="932" alt="image" src="https://github.com/user-attachments/assets/c81d8939-1b2c-470d-a853-26a708bc4135" />
</p>
<p>
  To create users we can use a script as show in this example. To do so we go to DC-1 as admin -> open Powershell ISE -> File -> New -> paste script into command line -> run script. If executed correctly the command line should generate users but to cofnirm we can head over to Active Directory Users and Computers -> _EMPLOYEES foler. There should be users in the folder. Note: In this script given, it generated 100 accounts and the password for all of them is "Password1" and they would be imported into the file _EMPLOYEES which is important to have the correct name. Lastly we want to see if we are able to login in into Client-1 with one of these users, to do so we just get the user name login in via Remote Desktop. A example would be : "mydomain.com\big.sapir" as the username and "Password1" for the password.
</p>
<br />
