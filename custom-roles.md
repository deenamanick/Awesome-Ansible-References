### Ansible Roles and Directory Structure

Ansible roles help organize playbooks and tasks into a standard, reusable directory structure. They allow you to group related configurations, making it easier to manage and reuse configurations across different environments or projects.


## Directory Structure of a Role

```
my_role/
├── tasks/            # Main tasks to be executed
│   └── main.yml      # Entry point for tasks
├── handlers/         # Handlers to restart/reload services
│   └── main.yml      
├── templates/        # Jinja2 templates for configuration files
│   └── example.j2    
├── files/            # Static files to be copied
│   └── example.conf  
├── vars/             # Role-specific variables
│   └── main.yml      
├── defaults/         # Default variables
│   └── main.yml      
├── meta/             # Role metadata (dependencies, authors)
│   └── main.yml      
├── tests/            # Test playbooks for the role
│   └── test.yml      
├── README.md         # Documentation for the role

```
## Creating and Using a Role

## 1. Role Creation

```
ansible-galaxy init my_role

```
## 2. Example Role: Install and Configure Nginx

### tasks/main.yml

```
---
- name: Install Nginx
  apt:
    name: nginx
    state: present

- name: Copy Nginx configuration
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart Nginx

```
### handlers/main.yml
```
---
- name: Restart Nginx
  service:
    name: nginx
    state: restarted

```

### templates/nginx.conf.j2

```
server {
    listen 80;
    server_name {{ server_name }};
    location / {
        root /var/www/html;
        index index.html index.htm;
    }
}

```
### vars/main.yml

```
---
server_name: example.com

```

### 3. Using the Role in a Playbook

Create a playbook that includes the role.

Playbook (site.yml):

```
Create a playbook that includes the role.

Playbook (site.yml):

```
### Running the Playbook

```
ansible-playbook -i inventory site.yml

```
