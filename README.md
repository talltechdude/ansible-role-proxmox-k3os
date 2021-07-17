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

Example Playbook
----------------

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
