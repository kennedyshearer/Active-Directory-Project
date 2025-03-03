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

### **1️⃣ Install Splunk Enterprise**  
A shared folder was setup to retrieve the splunk deb. file from the host computer using these steps: 

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

![Splunk](https://github.com/user-attachments/assets/1ab85349-4e1f-4916-a363-ed0a1c8030c6)
