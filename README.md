# Ansible K3s Cluster

🚀 **One-button deployment of a K3s cluster with Cilium networking, using user-defined variables.**

## Features

- Deploys a minimal **K3s** cluster with user-defined master and worker nodes.
- **Cilium** is installed by default for networking (BGP optional).
- Fully customizable via **values.yml** and **cluster_config.yml**.
- Designed for **homelabs** and small-scale setups.

## How It Works

1. **Define your cluster** in `cluster_config.yml`:
   ```yaml
   masters:
     - hostname: master1
       ip: 192.168.10.30
   workers:
     - hostname: worker1
       ip: 192.168.30.21
2. Customize general settings in values.yml:
    ```yaml
       ssh_user: josh
       k3s_version: v1.29.1+k3s1
       cilium_enabled: true
       cilium_bgp_enabled: false
3. Run the playbook to deploy:
    ```shell
       ansible-playbook -i inventory/generate_inventory.yml site.yml
Why is BGP Optional?

Not every user has a router that supports BGP/FRR. Some users may only need basic pod networking, while others want multi-VLAN or external routing.

This playbook:

    Always installs Cilium for pod networking.
    Only enables BGP if the user can configure their router.
    Allows users to toggle BGP on/off in values.yml.

Project Roadmap

Basic K3s install (masters + workers)
Default Cilium setup
Support for static IP configuration
Improve dynamic inventory generation
Add node labels & taints for customization
BGP automation for multi-VLAN setups