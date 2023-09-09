# CiscoACI

## Hostvar Based Ansible Playbook for Cisco ACI

Utilizes Host based specific variables as inputs for the playbook.

This format is based on the [My All in One, Device Specific YAML File for Ansible](https://github.com/jrosa770/ansible_device_var) hostvar scheme.

The `state` need to be set to present, absent or query for playbook task activation

Example:

Hostvar File: lab-apic.yml

```yml
aci_apic:
  hostname: 'lab-apic'
  location_domain_name: "example.com"
  ipv4: 10.100.1.1
  mask: 255.255.255.0
  gw: 10.100.1.254

tns:
  - name: 'DevQA1'
    state: absent

  - name: 'common'
    state: ignore

  - name: 'DevQA2'
    state: present
```

Playbook Task:

```yml
  - name: Load APIC Variable File
    include_vars: "hostvars/{{ inventory_hostname }}.yml"

  - name: TN Task
    aci_tenant:
      host: "{{ aci_apic.ipv4 }}"
      username: "{{ username }}"
      password: "{{ password }}"
      validate_certs: no
      tenant: "{{ item.name }}"
      description: "{{ reference }}"
      state: "{{ item.state }}"
    delegate_to: localhost
    register: result_tn
    when: (item.state == "present" or item.state == "absent" or item.state == "query")
    with_items:
      - "{{ tns }}"
```

In the above example the playbook will perform the following actions

1. The tenant `DevQA1` will be deleted from the APIC
2. The tenant `common` will remain untouched or ignored
3. The tenant `DevQA2` will be added to the APIC

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

### Playbook commands (Host: lab-apic.yml) Using included inventory:

```sh
ansible-playbook ACI_Net_SimpleBuild.yml -i inventory -l lab-apic
ansible-playbook ACI_Ops_SimpleBuild.yml -i inventory -l lab-apic
```

