🛡️ Project: Sentinel-HA (Automated High-Availability Cluster)
Goal: An Ansible-orchestrated, high-availability web stack featuring load balancing, redundant application nodes, and centralized shared storage.

This project serves as an exercise to practice Infrastructure as Code (IaC), Network Segmentation, and Linux Hardening.

🏗️ Architecture Overview
The cluster consists of four dedicated Linux nodes (VMs) orchestrated from a central Ubuntu control plane:

Node Name	IP Address	Role	Service
haproxy-server	10.0.2.6	Load Balancer	HAProxy (Front Door)
webserver1	10.0.2.3	App Node A	Apache (HTTPD)
webserver2	10.0.2.4	App Node B	Apache (HTTPD)
nfs-server	10.0.2.5	Storage Node	NFS (Shared Media/Logs)

🔄 The Traffic Flow
User hits 10.0.2.6 (HAProxy).
HAProxy balances traffic between webserver1 and webserver2.
Web Nodes serve content from a shared mount point provided by the NFS-Server.
🛠️ Ansible Implementation Details
The automation is split into modular roles to ensure Idempotency and Reusability:

1. Common Configuration (common)
Package Management: Updates all nodes and installs essential tools (vim, curl, iptables, httpd, other required packages).
Security: Configures Iptabes to only allow necessary inter-node traffic.
SSH Hardening: Ensures SSH is only accessible via keys.

2. Load Balancer Role (haproxy)
Installs and configures HAProxy.
Implements a Round Robin algorithm for the backend web nodes.
Configures health checks to automatically remove a failed web node from the rotation.

3. Web Layer Role (apache)
Installs Apache (HTTPD).
Configures custom virtual hosts.
NFS Integration: Mounts the remote storage from 10.0.2.5 to /var/www/html.

4. Storage Role (nfs)
Configures the NFS-Kernel-Server.
-
-
🚀 How to Run -- TBD
Prerequisites: Ensure SSH key access from your host to all four nodes.



🛡️ Security Posture (AppSec Focus)
Least Privilege: The NFS share is restricted to specific IP addresses.
Firewall Isolation: The Web nodes only accept traffic on port 80 from the HAProxy IP.
Immutable Config: All configuration changes are handled via Ansible; manual "drift" is discouraged.
