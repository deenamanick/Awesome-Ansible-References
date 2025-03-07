Mastering Ansible by solving **50 Ansible playbook tasks** is an excellent way to gain hands-on experience and deepen your understanding of Ansible's capabilities. Below is a structured roadmap to help you tackle these tasks, along with examples and key concepts to focus on.

---

## **1. Beginner-Level Tasks (10 Tasks)**  
Focus on basic playbook structure, modules, and simple automation tasks.

### **Tasks**:
Here are the **Ansible playbooks** for each task:  

---

### **1. Install `nginx` on a remote server**  
```yaml
---
- name: Install nginx
  hosts: all
  become: yes
  tasks:
    - name: Install nginx on Debian-based systems
      apt:
        name: nginx
        state: present
      when: ansible_os_family == "Debian"

    - name: Install nginx on RHEL-based systems
      yum:
        name: nginx
        state: present
      when: ansible_os_family == "RedHat"
```

---

### **2. Start and enable the `nginx` service**
```yaml
---
- name: Start and enable nginx
  hosts: all
  become: yes
  tasks:
    - name: Ensure nginx is started and enabled
      service:
        name: nginx
        state: started
        enabled: yes
```

---

### **3. Create a user and set a password**
```yaml
---
- name: Create a user and set password
  hosts: all
  become: yes
  tasks:
    - name: Create a user
      user:
        name: myuser
        password: "{{ 'MySecurePassword' | password_hash('sha512') }}"
        shell: /bin/bash
        state: present
```

---

### **4. Copy a file to a remote server**
```yaml
---
- name: Copy a file to remote server
  hosts: all
  become: yes
  tasks:
    - name: Copy file
      copy:
        src: /path/to/local/file.txt
        dest: /remote/path/file.txt
        mode: '0644'
```

---

### **5. Install multiple packages (`vim`, `curl`, `git`)**
```yaml
---
- name: Install multiple packages
  hosts: all
  become: yes
  tasks:
    - name: Install packages on Debian-based systems
      apt:
        name:
          - vim
          - curl
          - git
        state: present
      when: ansible_os_family == "Debian"

    - name: Install packages on RHEL-based systems
      yum:
        name:
          - vim
          - curl
          - git
        state: present
      when: ansible_os_family == "RedHat"
```

---

### **6. Create a directory and set permissions**
```yaml
---
- name: Create a directory and set permissions
  hosts: all
  become: yes
  tasks:
    - name: Create directory
      file:
        path: /opt/mydir
        state: directory
        mode: '0755'
```
![image](https://github.com/user-attachments/assets/42a06834-fde6-4966-bd12-909f9a13e585)

---

### **7. Check disk usage on remote servers**
```yaml
---
- name: Check disk usage
  hosts: all
  become: yes
  tasks:
    - name: Run df command
      command: df -h
      register: disk_usage

    - name: Show disk usage
      debug:
        msg: "{{ disk_usage.stdout_lines }}"
```
![image](https://github.com/user-attachments/assets/b0a67d1b-cf92-4cfe-b6cf-66a4b44901d0)

---

### **8. Run a shell command (`uptime`)**
```yaml
---
- name: Run a shell command
  hosts: all
  become: yes
  tasks:
    - name: Get system uptime
      command: uptime
      register: uptime_output

    - name: Display uptime
      debug:
        msg: "{{ uptime_output.stdout }}"
```
![image](https://github.com/user-attachments/assets/eaa6280f-7029-4979-8e1b-d6352801dc8d)

---

### **9. Fetch system information (`ansible_facts`)**
```yaml
---
- name: Fetch system information
  hosts: all
  tasks:
    - name: Get system information
      setup:

    - name: Show system information
      debug:
        var: ansible_facts
```

---

### **10. Use the `debug` module to print variables**
```yaml
---
- name: Debug example
  hosts: all
  tasks:
    - name: Set a variable
      set_fact:
        my_var: "Hello, Ansible!"

    - name: Print the variable
      debug:
        msg: "{{ my_var }}"
```
![image](https://github.com/user-attachments/assets/407b1e36-62d1-4fdb-8d20-42827864a39e)


---

## **2. Intermediate-Level Tasks (20 Tasks)**  
Focus on variables, loops, conditionals, and more complex automation.


---

### **11. Use Variables to Dynamically Set the Package Name**
```yaml
---
- name: Install a package using a variable
  hosts: all
  become: yes
  vars:
    package_name: wget
  tasks:
    - name: Install the package
      apt:
        name: "{{ package_name }}"
        state: present
      when: ansible_os_family == "Debian"

    - name: Install the package
      apt:
        name: "{{ package_name }}"
        state: present
      when: ansible_os_family == "redhat"
```
![image](https://github.com/user-attachments/assets/e57bc7be-2331-4dff-a6e3-3a178e8661a3)



---

### **12. Install Different Packages Based on the OS Type**
```yaml
---
- name: Install packages based on OS type
  hosts: all
  become: yes
  tasks:
    - name: Install packages on Debian/Ubuntu
      apt:
        name:
          - vim
          - curl
        state: present
      when: ansible_facts['os_family'] == "Debian"

    - name: Install packages on RedHat/CentOS
      yum:
        name:
          - vim
          - curl
        state: present
      when: ansible_facts['os_family'] == "RedHat"
```

---

### **13. Use Loops to Create Multiple Users**
```yaml
---
- name: Create multiple users
  hosts: all
  become: yes
  tasks:
    - name: Create users
      user:
        name: "{{ item }}"
        state: present
      loop:
        - alice
        - bob
        - charlie
```
![image](https://github.com/user-attachments/assets/f9129be2-4ea1-4c20-9e44-22f2eb4c88f8)

---

### **14. Deploy a Static Website Using the `copy` Module**
```yaml
---
- name: Deploy a static website
  hosts: all
  tasks:
    - name: Copy website files
      copy:
        src: /path/to/local/website/
        dest: /var/www/html/
        owner: www-data
        group: www-data
        mode: '0644'
```

---

### **15. Use Conditionals to Install Packages Only If Not Already Installed**
```yaml
---
- name: Install nginx if not installed
  hosts: all
  become: yes
  tasks:
    - name: Gather installed package facts
      package_facts:
        manager: auto

    - name: Install nginx if not installed
      yum:
        name: nginx
        state: present
      when: "'nginx' not in ansible_facts.packages"
```
![image](https://github.com/user-attachments/assets/540e343e-fe3d-4387-ad69-b0ff39a68d9c)

---

### **16. Configure a Firewall (e.g., `ufw` or `firewalld`)**
```yaml
---
- name: Configure firewall
  hosts: all
  become: yes
  tasks:
    - name: Allow SSH through UFW
      ufw:
        rule: allow
        name: OpenSSH

    - name: Enable UFW
      ufw:
        state: enabled
```

---

### **17. Use the `template` Module to Generate Configuration Files**

#### template nginx.conf.j2
```
user  nginx;
worker_processes  auto;

error_log  /var/log/nginx/error.log warn;
pid        /var/run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    include       /etc/nginx/mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  /var/log/nginx/access.log  main;

    sendfile        on;
    keepalive_timeout  65;

    server {
        listen 80;
        server_name {{ server_name | default('localhost') }};

        location / {
            root   /usr/share/nginx/html;
            index  index.html index.htm;
        }

        error_page 404 /404.html;
        location = /404.html {
            root /usr/share/nginx/html;
        }

        error_page 500 502 503 504 /50x.html;
        location = /50x.html {
            root /usr/share/nginx/html;
        }
    }
}


```

```yaml
---
- name: Generate nginx configuration
  hosts: all
  become: yes
  tasks:
    - name: Create nginx.conf from template
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
        owner: root
        group: root
        mode: '0644'
```
![image](https://github.com/user-attachments/assets/c19741b2-5d66-4e40-b908-653340cbd3d7)

---

### **18. Restart a Service Only If a Configuration File Changes**
```yaml
---
- name: Restart nginx if config changes
  hosts: all
  become: yes
  gather_facts: yes  # Ensure facts are gathered

  tasks:
    - name: Install packages (RedHat-based systems)
      package:  # Use 'package' instead of 'yum' for compatibility
        name:
          - nginx
        state: present
      when: ansible_facts['os_family'] == "RedHat"

    - name: Copy nginx configuration
      template:  # Use 'template' instead of 'copy' if using a Jinja2 template
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
        owner: root
        group: root
        mode: '0644'
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

---

### **19. Use the `lineinfile` Module to Add or Replace a Line in a File**
```yaml
---
- name: Add a line to a file
  hosts: all
  tasks:
    - name: Ensure a line exists in a file
      lineinfile:
        path: /etc/hosts
        line: "127.0.0.1 localhost"
        state: present
```

---

### **20. Clone a Git Repository and Deploy It**
```yaml
---
- name: Clone and deploy a Git repository
  hosts: all
  tasks:
    - name: Clone repository
      git:
        repo: https://github.com/example/repo.git
        dest: /var/www/html/
        version: main
```

---

### **21. Use the `unarchive` Module to Extract a Tarball**
```yaml
---
- name: Extract a tarball
  hosts: all
  tasks:
    - name: Unarchive a tarball
      unarchive:
        src: /path/to/file.tar.gz
        dest: /path/to/destination/
        remote_src: yes
```

---

### **22. Schedule a Cron Job**
```yaml
---
- name: Schedule a cron job
  hosts: all
  tasks:
    - name: Add a cron job
      cron:
        name: "Backup script"
        minute: "0"
        hour: "2"
        job: "/path/to/backup.sh"
```

---

### **23. Use `yum` and `apt` Modules for Different OS Types**
```yaml
---
- name: Install packages on different OS types
  hosts: all
  become: yes
  tasks:
    - name: Install packages on Debian/Ubuntu
      apt:
        name: nginx
        state: present
      when: ansible_facts['os_family'] == "Debian"

    - name: Install packages on RedHat/CentOS
      yum:
        name: nginx
        state: present
      when: ansible_facts['os_family'] == "RedHat"
```

---

### **24. Configure SSH Key-Based Authentication**
```yaml
---
- name: Configure SSH key-based authentication
  hosts: all
  tasks:
    - name: Copy SSH public key
      authorized_key:
        user: ubuntu
        state: present
        key: "{{ lookup('file', '/path/to/public_key.pub') }}"
```

---

### **25. Use the Hosts File to Group Servers and Run Tasks on Specific Groups**
```yaml
---
- name: Run tasks on specific groups
  hosts: webservers
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

---

### **26. Back Up Files Before Making Changes**
```yaml
---
- name: Back up files
  hosts: all
  tasks:
    - name: Create a backup directory
      file:
        path: /backup
        state: directory

    - name: Backup a file
      copy:
        src: /etc/nginx/nginx.conf
        dest: /backup/nginx.conf.backup
```

---

### **27. Use the `register` Module to Capture the Output of a Command**
```yaml
---
- name: Capture command output
  hosts: all
  tasks:
    - name: Run a command
      command: ls -l /home
      register: command_output

    - name: Print command output
      debug:
        var: command_output.stdout
```

---

### **28. Check If a Port Is Open on a Remote Server**
```yaml
---
- name: Check if a port is open
  hosts: all
  tasks:
    - name: Check port 80
      wait_for:
        port: 80
        timeout: 5
```

---

### **29. Use the `stat` Module to Check If a File Exists**
```yaml
---
- name: Check if a file exists
  hosts: all
  tasks:
    - name: Check file existence
      stat:
        path: /etc/nginx/nginx.conf
      register: file_stat

    - name: Print file existence
      debug:
        var: file_stat.stat.exists
```

---

### **30. Configure a Load Balancer (e.g., `haproxy`)**
```yaml
---
- name: Configure HAProxy
  hosts: all
  become: yes
  tasks:
    - name: Install HAProxy
      apt:
        name: haproxy
        state: present

    - name: Configure HAProxy
      template:
        src: haproxy.cfg.j2
        dest: /etc/haproxy/haproxy.cfg
        owner: root
        group: root
        mode: '0644'
      notify: Restart HAProxy

  handlers:
    - name: Restart HAProxy
      service:
        name: haproxy
        state: restarted
```

---

Let me know if you need further clarification or additional examples! 🚀

---

## **3. Advanced-Level Tasks (20 Tasks)**  
Focus on roles, handlers, vaults, and complex workflows.

### **Tasks**:
31. Create an Ansible role to set up a web server.
32. Use handlers to restart a service only when notified.
33. Write a playbook to encrypt sensitive data using Ansible Vault.
34. Use `tags` to run specific tasks in a playbook.
35. Write a playbook to deploy a multi-tier application (e.g., web, app, and database servers).
36. Use the `block` module to group tasks and handle errors.
37. Write a playbook to configure a database server (e.g., MySQL or PostgreSQL).
38. Use the `include_tasks` module to modularize your playbook.
39. Write a playbook to configure a Docker host and deploy containers.
40. Use the `set_fact` module to create custom facts.
41. Write a playbook to configure a Kubernetes cluster.
42. Use the `wait_for` module to wait for a service to start.
43. Write a playbook to configure logging (e.g., `rsyslog`).
44. Use the `assert` module to validate conditions.
45. Write a playbook to configure a monitoring tool (e.g., Prometheus).
46. Use the `slurp` module to fetch files from remote servers.
47. Write a playbook to configure a CI/CD pipeline (e.g., Jenkins).
48. Use the `uri` module to interact with REST APIs.
49. Write a playbook to configure a message queue (e.g., RabbitMQ).
50. Use the `community.general` collection to extend Ansible's functionality.

---

## **Key Concepts to Master**:
1. **Playbook Structure**: Learn how to write clean and organized playbooks.
2. **Modules**: Understand commonly used modules like `yum`, `apt`, `copy`, `template`, and `service`.
3. **Variables**: Use variables to make playbooks dynamic and reusable.
4. **Loops and Conditionals**: Automate repetitive tasks and handle different scenarios.
5. **Roles**: Organize playbooks into reusable roles.
6. **Handlers**: Trigger tasks only when notified (e.g., restarting a service).
7. **Ansible Vault**: Encrypt sensitive data like passwords and keys.
8. **Error Handling**: Use `block`, `rescue`, and `always` to handle errors.
9. **Idempotency**: Ensure playbooks can be run multiple times without causing issues.
10. **Collections**: Use community and custom collections to extend Ansible's functionality.

---

## **Tips for Success**:
- **Practice Regularly**: Solve at least 1-2 tasks daily to build momentum.
- **Use Documentation**: Refer to the [Ansible Documentation](https://docs.ansible.com/) for module details and examples.
- **Experiment**: Try modifying tasks to explore different approaches.
- **Join Communities**: Engage with the Ansible community on forums like Reddit, Stack Overflow, or the Ansible mailing list.
- **Automate Real-World Scenarios**: Apply your skills to automate tasks in your own environment.

---

By completing these **50 tasks**, you'll gain a deep understanding of Ansible and be able to automate complex infrastructure workflows with confidence. Happy automating! 🚀
