# 🛡️ Project: Sentinel-HA (Automated High-Availability Cluster)
Goal: An Ansible-orchestrated, high-availability web stack featuring load balancing, redundant application nodes, and centralized shared storage.

This project serves as an exercise to practice Infrastructure as Code (IaC), Network Segmentation, and Linux Hardening.

## 🏗️ Architecture Overview
The cluster consists of four dedicated Linux nodes (VMs) orchestrated from a central Ubuntu control plane:

Node Name	IP Address	Role	Service
haproxy-server	10.0.2.6	Load Balancer	HAProxy (Front Door)
webserver1	10.0.2.3	App Node A	Apache (HTTPD)
webserver2	10.0.2.4	App Node B	Apache (HTTPD)
nfs-server	10.0.2.5	Storage Node	NFS (Shared Media/Logs)

## 🔄 The Traffic Flow
User hits 10.0.2.6 (HAProxy).
HAProxy balances traffic between webserver1 and webserver2.
Web Nodes serve content from a shared mount point provided by the NFS-Server.

## 🛠️ Ansible Implementation Details
The automation is split into modular roles to ensure Idempotency and Reusability:

### 1. Setup
#### setup-*machine*.yaml file
- Each machine has a setup file that installs the required packages and checks they are functioning as intended.
- Configuration files are edited/created as need, you can review it inside the playbooks.

-
## 🚀 How to Run -- TBD
Prerequisites: Ensure SSH key access from your host to all four nodes.



## 🛡️ Security Posture (AppSec Focus)
Least Privilege: The NFS share is restricted to specific IP addresses.
Firewall Isolation: The Web nodes only accept traffic on port 80 from the HAProxy IP.
Immutable Config: All configuration changes are handled via Ansible; manual "drift" is discouraged.
