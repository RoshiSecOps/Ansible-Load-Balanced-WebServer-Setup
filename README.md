# 🛡️ Project: Sentinel-HA (Automated High-Availability Cluster)
Goal: An Ansible-orchestrated, high-availability web stack featuring load balancing, redundant application nodes, and centralized shared storage.

This project serves as an exercise to practice Infrastructure as Code (IaC), Network Segmentation, and Linux Hardening.

---

## 🏗️ Architecture Overview
The cluster consists of four dedicated Linux nodes (VMs) orchestrated from a central Ubuntu control plane:

#### Node Name	IP Address	Role	Service
- haproxy-server	10.0.2.6	Load Balancer	HAProxy (Front Door)
- webserver1	10.0.2.3	App Node A	Apache (HTTPD)
- webserver2	10.0.2.4	App Node B	Apache (HTTPD)
- nfs-server	10.0.2.5	Storage Node	NFS (Shared Media/Logs)

---

## 🔄 The Traffic Flow
User hits 10.0.2.6 (HAProxy).
HAProxy balances traffic between webserver1 and webserver2.
Web Nodes serve content from a shared mount point provided by the NFS-Server.

---

## 🛠️ Ansible Implementation Details
The automation is split into modular roles to ensure Idempotency and Reusability:

### 1. Setup
#### setup-*machine*.yaml file
- Each machine has a setup file that installs the required packages and checks they are functioning as intended.
- Running the setup playbooks will ensure all required packages are installed and ready to be utilized.

### 2. Config
#### config-*machine*.yaml file
- Each machine group has a config file as well that will edit the configuration files and ensure service restart and check the service status.

### 3. Firewall-Setup
#### firewalld-config-all.yaml file
- With this single playbook, we automate the provisioning of firewall rules across all the nodes, ensuring adequate connectivity.

### 4. SSH-Hardening
#### harden-ssh-config.yaml
- Lastly, we make sure that the ssh service is hardenend by disabling password logins and root logins directly via ssh.

### 5. How to run
- Setup SSH key based authentication from your ansible host
    - This can be done by generating a single key and then utilizing the ssh-copy-id command to every host, so the key can be installed in the authorized keys section.
- Run the playbooks
    - Sequence to run the playbooks is web -> proxy -> nfs
    - Example, run the setup playbooks as per the sequence above, then the config, then the firewall and finally the ssh.

### 6. Mounting
- Note that the NFS server folder gets mounted on the web servers at /var/www/html.

### 7. Future Recommendations
- Harden the Apache web servers
- Install SSL certificate via openssl or let's encrypt (certbot) and install them on the proxy.
- Optionally create a network diagram

--- 

## 🛡️ Security Posture (AppSec Focus)
Least Privilege: The NFS share is restricted to specific IP addresses.
Firewall Isolation: The Web nodes only accept traffic on port 80 from the HAProxy IP.
Immutable Config: All configuration changes are handled via Ansible; manual "drift" is discouraged.
