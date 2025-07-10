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

### ✅ **Verification Tasks **

1. **Check if Nginx is installed**

   * Run:

     ```bash
     nginx -v
     ```
   * Task: Confirm the installed version of Nginx.

2. **Check if the Nginx service is running**

   * Run:

     ```bash
     sudo systemctl status nginx
     ```
   * Task: Validate that Nginx is **active (running)**.

3. **Check the port Nginx is listening on**

   * Run:

     ```bash
     sudo netstat -tuln | grep :80
     ```
 
4. **Check web page response**

   * Run:

     ```bash
     curl http://localhost
     ```
   * Task: Ensure it returns the Nginx default web page.

5. **Check service is enabled on boot**

   * Run:

     ```bash
     sudo systemctl is-enabled nginx
     ```
   * Task: Verify that the Nginx service is set to start on boot.


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


---

## **3. Advanced-Level Tasks (20 Tasks)**  
Focus on roles, handlers, vaults, and complex workflows.

### **Tasks**:Here’s the code and explanations for each of the tasks you mentioned. Each task is written as a separate Ansible playbook or role.

---

### **31. Create an Ansible Role to Set Up a Web Server**
1. **Create the Role Structure**:
   ```bash
   ansible-galaxy init webserver
   ```
   This creates a directory structure like:
   ```
   webserver/
   ├── tasks
   │   └── main.yml
   ├── handlers
   │   └── main.yml
   ├── templates
   ├── files
   ├── vars
   │   └── main.yml
   ├── defaults
   │   └── main.yml
   └── meta
       └── main.yml
   ```

2. **Define Tasks** (`webserver/tasks/main.yml`):

### (`webserver/templates/nginx.conf.j2`)

```
worker_processes auto;
events {
    worker_connections 1024;
}
http {
    server {
        listen 80;
        server_name localhost;

        location / {
            root /usr/share/nginx/html;
            index index.html;
        }
    }
}

```

   ```yaml
   ---
   - name: Install nginx on debian bases system
     apt:
       name: nginx
       state: present
     when: ansible_os_family == "Debian"
   - name: Install nginx on RHEL bases system
     yum:
        name: nginx
        state: absent
     when: ansible_os_family == "RedHat"


   - name: Copy nginx configuration
     template:
       src: nginx.conf.j2
       dest: /etc/nginx/nginx.conf
     notify: Restart nginx
   ```

3. **Define Handlers** (`webserver/handlers/main.yml`):
   ```yaml
   ---
   - name: Restart nginx
     service:
       name: nginx
       state: restarted
   ```

4. **Use the Role in a Playbook**:
   ```yaml
   ---
   - name: Set up a web server
     hosts: all
     become: yes
     roles:
       - webserver
   ```
 5. ** Run the playbook **
   ```
   ansible-playbook site.yml

```
---

### **32. Use Handlers to Restart a Service Only When Notified**
```yaml
---
- name: Restart a service when notified
  hosts: all
  tasks:
    - name: Copy configuration file
      copy:
        src: /path/to/config.conf
        dest: /etc/service/config.conf
      notify: Restart service

  handlers:
    - name: Restart service
      service:
        name: my_service
        state: restarted
```

---

### **33. Encrypt Sensitive Data Using Ansible Vault**
1. **Create an Encrypted File**:
   ```bash
   ansible-vault create secrets.yml
   ```
   Add sensitive data:
   ```yaml
   db_password: mysecretpassword
   ```

2. **Use the Encrypted File in a Playbook**:
   ```yaml
   ---
   - name: Use encrypted data
     hosts: all
     vars_files:
       - secrets.yml
     tasks:
       - name: Print secret
         debug:
           msg: "Database password is {{ db_password }}"
   ```




3. **Run the Playbook**:
   ```bash
   ansible-playbook playbook.yml --ask-vault-pass
   ```
![image](https://github.com/user-attachments/assets/f8ec11fc-a281-4da1-a5fe-bfddf05fc876)
---

### **34. Use Tags to Run Specific Tasks**
```yaml
---
- name: Use tags
  hosts: all
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
      tags: install

    - name: Start nginx
      service:
        name: nginx
        state: started
      tags: start
```

Run tasks with a specific tag:
```bash
ansible-playbook playbook.yml --tags install
```

---

### **35. Deploy a Multi-Tier Application**
```yaml
---
- name: Deploy web tier
  hosts: webservers
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present

- name: Deploy app tier
  hosts: appservers
  tasks:
    - name: Install Python
      apt:
        name: python3
        state: present

- name: Deploy database tier
  hosts: dbservers
  tasks:
    - name: Install MySQL
      apt:
        name: mysql-server
        state: present
```

---

### **36. Use the `block` Module to Group Tasks and Handle Errors**
```yaml
---
- name: Handle errors with block
  hosts: all
  tasks:
    - block:
        - name: Install nginx
          apt:
            name: nginx
            state: present

        - name: Start nginx
          service:
            name: nginx
            state: started

      rescue:
        - name: Handle failure
          debug:
            msg: "Something went wrong!"
```

---

### **37. Configure a Database Server (e.g., MySQL or PostgreSQL)**
```yaml
---
- name: Configure MySQL
  hosts: dbservers
  become: yes
  tasks:
    - name: Install MySQL
      apt:
        name: mysql-server
        state: present

    - name: Start MySQL
      service:
        name: mysql
        state: started
        enabled: yes
```

---

### **38. Use the `include_tasks` Module to Modularize Your Playbook**
1. **Create a Task File** (`tasks/install_nginx.yml`):
   ```yaml
   ---
   - name: Install nginx
     apt:
       name: nginx
       state: present
   ```

2. **Include the Task in a Playbook**:
   ```yaml
   ---
   - name: Use include_tasks
     hosts: all
     tasks:
       - include_tasks: tasks/install_nginx.yml
   ```

---

### **39. Configure a Docker Host and Deploy Containers**
```yaml
---
- name: Configure Docker host
  hosts: all
  become: yes
  tasks:
    - name: Install Docker
      apt:
        name: docker.io
        state: present

    - name: Start Docker
      service:
        name: docker
        state: started
        enabled: yes

    - name: Deploy a container
      docker_container:
        name: my_container
        image: nginx
        state: started
```

---

### **40. Use the `set_fact` Module to Create Custom Facts**
```yaml
---
- name: Use set_fact
  hosts: all
  tasks:
    - name: Set a custom fact
      set_fact:
        my_fact: "Hello, Ansible!"

    - name: Print custom fact
      debug:
        var: my_fact
```

---

### **41. Configure a Kubernetes Cluster**
```yaml
---
- name: Configure Kubernetes
  hosts: all
  become: yes
  tasks:
    - name: Install kubeadm, kubelet, and kubectl
      apt:
        name:
          - kubeadm
          - kubelet
          - kubectl
        state: present
```

---

### **42. Use the `wait_for` Module to Wait for a Service to Start**
```yaml
---
- name: Wait for a service to start
  hosts: all
  tasks:
    - name: Wait for port 80
      wait_for:
        port: 80
        timeout: 60
```

---

### **43. Configure Logging (e.g., `rsyslog`)**
```yaml
---
- name: Configure rsyslog
  hosts: all
  become: yes
  tasks:
    - name: Install rsyslog
      apt:
        name: rsyslog
        state: present

    - name: Configure rsyslog
      template:
        src: rsyslog.conf.j2
        dest: /etc/rsyslog.conf
      notify: Restart rsyslog

  handlers:
    - name: Restart rsyslog
      service:
        name: rsyslog
        state: restarted
```

---

### **44. Use the `assert` Module to Validate Conditions**
```yaml
---
- name: Validate conditions
  hosts: all
  tasks:
    - name: Check if OS is Ubuntu
      assert:
        that:
          - ansible_facts['distribution'] == "Ubuntu"
        fail_msg: "This playbook only runs on Ubuntu."
```

---

### **45. Configure a Monitoring Tool (e.g., Prometheus)**
```yaml
---
- name: Configure Prometheus
  hosts: all
  become: yes
  tasks:
    - name: Install Prometheus
      apt:
        name: prometheus
        state: present
```

---

### **46. Use the `slurp` Module to Fetch Files from Remote Servers**
```yaml
---
- name: Fetch a file
  hosts: all
  tasks:
    - name: Read a file
      slurp:
        path: /etc/hosts
      register: file_content

    - name: Print file content
      debug:
        msg: "{{ file_content.content | b64decode }}"
```

---

### **47. Configure a CI/CD Pipeline (e.g., Jenkins)**
```yaml
---
- name: Configure Jenkins
  hosts: all
  become: yes
  tasks:
    - name: Install Jenkins
      apt:
        name: jenkins
        state: present
```

---

### **48. Use the `uri` Module to Interact with REST APIs**
```yaml
---
- name: Interact with a REST API
  hosts: all
  tasks:
    - name: Make a GET request
      uri:
        url: https://api.example.com/data
        method: GET
      register: response

    - name: Print response
      debug:
        var: response.json
```

---

### **49. Configure a Message Queue (e.g., RabbitMQ)**
```yaml
---
- name: Configure RabbitMQ
  hosts: all
  become: yes
  tasks:
    - name: Install RabbitMQ
      apt:
        name: rabbitmq-server
        state: present
```

---

### **50. Use the `community.general` Collection to Extend Ansible's Functionality**
1. **Install the Collection**:
   ```bash
   ansible-galaxy collection install community.general
   ```

2. **Use a Module from the Collection**:
   ```yaml
   ---
   - name: Use community.general
     hosts: all
     tasks:
       - name: Use the pacman module (from community.general)
         community.general.pacman:
           name: vim
           state: present
   ```

---

Let me know if you need further clarification or additional examples! 🚀

---


---

By completing these **50 tasks**, you'll gain a deep understanding of Ansible and be able to automate complex infrastructure workflows with confidence. Happy automating! 🚀
