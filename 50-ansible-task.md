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

### **Tasks**:
11. Use variables to dynamically set the package name in a playbook.
12. Write a playbook to install different packages based on the OS type.
13. Use loops to create multiple users.
14. Write a playbook to deploy a static website using the `copy` module.
15. Use conditionals to install packages only if they are not already installed.
16. Write a playbook to configure a firewall (e.g., `ufw` or `firewalld`).
17. Use the `template` module to generate configuration files (e.g., `nginx.conf`).
18. Write a playbook to restart a service only if a configuration file changes.
19. Use the `lineinfile` module to add or replace a line in a file.
20. Write a playbook to clone a Git repository and deploy it.
21. Use the `unarchive` module to extract a tarball on remote servers.
22. Write a playbook to schedule a cron job.
23. Use the `yum` and `apt` modules to handle package installation on different OS types.
24. Write a playbook to configure SSH key-based authentication.
25. Use the `hosts` file to group servers and run tasks on specific groups.
26. Write a playbook to back up files before making changes.
27. Use the `register` module to capture the output of a command.
28. Write a playbook to check if a port is open on a remote server.
29. Use the `stat` module to check if a file exists.
30. Write a playbook to configure a load balancer (e.g., `haproxy`).

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
