# CiscoACI

## Hostvar Based Ansible Playbook for Cisco ACI

Utilizes Host based specific variables as inputs for the playbook.

This format is based on the [My All in One, Device Specific YAML File for Ansible](https://github.com/jrosa770/ansible_device_var) hostvar scheme.

The `states` need to be set to present, absent or query for playbook task activation

Example:

```yml
tns:
  - name: 'DevQA1'
    state: absent

  - name: 'common'
    state: ignore

  - name: 'DevQA2'
    state: present
```

In the above example the playbook will perform the following actions

1. The tenant `DevQA1` will be deleted from the APIC
2. The tenant `common` will remain untouched or ignored
3. The tenant `DevQA2` will be added from the APIC

> ACI_Net_SimpleBuild Playbook

- Creates:
  - Tenants
  - VRF(s)

> ACI_Ops_SimpleBuild Playbook

- Creates:
  - AP(s)
  - BD(s)
  - BD Subnet(s)
  - EPG(s)

The playbook requires at least a basic inventory for the call `{{ inventory_hostname }}`

```ini
[all:vars]
ansible_python_interpreter=/usr/bin/python
ansible_connection = local

[aci:children]
aci_prod
aci_dev

[aci_dev]
lab-apic ansible_host=10.100.1.1

[aci_prod]
prod-apic ansible_host=10.200.1.1
```

### Playbook commands (Host: lab-apic.yml):

```sh
ansible-playbook ACI_Net_SimpleBuild.yml -i inventory -l lab-apic
ansible-playbook ACI_Ops_SimpleBuild.yml -i inventory -l lab-apic
```

