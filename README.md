# Ansible K3s Cluster

🚀 **One-button deployment of a K3s cluster with Cilium networking, using user-defined variables.**

## Features

- Deploys a minimal **K3s** cluster with user-defined master and worker nodes.
- **Cilium** is installed by default for networking (BGP optional).
- Uses **dynamic inventory generation** via Jinja2 (no need to manually edit inventory).
- Fully customizable via **values.yml** and **cluster_config.yml**.
- Designed for **homelabs** and small-scale setups.

---

## **📌 How to Deploy**

### **1️⃣ Edit Your Cluster Configuration (`cluster_config.yml`)**
**Before running anything, you must define your cluster nodes in `cluster_config.yml`.**  
This file specifies your **master and worker nodes** with their respective hostnames and IPs.

Example:
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
2️⃣ Customize General Settings (values.yml)

Modify values.yml to configure SSH access, the K3s version, and networking settings.

Example:
```yaml
    ssh_user: josh
    k3s_version: v1.29.1+k3s1
    cilium_enabled: true
    cilium_bgp_enabled: false
3️⃣ Generate the Dynamic Inventory

Since the inventory is dynamically created from cluster_config.yml, you must render the inventory template before deployment.

Run the following command to generate inventory/generated_inventory.yml:
```shell
    ansible-playbook render_inventory.yml -e "@cluster_config.yml"

This step converts your cluster configuration into a structured Ansible inventory.
4️⃣ Deploy the Cluster

Once the inventory is generated, run the main playbook to set up your K3s cluster:
```shell
    ansible-playbook -i inventory/generated_inventory.yml site.yml
🌐 Why is BGP Optional?

Not every user has a router that supports BGP/FRR.
This playbook:

    Always installs Cilium for pod networking.
    Only enables BGP if the user can configure their router.
    Allows users to toggle BGP on/off in values.yml.
## Project Roadmap

- [x] Basic K3s install (masters + workers)
- [x] Default Cilium setup
- [ ] Support for static IP configuration
- [ ] Improve dynamic inventory generation
- [ ] Add node labels & taints for customization
- [ ] BGP automation for multi-VLAN setups
