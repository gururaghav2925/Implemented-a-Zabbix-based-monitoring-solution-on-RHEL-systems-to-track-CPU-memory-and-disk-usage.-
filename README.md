# Implemented a Zabbix-based monitoring solution on RHEL systems to track CPU, memory, and disk usage. Configured triggers and email notifications for proactive incident response.

## 1. Aim
To establish a centralized, real-time monitoring ecosystem for **Red Hat Enterprise Linux (RHEL)** environments. The primary goal is to move from reactive troubleshooting to **proactive incident response** by tracking system health metrics (CPU, Memory, Disk) and automating stakeholder notifications.

---

## 2. Procedure
The solution utilizes a **Server-Agent architecture**. The Zabbix Server acts as the central data aggregator and visualization engine, while lightweight Zabbix Agents reside on the RHEL target hosts to collect and transmit low-level OS metrics.



---

## 3. Steps to Follow

### Phase I: Server Side (RHEL)
1. **Install Zabbix Repository:** Configure the official Zabbix dnf repository for RHEL.
2. **Deploy Core Components:** Install the `zabbix-server-mysql`, `zabbix-web-mysql`, and `zabbix-apache-conf` packages.
3. **Database Setup:** Create a MySQL/PostgreSQL database, import the initial schema, and configure `zabbix_server.conf` with the database credentials.
4. **Security & Firewall:** Configure SELinux policies to allow Zabbix web communication and open port **10051** on the firewall.

### Phase II: Agent Side Automated Deployment (RHEL Targets)
To standardize deployment across multiple nodes, the agent installation is automated using the following Bash script. 

**Deployment Script (`install_zabbix.sh`):**
```bash
#!/bin/bash

# Variables - Update these to match your environment
ZABBIX_SERVER_IP="192.168.1.100"
ZABBIX_REPO_URL="[https://repo.zabbix.com/zabbix/6.4/rhel/9/x86_64/zabbix-release-6.4-1.el9.noarch.rpm](https://repo.zabbix.com/zabbix/6.4/rhel/9/x86_64/zabbix-release-6.4-1.el9.noarch.rpm)"

echo "Starting Zabbix Agent Installation..."

# 1. Install Zabbix Repository
sudo dnf install -y $ZABBIX_REPO_URL
sudo dnf clean all

# 2. Install Zabbix Agent
sudo dnf install -y zabbix-agent

# 3. Configure the Agent
sudo sed -i "s/^Server=127.0.0.1/Server=$ZABBIX_SERVER_IP/" /etc/zabbix/zabbix_agentd.conf
sudo sed -i "s/^ServerActive=127.0.0.1/ServerActive=$ZABBIX_SERVER_IP/" /etc/zabbix/zabbix_agentd.conf
sudo sed -i "s/^Hostname=Zabbix server/Hostname=$(hostname)/" /etc/zabbix/zabbix_agentd.conf

# 4. Configure Firewall
sudo firewall-cmd --add-port=10050/tcp --permanent
sudo firewall-cmd --reload

# 5. Start and Enable Service
sudo systemctl enable --now zabbix-agent

echo "Zabbix Agent is installed and running on $(hostname)."

```


# Output:


<img width="960" height="901" alt="image" src="https://github.com/user-attachments/assets/ef763d81-49d2-47aa-b950-1dd2fe63586d" />


<img width="662" height="514" alt="image" src="https://github.com/user-attachments/assets/7a033a62-6e2f-45a6-b42a-d1b4051dcfb8" />



# Result:

The Zabbix monitoring solution was successfully deployed and configured across the target RHEL systems. Continuous, real-time data collection for CPU utilization, memory consumption, and disk I/O was observed and verified via the central Zabbix server dashboard. Upon simulating high-load stress conditions on the client nodes, the predefined triggers correctly identified resource threshold breaches (e.g., CPU load exceeding 90% for 5 minutes). Consequently, the system successfully generated and dispatched automated email notifications to the designated administrators, confirming the operational readiness of the proactive incident response mechanism.
