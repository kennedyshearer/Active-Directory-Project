# 🤖 Active Directory Project  

## 📌 Project Description  
This project demonstrates setting up an Active Directory (homelab), integrating Splunk, Kali Linux, and Atomic Red Team to explore how a domain environment works while learning how to ingest events to a SIEM and generate telemetry related to attacks in the real-world to help detect in the future.

---

## 🛡️ Skills Gained
✅ **Analyzing security logs** in Splunk.  
✅ **Powershell scripting** for tasks (Set-ExecutionPolicy, Invoke-AtomicTest).  
✅ **Brute force attacks** using crowbar and hydra.  
✅ **Simulate Atomic Tests** mapped to the MITRE ATT&CK with Atomic Red Team (ART).  
✅ **Troubleshooting network** connectivity between servers and workstations.  

---

## 🔧 Tools Used  
| Tool            | Description |
|----------------|------------|
| **Active Directory** | For centralized domain management and user authentication. |
| **Splunk Enterprise**    | For security information and event management (SIEM), used to collect and analyze system logs. |
| **Splunk Universal Forwarder** | For data collection from remote sources to forward into Splunk software for indexing and consolidation. |
| **Sysmon**    | For detailed event logging to support security analysis in Splunk. |
| **Atomic Red Team** | A collection of open-source tools designed for security testing and validation. |
| **Kali Linux** | A penetration platform used to simulate cyber attacks with lab environment. |

---

## 📋 Table of Contents

1️⃣ [Topology](#📈-topology)

2️⃣ [Installation and Setup](#-🛠️-installation-and-setup)
- [VM-1 for Splunk Server](#vm---1-for-splunk-server)
- [VM-2 for Active Directory](#vm---2-for-active-directory)
- [VM-3 for Target-PC](#vm---3-for-target---pc)
- [VM-4 for Attack Machine](#vm---4-for-attack-machine)

3️⃣ [Attack Simulations and Telemetry Generation via Splunk](#⚔️-attack-simulations-and-telemetry-generation-via-splunk)

---

## 📈 Topology  
<p align="center"> <img src="https://github.com/kennedyshearer/Active-Directory-Project/blob/main/ADP-diagram.drawio.png"></p>

---

## 🛠️ Installation and Setup  

### VM-1 for Splunk Server 

**Specifications**

- **RAM:** 8GB+
- **HDD:** 100GB+
- **OS:** Ubuntu 24.04 LTS

### **1️⃣ Setup Static IP Address**
1. **Open up text editor to the file 50-cloud-init.yaml**
   ```bash
   sudo nano /etc/netplan/50-cloud-init.yaml
   ```
   
2. **Modify with new IP address, DNS, and default route(gateway)**
   ```bash
   network:
       ethernets:
           ens33:
               dhcp4: no
               addresses: [192.168.10.10/24]
               nameservers:
                   addresses: [8.8.8.8]
               routes:
                   - to: default
                     via: 192.168.10.2
       version: 2
   ```

3. **Apply new Netplan configuration**
   ```bash
   sudo netplan apply
   ```

4. **Verify network:**
   <p align="center"> <img src="https://i.gyazo.com/eeaae200b4a51e9cf591a50ea17acf81.jpg"></p>
   <br>


### **2️⃣ Install Splunk Enterprise**  
A shared folder was setup to retrieve the Splunk deb. file from the host computer using these steps: 

Follow the official Splunk Enterprise installation guide:  
🔗 [Splunk Enterprise Installation Guide](https://docs.splunk.com/Documentation/Splunk/9.4.1/SearchTutorial/InstallSplunk)  

1. **Update and Upgrade:**
   ```bash
   apt-get update && apt-get upgrade
   ```

2. **Install Splunk Enterprise:**
   ```bash
   dpkg -i splunk-9.4.0-6b4ebe426ca6-linux-amd64.deb
   ```

3. **Change to Splunk user:**
   ```bash
   sudo -u splunk bash
   ```

4. **Navigate to `bin` directory & RUN installer:**
   ```bash
   cd bin
   ./splunk start
   ```
   
5. **Create Splunk Credentials:**
   - **User:** mydfir
   - **Password:** ***************

6. **Navigate to `bin` directory & RUN installer:**
   ```bash
   cd bin
   ./splunk start
   ```

7. **Enable Splunk on boot:** (Ensuring Splunk starts after reboot)
   ```bash
   exit
   cd bin
   sudo ./splunk enable boot-start -user splunk
   ```

8. **Access Splunk Dashboard:**
   - Open your browser and go to: `https://192.168.10.10:8000`

   <p align="center"> <img src="https://i.gyazo.com/30323d51eb8a00ff0594e3cfd3513962.png"></p>
   <br>


### VM-2 for Active Directory 

**Specifications**

- **RAM:** 4GB+
- **HDD:** 50GB+
- **OS:** Windows 2022 (64-bit)

### **1️⃣ Setup Static IP Address**
1. **Navigate to IPv4 properties and modify general settings:**
   
   ![ADDC01](https://i.gyazo.com/3c54f00369d4df4a24591c2db3ae7d45.png)
   

### **2️⃣ Install Sysmon**
Download the Sysmon configuration file (sysmonconfig.xml) from github:  
🔗 [Sysmon Configuration File](https://github.com/olafhartong/sysmon-modular/blob/master/sysmonconfig.xml) 

1. **Extract Sysmon files**
2. **Open Powershell as administator**
3. **Change directory to the path of extracted Sysmon files**
4. **Run ``Sysmon64.exe -i`` on sysmonconfig.xml file:**
   
   ![Sysmon64](https://i.gyazo.com/45163ab1be22686e8bb86688c5e457a9.png)
   

### **3️⃣ Install & Configure Splunk Universal Forwarder**
Follow the official Splunk Universal Forwarder installation guide:  
🔗 [Splunk Universal Forwarder Installation Guide](https://docs.splunk.com/Documentation/Forwarder/9.4.0/Forwarder/InstallaWindowsuniversalforwarderfromaninstaller)  

1. **Run Splunk Universal Forwarder installer:**
   
   ![SplunkUF](https://i.gyazo.com/2b72deed3c8527c0b373dd4e325cc0e0.gif)

2. **Create inputs.conf file for setting up file monitoring input:**
   ```bash
   [WinEventLog://Application]
   index = endpoint
   disabled = false

   [WinEventLog://Security]
   index = endpoint
   disabled = false

   [WinEventLog://System]
   index = endpoint
   disabled = false

   [WinEventLog://Microsoft-Windows-Sysmon/Operational]
   index = endpoint
   disabled = false
   renderXml = true
   source = XmlWinEventLog:Microsoft-Windows-Sysmon/Operational
   ```
   
3. **Save inputs.conf file under local directory:**
   ```bash
   C:\Program Files\SplunkUniversalForwarder\etc\system\local
   ```
   
4. **Change Splunk "Log on as" setting to Local System account:**

   ![WinServices](https://i.gyazo.com/42c164a68e91b71fe3ad062f5685cd18.png)

5. **Restart Splunk Universal Forwarder service**

6. **Setup Splunk to receive data from Universal Forwarder:**
   - Create endpoint index: 
     
   <p align="center"> <img src="https://i.gyazo.com/05bf4795b4871b4860479f6f72be32df.gif"></p>
   <br>

   - Configure receiving data: 
     
   <p align="center"> <img src="https://i.gyazo.com/11e54bf5e1729ad19ffcbf10d70f47b0.gif"></p>
   <br>

 
 ### **4️⃣ Create Domain Controller**
1. **Install Active Directory Domain Services:**

   <p align="center"> <img src="https://i.gyazo.com/930ceaf569b4aca5087a041f0d719dc6.gif"></p>
   <br>

2. **Promote server to a domain controller:**

   <p align="center"> <img src="https://i.gyazo.com/88d67712159f2a518191f9e5d37e45d2.gif"></p>
   <br>

3. **Restart Active Directory:**
   - Successfully installed Active Directory Domain Services and promoted server to a domain controller:

   ![mydfirDC](https://i.gyazo.com/e9f73c24be35cfbd91a9d29180004a98.png)
   <br>


### **5️⃣ Create Multiple Users**
1. **Create an organizational unit (OU)**
2. **Create users and assign passwords to each**
   
   <p align="center"> <img src="https://i.gyazo.com/0593b27ba6274101d70e2e70fbc5c1d4.gif"></p>
   <br>
   

### VM-3 for Target-PC 

**Specifications**

- **RAM:** 4GB+
- **HDD:** 50GB+
- **OS:** Windows 10 (64-bit)

### **1️⃣ Setup Static IP Address**
1. **Navigate to IPv4 properties and modify general settings**
   
   ![targetPC](https://i.gyazo.com/b7bfc9fcee64c6bb66c8a1d895cbfccb.png)


### **2️⃣ Install Sysmon**
Follow these steps for [Sysmon installation](#2️⃣-install-sysmon)


### **3️⃣ Install Splunk Universal Forwarder**
Follow steps 1-5 for [Splunk Universal Forwarder installation](#3️⃣-install--configure-splunk-universal-forwarder)


### **4️⃣ Setup Atomic Red Team(ART)**
1. **Open `PowerShell` as Administrator**
2. **Temporarily change the script execution policy for current user:**
   ```bash
   PS C:\Windows\system32> Set-ExecutionPolicy Bypass CurrentUser
   ```

3. **Set exclusion for C:\ in Windows Defender settings to prevent it from removing files from ART after install:**

   ![exclusion](https://i.gyazo.com/07e66cdf65173dcfcf5342fd066c18d2.png)

4. **Install Atomic Red Team**
   ```bash
   PS C:\Windows\system32> IEX (IWR 'https://raw.githubusercontent.com/redcanaryco/invoke-atomicredteam/master/install-atomicredteam.ps1' -UseBasicParsing);
   PS C:\Windows\system32> Install-AtomicRedTeam -getAtomics
   ```

### **5️⃣ Join Domain Controller**
1. **Navigate to `Advanced system settings` under PC > properties**
2. **Select Change**
3. **Set domain to `mydfir.local`**

   <p align="center"> <img src="https://i.gyazo.com/8998c20703e5398478ec090a399ba7a2.gif"></p>
   <br>

4. **Test logging into an account created on the AD server:**

   <p align="center"> <img src="https://i.gyazo.com/b97eded2a46bb27cc542b47fb5f5c58c.gif"></p>
   <br>


### **6️⃣ Enable Remote Desktop Protocol (RDP)**
1. **Navigate to PC properties**
2. **Select `Advanced system settings`**
3. **Under the Remote tab, select `Allow remote connections to this computer`**
4. **Add two users created in Active Directory Domain Controller**

   <p align="center"> <img src="https://i.gyazo.com/59f7c309dd4705512775429fdafc7572.gif"></p>
   <br>


### VM-4 for Attack Machine

**Specifications**

- **RAM:** 4GB+
- **HDD:** 50GB+
- **OS:** Kali Linux 2024.4

### **1️⃣ Setup Static IP Address**
1. **Navigate to Wired connection IPv4 settings and modify accordingly**

   ![kali](https://i.gyazo.com/a63ac3e5400c249ebfa40a1386b528d1.png)


### **2️⃣ Install and Setup Tools**
1. **Update and Upgrade:**
   ```bash
   apt-get update && apt-get upgrade
   ```

2. **Install crowbar (brute force tool):**
   ```bash
   sudo apt-get install -y crowbar
   ```
   
3. **Create a folder to save tools used for attack:**
   ```bash
   mkdir AD-project
   ```
   
5. **Find, unzip, and copy rockyou.txt wordlist to AD-Project:**
   ```bash
   cd /usr/share/wordlists/
   sudo gunzip rockyou.txt.gz
   cp rockyou.txt ~/Desktop/AD_project
   ```

6. **Change to AD-project and create a smaller file with the first 20 lines of rockyou.txt:**
   ```bash
   cd ~/Desktop/AD_project
   head -n 20 rockyou.txt > passwords.txt
   ```

7. **Add the `MAGIC` password to the new wordlist; passwords.txt:**
   ```bash
   echo "China123" >> passwords.txt
   ```
   <br>
   

## ⚔️ Attack Simulations and Telemetry Generation via Splunk

### **1️⃣ Kali Linux Attack**
1. **Execute crowbar using Kevin's account and passwords.txt wordlist:**
   ```bash
   crowbar -b rdp -u kstrickland -C passwords.txt -s 192.168.10.100/32
   ```
   <p align="center"> <img src="https://i.gyazo.com/73e83c0536e5e1c7ebdf3414885551aa.png"></p>
   <br>

2. **Search for events related to Kevin's account on the Splunk dashboard:**
   ```bash
   index=endpoint kstrickland
   ```

3. **Investigate findings:**
   - Found EventCode 4625(20 counts) and 4624:
     
   ![EventCode](https://i.gyazo.com/23009daac2026eae089c612e9491aee2.png)

   - A deeper search revealed that EventCode 4625 are "failed login attempts":
     ```bash
     index=endpoint kstrickland EventCode=4625
     ```

     ![failed](https://i.gyazo.com/ba6153af2483aadb4764e8b55fb4de20.png)

   - While EventCode 4625 is a "succesful login attempt":
     ```bash
     index=endpoint kstrickland EventCode=4624
     ```

     ![success](https://i.gyazo.com/af9c6de6ec630c0f052f06377645264e.png)

   - Proof of event source:

     ![kaliEvent](https://i.gyazo.com/5b79c2b3776a65e0b5e90be6cdcfab4a.png)
     

### **2️⃣ Atomic Red Team Test**
1. **Choose an Atomic Test, in correspondence to MITRE ATT&CK Matrix:**
   - First tactic/technique will be Persistence --> Create Account --> Local Account; ID = T1136.001
     ```bash
     PS C:\Windows\system32> Invoke-AtomicTest T1136.001
     ```
     
     ![LocalAccount](https://i.gyazo.com/fed106237da34f1fdbe82217a4a4425e.png)

2. **Search for logs related to NewLocalUser created:**
   ```bash
     index=endpoint NewLocalUser
   ```

   ![NewLocalUser](https://i.gyazo.com/784f99c4d7bb9446fa9775247b487c41.png)

