# ansible-maas-deploy

Ansible role to deploy [MAAS](https://maas.io/).

## Requirements

In order to install MAAS with this Ansible role, you first need to install
Ubuntu 18.04 on two servers (for example with another MAAS server).

## Role Variables

TBD

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
