## Objective  
To set up a Security Monitoring Home Lab using the ELK Stack (Elasticsearch, Logstash, Kibana) for centralized log analysis and monitoring, and extend its functionality by integrating Fleet to manage agents on other servers.

---

## Requirements  
1. **Virtualization Tools:**  
   - [VirtualBox](https://www.virtualbox.org/)  
   - [VMware Workstation Pro](https://www.vmware.com/products/workstation-pro.html)  
   - [Proxmox VE](https://www.proxmox.com/)  

2. **Ubuntu Servers:**  
   - One server as the **ELK Stack Manager** (20.04 or later).  
   - One server as the **Fleet Agent Node** (20.04 or later).

3. **Stable Internet Connection**  
4. **Sudo Privileges** on both Ubuntu servers.

---

## Step-by-Step Guide

### 1. **Set Up ELK Stack on Ubuntu Server**  
   - Update the server packages:  
     ```bash
     sudo apt update && sudo apt upgrade -y
     ```
   - Install Elasticsearch:  
     ```bash
     wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
     sudo apt install apt-transport-https
     echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
     sudo apt update && sudo apt install elasticsearch
     ```
   - Start and enable Elasticsearch:  
     ```bash
     sudo systemctl enable --now elasticsearch
     ```
   - Install and configure Kibana:  
     ```bash
     sudo apt install kibana
     sudo systemctl enable --now kibana
     ```
   - Access Kibana via web browser at:  
     ```
     http://<ELK_SERVER_IP>:5601
     ```

### 2. **Install Fleet Server**  
   - On the same ELK server, set up Fleet Server for agent management:  
     ```bash
     sudo apt install elastic-agent
     sudo elastic-agent enroll --url=http://<ELK_SERVER_IP>:8220 --fleet-server-es=http://<ELK_SERVER_IP>:9200
     sudo systemctl enable --now elastic-agent
     ```
   - Replace `<ELK_SERVER_IP>` with the IP address of the ELK Manager.

### 3. **Set Up Fleet Agent on Another Ubuntu Server**  
   - On the **second Ubuntu server**, update packages:  
     ```bash
     sudo apt update && sudo apt upgrade -y
     ```
   - Install Elastic Agent:  
     ```bash
     sudo apt install elastic-agent
     ```
   - Enroll the agent into Fleet using the enrollment token and Fleet Server URL from Kibana:  
     ```bash
     sudo elastic-agent enroll --url=http://<FLEET_SERVER_IP>:8220 --enrollment-token=<ENROLLMENT_TOKEN>
     ```
   - Replace `<FLEET_SERVER_IP>` and `<ENROLLMENT_TOKEN>` with the appropriate values from the Fleet setup.

### 4. **Verify Agent Integration**  
   - Log in to Kibana.  
   - Navigate to **Fleet > Agents**.  
   - Confirm that the new agent is listed and its status is **Healthy**.

### 5. **Test Log Collection**  
   - Generate sample logs on the agent server (e.g., system logs, application logs).  
   - Verify that the logs are visible in Kibana under **Discover**.

## Conclusion  
This guide sets up the ELK Stack for centralized log monitoring and adds Fleet to manage agents on other servers. With this setup, you can monitor and analyze logs from multiple servers in real-time, providing a robust platform for security monitoring and incident detection.

