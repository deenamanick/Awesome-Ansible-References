# Example Usage in Playbook

```
---
- name: Configure Web Server
  hosts: web
  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

- name: Configure Development Tools
  hosts: dev
  tasks:
    - name: Install Git
      apt:
        name: git
        state: present

- name: Configure Production Server
  hosts: prod
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
```
### inventory.ini
```
[workers]
worker1 ansible_host=192.168.59.25 ansible_user=vagrant
worker2 ansible_host=192.168.59.26 ansible_user=vagrant
worker3 ansible_host=192.168.59.27 ansible_user=vagrant
worker4 ansible_host=192.168.59.28 ansible_user=vagrant

[web]
worker2

[dev]
worker3

[prod]
worker4
```
### Explanation:
#### Group Definitions:

[workers]: All worker nodes.
[web]: Contains worker2 for web tasks.
[dev]: Contains worker3 for development tasks.
[prod]: Contains worker4 for production tasks.
Use Case:

You can now target the web, dev, or prod groups specifically in your playbooks or ad-hoc commands.
