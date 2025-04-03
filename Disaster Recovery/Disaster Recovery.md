# **🔥 Disaster Recovery (DR) in Detail**  

Disaster Recovery (DR) is a **critical part of Site Reliability Engineering (SRE)** and **DevOps**. It ensures that systems can **quickly recover** from failures like hardware crashes, cyberattacks, or natural disasters.  

---

## **✅ What is Disaster Recovery?**
Disaster Recovery (DR) is the **process of restoring IT systems and data** after an outage or disaster to ensure **business continuity**.  

🚨 **Common Causes of Disasters:**  
1️⃣ **Hardware Failures** – Disk crashes, power failures  
2️⃣ **Cyberattacks** – Ransomware, DDoS attacks  
3️⃣ **Software Bugs** – Faulty deployments, memory leaks  
4️⃣ **Human Errors** – Accidental data deletion  
5️⃣ **Natural Disasters** – Earthquakes, floods  

---

## **✅ Key Components of Disaster Recovery**
### **1️⃣ Recovery Point Objective (RPO)**
- **Definition:** The maximum acceptable data loss after a failure.  
- **Example:** If **RPO = 10 minutes**, backups should be taken every 10 minutes.  

### **2️⃣ Recovery Time Objective (RTO)**
- **Definition:** The maximum time allowed to restore services after a disaster.  
- **Example:** If **RTO = 30 minutes**, systems must be back online within 30 minutes.  

📌 **Formula for Total Downtime:**
\[
\text{Total Downtime} = \text{Detection Time} + \text{Response Time} + \text{Recovery Time}
\]

---

## **✅ Types of Disaster Recovery Strategies**
### **1️⃣ Backup and Restore (Basic DR)**
🔹 **How It Works:**  
- Regular backups of data  
- Stored in cloud (AWS S3, Google Cloud Storage) or on-prem  
- Restored manually in case of failure  

🚀 **Pros:** Low cost, simple to implement  
⚠️ **Cons:** High RTO (slow recovery)  

🔹 **Example:**  
- Taking daily database snapshots and storing them in **AWS S3**  
- Using **rsync** to copy files to a remote server  

```bash
rsync -avz /data/ remote-backup:/backup/
```

---

### **2️⃣ Pilot Light Strategy (Minimal DR)**
🔹 **How It Works:**  
- A **minimal environment** is always running  
- Data is replicated, but most servers are turned off  
- In a disaster, scale up the infrastructure  

🚀 **Pros:** Lower cost than Hot Standby, faster than Backup & Restore  
⚠️ **Cons:** Requires manual scaling during recovery  

🔹 **Example:**  
- **Databases are replicated**, but app servers are turned off  
- AWS **EC2 Auto Scaling** is used to launch new instances when needed  

```yaml
apiVersion: autoscaling/v1
kind: HorizontalPodAutoscaler
metadata:
  name: web-hpa
spec:
  maxReplicas: 10
  minReplicas: 2
  targetCPUUtilizationPercentage: 70
```

---

### **3️⃣ Warm Standby Strategy (Partial DR)**
🔹 **How It Works:**  
- A **smaller version** of production is always running  
- Can be **scaled up** quickly in case of failure  

🚀 **Pros:** Faster recovery than Pilot Light  
⚠️ **Cons:** Costs more since instances are running  

🔹 **Example:**  
- Running a **smaller database replica**  
- Keeping **two app servers** instead of ten  

```sql
CREATE DATABASE replica_db AS SELECT * FROM main_db;
```

---

### **4️⃣ Hot Standby (Full DR - Active/Active)**
🔹 **How It Works:**  
- **Fully duplicated environment**  
- Traffic can be instantly switched to backup systems  

🚀 **Pros:** **Near-zero downtime**, best RTO  
⚠️ **Cons:** **Expensive** (double infrastructure costs)  

🔹 **Example:**  
- Two **AWS Regions** (us-east-1, us-west-2)  
- Load balancer shifts traffic if one region fails  

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: failover-ingress
spec:
  rules:
    - host: myapp.com
      http:
        paths:
          - path: /
            backend:
              service:
                name: app-primary
          - path: /backup
            backend:
              service:
                name: app-secondary
```

---

## **✅ Disaster Recovery in Cloud (AWS, GCP, Azure)**
### **AWS Disaster Recovery Services**
- **AWS Backup** – Automated backups  
- **AWS S3 Cross-Region Replication** – Store data in multiple locations  
- **AWS RDS Multi-AZ** – High availability for databases  
- **AWS Route 53 Failover** – Redirect traffic in case of failure  

---

## **✅ How to Build a Disaster Recovery Plan (DRP)**
### **1️⃣ Risk Assessment**
- Identify critical services  
- Define **RPO/RTO goals**  

### **2️⃣ Choose DR Strategy**
- Based on budget & downtime tolerance  

### **3️⃣ Implement DR Tools**
- Backups, replication, failover  

### **4️⃣ Test the DR Plan**
- Regular DR drills (simulate failures)  

### **5️⃣ Continuously Improve**
- Adjust based on incidents  

---

## **🔥 Summary: Disaster Recovery Strategies**
| **Strategy** | **RTO (Recovery Time)** | **RPO (Data Loss)** | **Cost** | **Example** |
|-------------|------------------|----------------|------|----------|
| **Backup & Restore** | **Hours to Days** | **Minutes to Hours** | Low | S3 Backups |
| **Pilot Light** | **Minutes to Hours** | **Seconds to Minutes** | Medium | AWS Auto Scaling |
| **Warm Standby** | **Minutes** | **Near Zero** | High | Database Replicas |
| **Hot Standby** | **Seconds** | **Zero** | Very High | Multi-Region Deployment |

---
