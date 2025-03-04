# 🤖 Active Directory Project  

## 📌 Project Description  
This project demonstrates setting up an Active Driectory(homelab), integrating Splunk, Kali Linux, and Atomic Red Team to explore how a domain environment works while learning how to ingest events to a SIEM and generate telemetry related to attacks in the real-world to help detect in the future.

✅ **Analyzing security logs** in Splunk.  
✅ **Powershell scripting** for tasks (Set-ExecutionPolicy, Invoke-AtomicTest).  
✅ **Brute force attacks** using crowbar and hydra.  
✅ **Cyber attack simulations** with Atomic Red Team (ART).  
✅ **Troubleshooting network** connectivity between VM machines.  

By implementing this SOAR workflow, you can **automate security operations**, reduce response time, and improve efficiency in a **SOC environment**.  

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

## 🛠️ Installation & Setup  

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


### **4️⃣ Join Domain Controller**
1. **Navigate to Advanced system settings under PC > properties**
2. **Select Change**
3. **Set domain to mydfir.local**

   <p align="center"> <img src="https://i.gyazo.com/8998c20703e5398478ec090a399ba7a2.gif"></p>
   <br>
