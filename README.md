# Ansible K3s Cluster

🚀 **One-button deployment of a K3s cluster with Cilium networking, storage, and optional media services using user-defined variables.**

## Features

- Deploys a minimal **K3s** cluster with user-defined master and worker nodes.
- **Cilium** is installed by default for networking (BGP optional).
- Uses **dynamic inventory generation** via Jinja2 (no need to manually edit inventory).
- Supports **custom storage backends** (`Longhorn`, `NFS`, `Local-Path`).
- Optionally deploys **ARR Media Stack** (Plex, Sonarr, Radarr, etc.).
- Fully customizable via **values.yml** and **cluster_config.yml**.
- Designed for **homelabs** and small-scale setups.

---

## **📌 Prerequisites**
Before proceeding, ensure you have the following:
- A working **Ansible** environment.
- SSH access to all cluster nodes.
- Properly configured `cluster_config.yml` and `values.yml`.

Test SSH connectivity:
```shell
ansible all -m ping -i inventory/generated_inventory.yml
```
---

## **📉 How to Deploy**

### **1️⃣ Edit Your Cluster Configuration (`cluster_config.yml`)**
**Before running anything, you must define your cluster nodes in `cluster_config.yml`.**  
This file specifies your **master and worker nodes** with their respective hostnames and IPs.

#### Example:
```yaml
masters:
  - hostname: master1
    ip: 192.168.10.30
workers:
  - hostname: worker1
    ip: 192.168.30.21
  - hostname: worker2
    ip: 192.168.30.22
  - hostname: worker3
    ip: 192.168.30.23
```

---

### **2️⃣ Customize General Settings (`values.yml`)**

Modify `values.yml` to configure SSH access, the K3s version, networking settings, storage, and optional services.

#### Example:
```yaml
ssh_user: josh
k3s_version: v1.29.1+k3s1
cilium_enabled: true
cilium_bgp_enabled: false

# ARR stack
deploy_media_stack: false  # Set to true to install Plex, Radarr, Sonarr, etc.

# Per-Service Toggle (More Customization)
deploy_plex: false
deploy_sonarr: false
deploy_radarr: false
deploy_bazarr: false
deploy_prowlarr: false
deploy_lidarr: false
deploy_overseerr: false
deploy_sabnzbd: false
```

---

### **3️⃣ Deploy the Cluster**

This playbook automates **inventory generation** and **cluster deployment** in one step:
```shell
ansible-playbook site.yml
```

To deploy specific components:
```shell
ansible-playbook site.yml --tags storage   # Deploys storage setup
ansible-playbook site.yml --tags media     # Deploys media stack
```

---

## **🌐 Why is BGP Optional?**
Not every user has a router that supports BGP/FRR.  
This playbook:

- Always installs **Cilium** for pod networking.
- Only enables **BGP** if the user can configure their router.
- Allows users to toggle **BGP on/off** in `values.yml`.

---

## **💾 Storage Configuration**

You can mix multiple storage backends (`Longhorn`, `NFS`, `Local-Path`).  
Define storage classes in `values.yml`:

```yaml
storage_classes:
  longhorn:
    enabled: true
    default: true
    default_path: "/mnt/longhorn"

  nfs:
    enabled: true
    server: "192.168.1.100"
    path: "/mnt/storage"
    default: false

  local-path:
    enabled: true
    default: false
```
🚨 Storage Rules:

    - Only one default storage class can be set (default: true).
    - NFS requires a server IP and path if enabled.
    - Longhorn auto-installs if enabled.

To deploy storage **before** the cluster:
```shell
ansible-playbook site.yml --tags storage
```

---

## **🌀 ARR Media Stack Configuration**

If enabled, this will install Plex, Radarr, Sonarr, and other media services.  
Ensure the correct settings in `values.yml` before deployment.

🚀 **To deploy the media stack:**
```shell
ansible-playbook site.yml --tags media
```

---

### **✅ Final Summary**
✔ **Users can configure multiple storage backends**  
✔ **ARR services deploy based on user preferences**  
✔ **Cilium LB and Ingress are installed only when needed**  
✔ **Fails gracefully if configurations are incorrect**  
✔ **Clear README instructions**  

🚀 **Ready to deploy? Let’s go!**

---

## **🔍 Project Roadmap**

### ✅ Completed
- [x] **Basic K3s install (masters + workers)**
- [x] **Default Cilium setup**
- [x] **Dynamic inventory generation with Jinja2**
- [x] **Cilium-based LoadBalancer & Ingress setup**
- [x] **ARR Media Stack deployment toggle**
- [x] **Storage backend configuration (Longhorn, NFS, Local)**
- [x] **Integrated inventory generation in `site.yml`**

### 🛠️ In Progress / Planned
- [ ] **Support for static IP configuration**
- [ ] **Improve inventory generation error handling**
- [ ] **Add node labels & taints for customization**
- [ ] **BGP automation for multi-VLAN setups**
- [ ] **Automate SSH key distribution for Ansible**
- [ ] **Ensure hostname, SSH, and timezone are configured properly**

