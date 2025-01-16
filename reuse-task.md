## To demonstrate how to reuse tasks in Ansible playbooks, you can create a separate file, group-tasks.yml, that contains common tasks. Then, you can include this file in multiple playbooks.

##### Here’s an example of how you can organize the common tasks in the group-tasks.yml file, and then reuse them in your playbooks.

group-tasks.yml
```
# group-tasks.yml
---
- name: Install common packages
  apt:
    name:
      - vim
      - curl
      - wget
    state: present

- name: Ensure firewall is installed and started
  service:
    name: ufw
    state: started
    enabled: yes

- name: Add a user to the system
  user:
    name: myuser
    state: present
    groups: sudo
    create_home: yes
    shell: /bin/bash
```
### Reusing group-tasks.yml in Your Playbooks

Now, you can include this common group-tasks.yml file in your main playbook by using the import_tasks or include_tasks directive.

#### Example 1: Including Common Tasks in Multiple Playbooks
```
# web-server-playbook.yml
---
- name: Configure Web Server
  hosts: web
  tasks:
    - import_tasks: group-tasks.yml
    - name: Install Apache
      apt:
        name: apache2
        state: present

```
```
# dev-server-playbook.yml
---
- name: Configure Development Tools
  hosts: dev
  tasks:
    - import_tasks: group-tasks.yml
    - name: Install Git
      apt:
        name: git
        state: present

```
```
# prod-server-playbook.yml
---
- name: Configure Production Server
  hosts: prod
  tasks:
    - import_tasks: group-tasks.yml
    - name: Install Nginx
      apt:
        name: nginx
        state: present

```


### How It Works
Common Tasks File (group-tasks.yml): Contains reusable tasks that can be shared across different playbooks.

Main Playbooks: In each of your playbooks (e.g., web-server-playbook.yml, dev-server-playbook.yml, prod-server-playbook.yml), you can use the import_tasks directive to import and reuse the tasks from the group-tasks.yml file.

import_tasks is used here because it includes the tasks at the time of playbook parsing, meaning the tasks are directly added to the list of tasks for that play.
Reusability: This way, if you need to change the common tasks (e.g., adding or removing a package), you only need to update group-tasks.yml, and all playbooks that import it will automatically get the changes.
