# ansible-maas-deploy

Ansible role to deploy [MAAS](https://maas.io/).

## Requirements

In order to install MAAS with this Ansible role, you first need to install
Ubuntu 18.04 on two servers (for example with another MAAS server).

## Role Variables

You have to set the following variables for both MAAS servers

```yaml
# IP address of MAAS master
maas_master: 172.16.128.1

# IP address of MAAS slave
maas_slave: 172.16.128.2

# Default domain
maas_dns_domain: 'maas.oob-test.naturalis.local'

# Upstream DNS servers
maas_dnsservers:
  - 192.87.106.106
  - 195.169.124.124

# MAAS admin users
maas_adminusers:
  - username: 'maas'
    email: 'admin@{{ maas_dns_domain }}'
    password: 'maas'

# Password and IP for MAAS database
maas_database:
  host: 172.16.168.1
  password: maas
```

For maas-master and maas-slave you have to define the following variables:

```yaml
maas_network_interfaces:
  lo:
    mode: loopback
    config: {}
  eno1:
    config:
      address: 172.16.44.100
      netmask: 255.255.255.0
      network: 172.16.44.0
      gateway: 172.16.44.1
      boardcast: 172.16.44.255
      dns-nameservers: 192.87.106.106
    mode: static
  eno2:
    config:
      address: 172.16.168.1
      netmask: 255.255.255.0
    mode: static
  enp179s0f0:
    config: {}
    mode: manual
```

On maas-master provide these variables as well:

```yaml
postgresql_global_config_options:
  - option: listen_addresses
    value: 'localhost, 172.16.168.1'

postgresql_hba_entries:
  - type: local
    database: all
    user: postgres
    auth_method: peer
  - type: host
    database: all
    user: maas
    address: 172.16.168.0/24
    auth_method: trust
  - type: host
    database: replication
    user: replicator
    address: 172.16.168.0/24
    auth_method: trust

postgresql_users:
  - name: maas
    pass: "{{ 'maas' | password_hash('sha512') }}"
    encrypted: yes
    role_attr_flags: 'REPLICATION,LOGIN'
  - name: replicator
    pass: "{{ 'replicator' | password_hash('sha512') }}"
    encrypted: yes
    role_attr_flags: 'SUPERUSER,LOGIN'
```

## Dependencies

This role depends on the following roles:

* [naturalis.base](https://github.com/naturalis/ansible-naturalis.base)
* [geerlingguy.postgresql](https://galaxy.ansible.com/geerlingguy/postgresql)
* [ansible-maas](https://github.com/mrlesmithjr/ansible-maas)

A list of other roles hosted on Galaxy should go here, plus any details in
regards to parameters that may need to be set for other roles, or variables that
are used from other roles.

## Example Playbook

This role can be used in a playbook like this:

```yaml
# First bootstrap Ubuntu
- hosts:
  - maas
  gather_facts: false
  tasks:
    - name: Install Python
      raw: apt-get install -y python-minimal python-netaddr
    - name: Set mtu to 1500
      command: ifconfig eno1 mtu 1500

# Install the role
- hosts:
  - maas
  gather_facts: true
  roles:
    - role: ansible-maas-deploy
```

## License

Apache License v2.0

## Author Information

David Heijkamp
Atze de Vries (original author of underlying code)
