---
marp: true
paginate: true
---
<!-- theme: gaia -->
<!-- _class: lead -->

# Container Audit Logging Best Practice



by Sanhe Hu

Description:1. What is Container Audit Log Best Practice
2. Fluent-bit Deamon Set deployment on EKS
3. Sample log generator application container
4. Engineering Knowledge Base Documentation and Run Books


---

<!-- _class: head -->

# What is Container Audit Log

- VM Level System Log: login, system command call, etc ...
- **Container Runtime Log**
- Application Log
- System Performance Metrics
- Terminal, Shell history

---

# Goal

- Be able to collect container runtime log for audit from all cluster
- Be able to send to common logging tools like ELK to consume
- Deployment automation (Optional)

---

# The Solution

https://github.com/GSA/odp-container-audit-log-best-practice/blob/main/01-About-This-POC/contanier-audit-log-solutioin.jpg

---

# Node Example

- input = rsyslog
- output = AWS S3
- fluent-bit daemon
- testing application container

https://github.com/GSA/odp-container-audit-log-best-practice/tree/main/02-Understand-How-syslog-and-fluent-bit-Works/2.9-Use-case-input-syslog-output-s3

---

# K8S Example

https://github.com/GSA/odp-container-audit-log-best-practice/tree/main/02-Understand-How-syslog-and-fluent-bit-Works/3.2-Use-case-fluent-bit-syslog-s3-on-EKS

---

# Engineer Knowledge Base Documentation and Run Books

https://github.com/GSA/odp-container-audit-log-best-practice

---

# Questions

---

# Thank You
