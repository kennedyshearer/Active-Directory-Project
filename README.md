# 🤖 Active Directory Project  

## 📌 Project Description  
This project demonstrates setting up an Active Driectory(homelab), integrating Splunk, Kali Linux, and Atomic Red Team to explore how a domain environment works while learning how to ingest events to a SIEM and generate telemetry related to attacks in the real-world to help detect in the future.

✅ **Analyzing security logs** in Splunk.  
✅ **Powershell scripting** for tasks (Set-ExecutionPolicy, Invoke-AtomicTest).
✅ **Brute force attacks** using crowbar and hydra.  
✅ **Simulating cyber attacks** with Atomic Red Team (ART).  
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


### VM-2 for Target-PC 

**Specifications**

- **RAM:** 4GB+
- **HDD:** 50GB+
- **OS:** Windows 10 (64-bit)

### **1️⃣ Setup Static IP Address**
1. **Navigate to IPv4 properties and modify general settings**
   
   ![targetPC](https://i.gyazo.com/b7bfc9fcee64c6bb66c8a1d895cbfccb.png)


### **2️⃣ Install Splunk Universal Forwarder**


### **2️⃣ Install Sysmon**
