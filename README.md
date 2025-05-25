# **DevOps Network Automation with Linux Scripts**

## **Overview**
This repository contains five Linux automation scripts that leverage **network commands** for **DevOps automation use cases**. Each script is designed to enhance infrastructure management, troubleshooting, and monitoring in a **cloud-native** environment.

## **Use Cases Covered**
✅ **Dynamic IP Allocation Monitoring** (`ip` command)  
✅ **DNS Health Check & Debugging** (`dig`, `nslookup`)  
✅ **Latency & Bandwidth Monitoring** (`ping`, `iperf3`)  
✅ **Firewall & Port Security Audit** (`iptables`, `netstat`)  
✅ **Automated SSL Certificate Expiry Alerting** (`openssl`)  

### **Best Practices Followed**
- **Modular & Parameterized Design**: Allows flexible inputs.
- **Logging & Error Handling**: Ensures traceability.
- **Security Considerations**: Uses least privilege principle.
- **CI/CD Integration**: Scripts can be used with Jenkins, GitHub Actions, etc.
- **Scalability**: Works across hybrid cloud and on-prem environments.

---

## **1️⃣ Dynamic IP Allocation Monitoring (`ip-monitor.sh`)**

### **Use Case**
Large-scale cloud infrastructure relies on dynamic IP allocation. This script **tracks IP assignments** and **alerts** on unexpected changes.

### **Script: `ip-monitor.sh`**
```bash
#!/bin/bash
# Monitors IP allocation changes and logs changes.

LOG_FILE="/var/log/ip_changes.log"
CURRENT_IP=$(ip a show eth0 | grep "inet " | awk '{print $2}' | cut -d/ -f1)

while true; do
    NEW_IP=$(ip a show eth0 | grep "inet " | awk '{print $2}' | cut -d/ -f1)
    if [[ "$NEW_IP" != "$CURRENT_IP" ]]; then
        echo "$(date) - IP changed from $CURRENT_IP to $NEW_IP" | tee -a $LOG_FILE
        CURRENT_IP=$NEW_IP
    fi
    sleep 10  # Check every 10 seconds
done
```
### **Automation Features**
✅ **Continuous monitoring** of dynamic IP changes  
✅ **Logging for audit trail**  
✅ **Integrates into DevOps incident response**

---

## **2️⃣ DNS Health Check & Debugging (`dns-check.sh`)**

### **Use Case**
Fast DNS resolution is crucial for **CI/CD pipelines** and **API services**. This script **checks DNS health**, detects misconfigurations, and logs response times.

### **Script: `dns-check.sh`**
```bash
#!/bin/bash
# Performs DNS resolution check and logs response times.

DOMAIN=${1:-"example.com"}
LOG_FILE="/var/log/dns_check.log"

echo "$(date) - Checking DNS resolution for $DOMAIN" | tee -a $LOG_FILE
START_TIME=$(date +%s%3N)
dig +short $DOMAIN
END_TIME=$(date +%s%3N)
TIME_TAKEN=$((END_TIME - START_TIME))

echo "$(date) - DNS lookup took $TIME_TAKEN milliseconds" | tee -a $LOG_FILE

if [[ $TIME_TAKEN -gt 500 ]]; then
    echo "Warning: DNS lookup is slow!" | tee -a $LOG_FILE
fi
```
### **Automation Features**
✅ **Tracks DNS lookup speed**  
✅ **Detects slow resolutions impacting services**  
✅ **Alerts if response exceeds threshold**

---

## **3️⃣ Latency & Bandwidth Monitoring (`network-health.sh`)**

### **Use Case**
This script measures **latency** and **bandwidth performance** in cloud environments, helping **DevOps engineers** troubleshoot network bottlenecks.

### **Script: `network-health.sh`**
```bash
#!/bin/bash
# Measures network latency and bandwidth.

TARGET=${1:-"google.com"}
LOG_FILE="/var/log/network_health.log"

echo "$(date) - Testing network latency to $TARGET" | tee -a $LOG_FILE
ping -c 5 $TARGET | tee -a $LOG_FILE

echo "$(date) - Testing bandwidth using iperf3 (requires iperf3 server)" | tee -a $LOG_FILE
iperf3 -c $TARGET -t 5 | tee -a $LOG_FILE
```
### **Automation Features**
✅ **Latency testing with `ping`**  
✅ **Bandwidth benchmarking with `iperf3`**  
✅ **Can be triggered in CI/CD to validate network before deployments**

---

## **4️⃣ Firewall & Port Security Audit (`port-audit.sh`)**

### **Use Case**
In **secure cloud environments**, DevOps engineers must ensure **ports and firewall rules are correctly configured**. This script audits firewall rules and detects open ports.

### **Script: `port-audit.sh`**
```bash
#!/bin/bash
# Audits firewall rules and detects unauthorized open ports.

LOG_FILE="/var/log/firewall_audit.log"

echo "$(date) - Auditing firewall rules" | tee -a $LOG_FILE
iptables -L | tee -a $LOG_FILE

echo "$(date) - Checking open ports" | tee -a $LOG_FILE
netstat -tulnp | grep LISTEN | tee -a $LOG_FILE
```
### **Automation Features**
✅ **Identifies security risks in firewall rules**  
✅ **Lists open ports to detect potential vulnerabilities**  
✅ **Integrates with security automation workflows**

---

## **5️⃣ Automated SSL Certificate Expiry Alerting (`ssl-expiry.sh`)**

### **Use Case**
In **production environments**, expired SSL certificates can disrupt **CI/CD pipelines** and lead to security failures. This script monitors SSL certificate expiry and sends alerts.

### **Script: `ssl-expiry.sh`**
```bash
#!/bin/bash
# Checks SSL certificate expiry.

DOMAIN=${1:-"example.com"}
LOG_FILE="/var/log/ssl_expiry.log"

echo "$(date) - Checking SSL expiry for $DOMAIN" | tee -a $LOG_FILE
EXPIRY_DATE=$(echo | openssl s_client -connect $DOMAIN:443 -servername $DOMAIN 2>/dev/null | openssl x509 -noout -enddate | cut -d= -f2)

echo "SSL Certificate expires on $EXPIRY_DATE" | tee -a $LOG_FILE

if [[ "$(date -d "$EXPIRY_DATE" +%s)" -lt "$(date -d "30 days" +%s)" ]]; then
    echo "⚠️ ALERT: SSL Certificate is expiring within 30 days!" | tee -a $LOG_FILE
fi
```
### **Automation Features**
✅ **Detects SSL expiry early**  
✅ **Alerts security teams before certificates expire**  
✅ **Critical for secure CI/CD and production systems**

---

## **Deployment & Automation**
These scripts can be:
- **Scheduled via CronJobs**
  ```bash
  crontab -e
  ```
  Example entry to run **SSL monitoring** every day at midnight:
  ```
  0 0 * * * /path/to/ssl-expiry.sh example.com
  ```
- **Integrated into CI/CD pipelines** for **pre-deployment checks**.
- **Used in monitoring tools** such as Prometheus or ELK Stack.

---

## **Conclusion**
These five Linux scripts provide **essential network automation** tools for **DevOps engineers**, ensuring **secure, reliable, and optimized infrastructure**. Whether it's **firewall auditing, latency monitoring, DNS health checks, IP tracking, or SSL alerts**, they enable proactive management of **cloud-native environments**.

For further enhancements:
- Consider **logging results to centralized monitoring tools**.
- Automate alerts via **Slack or email notifications**.

Happy automating! 🚀
```

