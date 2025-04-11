# 🛡️ Honeypot-Threat-Detection-Lab

![12 overall structure](https://github.com/user-attachments/assets/a397584c-965b-4cbf-a01d-2e98ae5d0ffb)


---

# 🔥 Real-World Honeypot Threat Detection Lab (Azure + Sentinel)

## 🧠 **What This Project Is About:**
This is a **real-world cybersecurity detection project** using a Windows 10 honeypot virtual machine in Microsoft Azure. In this project, I created a vulnerable environment, monitor it for failed login attempts, forward logs to Microsoft Sentinel, enrich the data with geolocation, and visualize attacker patterns on a global map. This is not a simulation—it's a real setup showing how attackers from around the world try to brute force into exposed machines.

---

## ✅ **Step-by-Step Instructions**

### **Step 1 – Create and Configure the Honeypot VM**
- Go to the Azure Portal and create a **Windows 10 Virtual Machine**.
- Log in to the VM using RDP.
- **Disable the Windows Firewall**:
  - Press `Win + R` → Type `wf.msc`
  - Set Domain, Private, and Public profiles to "Off"
- Open **Event Viewer**:
  - Go to `Windows Logs > Security`
  - Look for Event ID `4625` – Failed Logons
 ![1 event log failed logon honeypot](https://github.com/user-attachments/assets/0ec58f09-526f-469f-811d-72f569f45c17)
---

![2 event log failed filter](https://github.com/user-attachments/assets/e997e33c-7146-4d5e-8077-89efacd008ac)

    

---

### **Step 2 – Create Log Analytics Workspace (LAW)**
- In Azure, search **"Log Analytics Workspace"**
- Click **Create**
- Select your resource group and region
![3 create LAW](https://github.com/user-attachments/assets/c0b4475c-d732-4c07-be2d-f7c5f3dce42f)

---

### **Step 3 – Set Up Microsoft Sentinel**
- Go to **Microsoft Sentinel**
- Click **+ Add** and link it to your LAW
- Go to **Data Connectors**
- Search: **Windows Security Events via AMA**
- Click **"Open Connector"** and configure it
![4 create security event connector](https://github.com/user-attachments/assets/44a285c7-f092-415d-8cf7-d2e7624630a4)
---

![5 clicked open connector page ](https://github.com/user-attachments/assets/465e9c7f-4648-4086-abd9-153dc861dbc6)

---

### **Step 4 – Verify the VM Extension**
- Go to the VM in Azure
- Under **Extensions + applications**, confirm:
  - `azureMonitorWindowsAgent` is installed
  - ---
![5 check extensions in settings of VM ](https://github.com/user-attachments/assets/002c3f6f-9d4e-46b2-abe1-ab55182705f3)

---

### **Step 5 – Run a Basic KQL Query**
In **Log Analytics > Logs**, run this to view failed logins:

```
SecurityEvent
| where Computer == "vmgoogle-corey"
| where EventID == "4625"
| project Account, Computer, EventID, Activity, IpAddress
```
![6 LAW KQL failed logons](https://github.com/user-attachments/assets/825e93d7-9975-4740-9ea7-b627102f7bb8)

---

### **Step 6 – Advanced KQL for Map Visualization**
Use a GeoIP Watchlist to add location data:

```
let GeoIPDB_FULL = _GetWatchlist("geoip");
let WindowsEvents = SecurityEvent
| where EventID == 4625
| where Computer == "vmgoogle-corey"
| order by TimeGenerated desc
| evaluate ipv4_lookup(GeoIPDB_FULL, IpAddress, network);
WindowsEvents
```
![7 LAW KQL MAPPED LOCATION](https://github.com/user-attachments/assets/10f5baa7-6cbe-4858-88d9-3e5da3e6a9f7)

---

### **Step 7 – Create a Workbook**
- Go to **Sentinel > Workbooks**
- Click **+ Add Workbook** to visulize all data collected on a map
 ---
![8 CREATE WORKBOOK SENTINEL](https://github.com/user-attachments/assets/975eef1a-5bbe-4275-8d29-bfff814a1207)

---

### **Step 8 – Add a Query**
- Click **Add > Query**
-----
![9 EDIT ADD QUERY ](https://github.com/user-attachments/assets/cfc46e06-3660-4ac7-9cf5-bd71f634699b)

---

### **Step 9 – Use Advanced Editor**
- Click **Advanced Editor**
- Paste your **map JSON code** for visualization
---

![10 CLICK ADVANCED EDITOR AND PASTE JSON MAP CODE](https://github.com/user-attachments/assets/95f90247-db4e-4d95-88c0-7f447dfcf944)

---

### **Step 10 – View Final Attack Map**
- The map will show IP-based attacker locations.
- You’ll likely observe most attacks are originating from **Europe**.
---

![11 FINAL ATTACK MAP vm corey](https://github.com/user-attachments/assets/0e5bc0ea-3484-4031-b905-e82559ec8184)

---

## 🎯 Final Summary
In this lab, I deployed a real honeypot virtual machine in Azure to attract and log unauthorized access attempts. I captured failed login events, forwarded the data to Microsoft Sentinel through a Log Analytics Workspace, and enriched the logs with geolocation data using a custom watchlist. I then created a live attack map to visualize where the threats were coming from. This hands-on project gave me real-world insight into how attackers actively scan for exposed systems and allowed me to track and analyze their origin to better understand global threat patterns.

**Key Insight:**  
This project shows how attackers are actively scanning the internet for exposed VMs. With this setup, we can **track those threats**, see **who’s attacking us**, and **where they’re coming from**—giving us a clearer picture of global attack patterns in real time.
