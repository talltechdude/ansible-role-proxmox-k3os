Proxmox K3os Cluster Builder
=========

Builds a K3os cluster on Proxmox

Requirements
------------

Requires an existing Proxmox cluster with appropriate storage and networking and K3os ISO in ISO folder

Role Variables
--------------

See `defaults/main.yml`
```
# Name of ISO file
k3os_iso: "k3os-amd64"

# K3s Token used for all nodes - please override to something more secure!
k3os_token: "SuperSecretToken"

# Datastore for templates (iso/snippets)
templates: "local"

# ISO path (override if required)
pve_iso_path: "{{ (templates == 'local') | ternary('/var/lib/vz/template/iso', '/mnt/pve/{{ templates }}/template/iso') }}"
# Snippets path (override if required)
pve_snippets_path: "{{ (templates == 'local') | ternary('/var/lib/vz/snippets', '/mnt/pve/{{ templates }}/snippets') }}"

# Datastore for disk storage
storage: "local"
# Local disk size (GB)
disk_size: 10

# Number of CPU Sockets
sockets: 1
# Memory per node (MB)
memory: 1024

# Bridge to use for NIC
bridge: vmbr0
# VLAN to use for NIC
vlan: 1

# Cluster name
cluster_name: k3os

# Location to store KubeConfig file
kubeconfig_file: "{{ cluster_name | default ('') }}.kubeconfig.yaml"

# Add KubeConfig with konfig
konfig: true

# Set static IPs on each node (forces IP to ansible_host value from inventory)
static_ip: false
```
Dependencies
------------

N/A

Examples
----------------

### Hosts file
```
[proxmox]
pve-01 ansible_host=10.0.0.31
pve-02 ansible_host=10.0.0.32
pve-03 ansible_host=10.0.0.33

[k3os_managers]
k3os-manager-01 ansible_host=10.0.0.11 pve_host=pve-01 vmid=701
k3os-manager-02 ansible_host=10.0.0.12 pve_host=pve-02 vmid=702
k3os-manager-03 ansible_host=10.0.0.13 pve_host=pve-03 vmid=703

[k3os_workers]
# Auto numbering worker nodes - use e.g. 01:06 for 01, 02, 03, 04, 05, 06
k3os-[01:06] node_number="{{ inventory_hostname | regex_replace(\"^k3os-(\\d+)$\", \"\\1\") | int }}" ansible_host="10.0.0.{{ 20 + node_number | int }}" pve_host="{{ groups.proxmox[(node_number | int - 1) % (groups.proxmox | length)] }}" vmid="{{ node_number | int + 750 }}"

[k3os:children]
k3os_managers
k3os_workers
```

### Playbook
```
- name: 'K3os | Install'
  hosts: k3os
  gather_facts: false
  roles:
  - name: talltechdude.proxmox_k3os
    vars:
      iso_name: "k3os-amd64-{{ k3os_version }}"
      cluster_name: "Example"
```

License
-------

MIT

Author Information
------------------

TallTechDude
