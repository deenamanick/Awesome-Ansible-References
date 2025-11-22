## 1. Variables: For Configuring and Customizing Playbooks
Purpose: Variables allow you to define dynamic, reusable values that can be customized for different environments, hosts, or scenarios.
### Why Use Them:
To avoid hardcoding values in your playbooks.
To make playbooks reusable across multiple environments (e.g., dev, test, prod).
To set defaults or override values for specific hosts or groups.
### Example Scenarios:
Defining software packages to install based on environment.
Configuring application-specific parameters, such as database URLs or ports.
Storing sensitive information like passwords (using Ansible Vault).

## 2. Facts: For Gathering System Information

Purpose: Facts are automatically collected data about managed hosts, such as their operating system, IP address, CPU, memory, and more.
### Why Use Them:
To make decisions dynamically during playbook execution (e.g., run specific tasks based on the OS).
To verify system states or configurations before applying changes.
To generate reports or debug information about your inventory.
### Example Scenarios:
Installing a package only on hosts running a specific OS (e.g., Debian or RedHat).
Using the IP address or hostname in configuration templates.
Checking disk space before deploying a new application.


## 3. Registers: For Storing Task Output
Purpose: Registers capture the output of a task, which can be used in subsequent tasks for decision-making or debugging.
### Why Use Them:
To handle and act on dynamic results from tasks (e.g., command output or API responses).
To validate if a task succeeded, changed the system, or failed.
To debug playbook execution by logging task outputs.
### Example Scenarios:
Running a shell command to check the status of a service and restarting it if necessary.
Capturing the output of a configuration script to validate its result.
Storing the response from an API call to configure other systems.

```
---
- name: Install Node.js and Validate Installation
  hosts: all
  vars:
    node_package: nodejs  # Define the package name for Node.js
    node_version_command: "node -v"  # Command to check Node.js version

  tasks:
    # Task 1: Install Node.js
    - name: Install Node.js
      apt:
        name: "{{ node_package }}"
        state: present
      become: true  # Ensure the task runs with elevated privileges

    # Task 2: Use Facts to Gather Host Information
    - name: Display Host Details
      debug:
        msg:
          - "Host: {{ inventory_hostname }}"
          - "OS Family: {{ ansible_facts['os_family'] }}"
          - "IP Address: {{ ansible_facts['default_ipv4']['address'] }}"

    # Task 3: Verify Node.js Installation Using Register
    - name: Check Node.js Version
      shell: "{{ node_version_command }}"
      register: node_version
      failed_when: node_version.rc != 0  # Fail if the command is not found

    - name: Display Node.js Version
      debug:
        msg: |
          Node.js version on {{ inventory_hostname }}:
          {{ node_version.stdout }}

    # Conditional Task: Install npm if Node.js is installed
    - name: Install npm (Node Package Manager)
      apt:
        name: npm
        state: present
      become: true
      when: node_version.stdout is defined


```

### Explanation:
Variables (vars):

node_package: Defines the Node.js package to install.
node_version_command: Command used to verify the installation of Node.js.
Facts:

Host information like OS family and IP address is displayed for verification using debug.
Registers:

The result of the node -v command is stored in node_version.
Ensures that Node.js is properly installed and logs the version.
Conditional Logic (when):

npm is installed only if Node.js is detected (i.e., node_version.stdout is defined).
Privilege Escalation:

become: true ensures tasks requiring root privileges, like installing packages, can run without issues.
