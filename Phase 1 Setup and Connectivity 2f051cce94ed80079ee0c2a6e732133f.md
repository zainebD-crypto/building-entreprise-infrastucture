# Phase 1 :Setup and Connectivity

---

## **Tasks:**

- Download ISOs(OPNsense,Windows 10 or 11 and Windows Server 2020)
- Configure VMs
- Install OS
- verify connectivity

## **1-Download ISOs**

first of all the win server 2022 by their link

- [https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022](https://www.microsoft.com/en-us/evalcenter/download-windows-server-2022)

we choose the English(United States) ISO downloads 

![image.png](image.png)

Secondly  OPNsense: 

- [https://opnsense.org/download/](https://opnsense.org/download/)

select the dvd image(ISO) and download

![image.png](image%201.png)

and finally the win10 installation media

- [https://www.microsoft.com/en-us/software-download/windows10](https://www.microsoft.com/en-us/software-download/windows10)

![image.png](image%202.png)

## **2-Configure VMs**

for windows 10(client ):

![image.png](image%203.png)

       for win10 server (DC)

![image.png](image%204.png)

![image.png](image%205.png)

![image.png](image%206.png)

and network setting for both of them so the connectivity work(ping)

we create new host-only-network for both of dc and client 

![image.png](image%207.png)

and in the network settings we put 

![image.png](image%208.png)

the host ip : **`192.168.77.1`** as we need it for later on

<aside>
<img src="https://www.notion.so/icons/info-alternate_green.svg" alt="https://www.notion.so/icons/info-alternate_green.svg" width="40px" />

make sure to disable the Unattended Installation so nothing goes wrong with installation

![Screenshot 2026-01-31 130501.png](Screenshot_2026-01-31_130501.png)

</aside>

<aside>
<img src="https://www.notion.so/icons/info-alternate_green.svg" alt="https://www.notion.so/icons/info-alternate_green.svg" width="40px" />

</aside>

### A-setup DC

in the installation process make sure to choose **Windows Server 2022 Standard Evaluation(Desktop Experience)** so we will get the server manager 

![image.png](image%209.png)

for now Windows server account : **`*Administrator:password123!*`**

first of all in the Windows server **Control Panel\Network and Internet\Network Connections properties of** Ethernet **** 

![image.png](image%2010.png)

disable IPv6 and go to IPv4 Properties 

![image.png](image%2011.png)

as we have host ip is **`192.168.77.1`**  we use **`192.168.77.3`**  as static ip for the DC and the **Loopback address as Preferred DNS server(broadcast address `192.168.77.255`)**

![image.png](image%2012.png)

in system proprties change the name of the Windows server computre to DC_server

![image.png](image%2013.png)

### B-setup client

account for the client : **`*client:password123!*`**

we set up static ip for the client **`192.168.77.101`**  and default gateway **`192.168.77.1`**  and Preferred DNS server **`192.168.77.3` (DC IP)**

![image.png](image%2014.png)

## 3-verify connectivity

### A-ping each

we try to ping the dc from the client :

![image.png](image%2015.png)

same for ping client from dc

all of this due to windows firewall so gonna we enable File and Printer sharing (Echo Request - ICMPv4-In) both **Domain** and **Private** profile in the DC and client 

![image.png](image%2016.png)

and finally both of them can ping each others 

![image.png](image%2017.png)

![image.png](image%2018.png)

### B-Resolve DNS

- trying to ping **Windows server** computre name :

![image.png](image%2019.png)

for the next step we’ll set up domain name on the **Windows server** and figure out to resolve dns from the client computer  so we’ll promote **Windows server** to **Domain controller**

in the server manager Dashboard click add roles and features

![image.png](image%2020.png)

next next then 

![image.png](image%2021.png)

check both of **Active Directory Domain Services** and **DNS Server and add them as feature** 

![image.png](image%2022.png)

make sure the group policy Mangement is checked 

![image.png](image%2023.png)

then next next and install

![image.png](image%2024.png)

and after the installation finished 

![image.png](image%2025.png)

and then promote this server to a domain controller so we set up domain name 

- add new forest
- set the Root domain name to **securinetsenit.local**

![image.png](image%2026.png)

next we set up Domain controller Options 

![image.png](image%2027.png)

as the passowrd for DSRM is **`fr0ma4arw$p6y`**

![image.png](image%2028.png)

just dont :”) 

and the NetBIOS domain name as **`SECURINETSENIT`**

![image.png](image%2029.png)

next next then install 

![image.png](image%2030.png)

after quick restart we go to server manager\dashboard then tools \Active Directory Users and Computers

![image.png](image%2031.png)

we will try to add the client computre to the computers folder in the forest sercurinets.local

![image.png](image%2032.png)

in the client we head torward settings\about the scroll down for Advance system settings

![image.png](image%2033.png)

then Computer Name,change,check Domain and set name to our DC domain name **securinetsenit.local** 

![image.png](image%2034.png)

<aside>
<img src="https://www.notion.so/icons/info-alternate_green.svg" alt="https://www.notion.so/icons/info-alternate_green.svg" width="40px" />

make sure the **Preferred DNS server** is set exactly **`192.168.77.3`** in the client or we will face issue like this

![image.png](image%2035.png)

</aside>

put the username and password of the Admin DC

![image.png](image%2036.png)

![image.png](image%2037.png)

everything is going great 

now in the DC the client will apear in the computers forest 

![image.png](image%2038.png)

then we test `**nslockup** **securinetsenit.local`**  and **`ping securinetsenit.local`** in client(after restart) and we got their IP address in return that mean the connectivity is fully working

![image.png](image%2039.png)

![image.png](image%2040.png)

---