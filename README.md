Ansible Role: Add inventory host
=========

Ansible role to simplify adding ansible inventory host items on the localhost

Requirements
------------

- SSH access details to the server you are going to use as an inventory host item

Role Variables
--------------

See [defaults/main.yml](defaults/main.yml)

Dependencies
------------

None

Installation
------------

### Ansible Galaxy (recommended)

```bash
$ cd path/to/playbook/root
$ cat >> requirements.yaml <<EOF
- src: "https://github.com/martinboy/ansible-role-add-inventory-host"
  name: "martinboy.add-inventory-host"
EOF
$ ansible-galaxy install -r requirements.yaml
```


Example Playbook
----------------


```yaml
---

# Gather new host's inventory variables in interactive mode
# 
# Make sure that all variables registered in "vars_prompt" section
# are defined in "set_fact" module in "tasks" section of the current play
- name: Enter Host Variables
  hosts: localhost
  vars_prompt:
    - name: "host_name"
      prompt: Enter host name
      private: no

    - name: "ansible_ssh_host"
      prompt: Enter 'ansible_ssh_host' var
      private: no

    - name: "ansible_ssh_user"
      prompt: Enter 'ansible_ssh_user' var
      default: root
      private: no

    - name: "ansible_become_user"
      prompt: Enter 'ansible_become_user' var
      private: no

    - name: "ansible_become_pass"
      prompt: Enter 'ansible_become_pass' var
      private: yes

    - name: "ansible_ssh_port"
      prompt: Enter 'ansible_ssh_port' var
      default: 22
      private: no

    - name: "ansible_ssh_private_key_file"
      prompt: Enter 'ansible_ssh_private_key_file' var
      private: no

  pre_tasks:
    - set_fact: 
        add_host_inventory_vars: 
          - { key: host_name, value: "{{ host_name }}" }
          - { key: ansible_ssh_host, value: "{{ ansible_ssh_host }}" }
          - { key: ansible_ssh_user, value: "{{ ansible_ssh_user }}" }
          - { key: ansible_become_user, value: "{{ ansible_become_user }}" }
          - { key: ansible_become_pass, value: "{{ ansible_become_pass }}" }
          - { key: ansible_ssh_port, value: "{{ ansible_ssh_port }}" }
          - { key: ansible_ssh_private_key_file, value: "{{ ansible_ssh_private_key_file }}" }
        host: "{{ host_name }}"
    - debug: msg="1. Host {{ host_name }} is found"
      when: host_name in hostvars
    - add_host: hostname={{ host_name }}
                ansible_ssh_host={{ ansible_ssh_host }}
                ansible_ssh_port={{ ansible_ssh_port }}
                ansible_ssh_user={{ ansible_ssh_user }}
                ansible_become_user={{ ansible_become_user }}
                ansible_become_pass={{ ansible_become_pass }}
                ansible_ssh_private_key_file={{ ansible_ssh_private_key_file }}
    - debug: msg="2. Host {{ host_name }} is found"
      when: host_name in hostvars



  roles:
    - { role: add_host, add_host_file: "{{ inventory_dir }}/hosts" }
```

License
-------

See LICENSE file for details.

Author Information
------------------

https://github.com/martinboy
