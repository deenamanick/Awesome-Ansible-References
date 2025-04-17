# **Amazon Linux Kernel Patching Automation**

## **Overview**
This Ansible playbook automates the process of patching the kernel on Amazon Linux instances. It handles pre-checks, kernel installation, and post-installation verification, including automatic reboots when required.

## **Features**
✅ **Pre-Patching Checks**  
- Verifies critical services are stopped (`httpd`, `mysql`, `postgresql`)  
- Checks available disk space (minimum 1GB required)  
- Validates target kernel version availability  

✅ **Kernel Installation**  
- Uses `amazon-linux-extras` to install the specified kernel version  
- Supports custom kernel version targeting (default: `kernel-5.10`)  

✅ **Post-Patching Verification**  
- Confirms successful kernel installation  
- Checks if a reboot is required  
- Automatically reboots and waits for system recovery  
- Reports old and new kernel versions  

---

## **Prerequisites**
### **1. Ansible Setup**
- Ansible installed on the control node  
- SSH access to target Amazon Linux instances  

### **2. Target Host Requirements**
- Amazon Linux (2 or 2023)  
- `sudo` privileges for the Ansible user  
- Internet access to fetch kernel packages  

---

## **Usage**
### **1. Basic Execution**
```sh
ansible-playbook kernel_patching.yml
```

### **2. Custom Kernel Version**
Override the default kernel version:
```sh
ansible-playbook kernel_patching.yml -e "target_kernel_version=kernel-5.15"
```

### **3. Dry Run (Check Mode)**
Simulate changes without applying them:
```sh
ansible-playbook kernel_patching.yml --check
```

### **4. Limit to Specific Host**
Run only on a single host:
```sh
ansible-playbook kernel_patching.yml -l worker1
```

---

## **Variables**
| Variable | Default | Description |
|----------|---------|-------------|
| `required_services` | `['httpd', 'mysql', 'postgresql']` | Services checked before patching |
| `max_reboot_wait_minutes` | `5` | Maximum wait time after reboot |
| `target_kernel_version` | `kernel-5.10` | Kernel version to install |

---

## **Logs & Troubleshooting**
### **Generated Logs**
- **Pre-checks:** `pre_test.log`  
- **Post-install verification:** `post_test.log`  

### **Common Issues**
❌ **"Requested kernel version not available"**  
→ Verify available kernels with:  
```sh
amazon-linux-extras list | grep kernel
```

❌ **"Insufficient disk space"**  
→ Ensure at least **1GB** free space on `/`  

❌ **"Services still running"**  
→ Manually stop services before retrying:  
```sh
sudo systemctl stop httpd mysql postgresql
```

---

## **Example Output**
```plaintext
TASK [Display patching results] ***************************************
ok: [server01] => {
    "msg": "Patching complete!\nOld kernel: 4.14.294-200.489.amzn2.x86_64\nNew kernel: 5.10.179-200.489.amzn2.x86_64\nReboot status: reboot_required\nTarget kernel version: kernel-5.10\nKernel package verified: kernel-5.10.179-200.489.amzn2"
}
```

---

## **Best Practices**
🔹 **Test in staging first** before production rollout.  
🔹 **Monitor after reboot** for any service issues.  
🔹 **Use tags** for selective task execution (e.g., `--tags=verify,install`).  

---

#### Make sure you have already setup the ansible master, worker nodes. 

## Patching_script.sh

```
#!/bin/bash

# Test script for kernel patching playbook

# Variables
TEST_HOST="worker1"       # Change this to your target host
PLAYBOOK="kernel_patching.yml"  # Your playbook filename
PRE_TEST_LOG="pre_test.log"
POST_TEST_LOG="post_test.log"

# Function to run tests
run_test() {
  echo -e "\n=== TEST: $description ==="
  eval "$command"
}

# Pre-test checks
echo "Running pre-test checks..." > $PRE_TEST_LOG
run_test "Inventory check" "ansible --list-hosts worker1" | tee -a $PRE_TEST_LOG
run_test "Ping test" "ansible worker1 -m ping" | tee -a $PRE_TEST_LOG
run_test "Current kernel versions" "ansible worker1 -m shell -a 'uname -r'" | tee -a $PRE_TEST_LOG

# Dry run
run_test "Dry run check" "ansible-playbook  $PLAYBOOK --check" | tee -a $PRE_TEST_LOG

# Actual test
echo -e "\n=== RUNNING ACTUAL PATCHING TEST ==="
run_test "Execute playbook" "ansible-playbook  $PLAYBOOK" | tee $POST_TEST_LOG

# Post-test verification
echo -e "\n=== POST-TEST VERIFICATION ==="
run_test "Verify new kernel" "ansible  $TEST_HOST -m shell -a 'uname -r'" | tee -a $POST_TEST_LOG
run_test "Check yum history" "ansible  $TEST_HOST -m shell -a 'yum history info kernel'" | tee -a $POST_TEST_LOG
run_test "Verify services" "ansible  $TEST_HOST -m shell -a 'systemctl list-units --state=failed'" | tee -a $POST_TEST_LOG

echo -e "\n=== TEST SUMMARY ==="
echo "Pre-test log: $PRE_TEST_LOG"
echo "Post-test log: $POST_TEST_LOG"
grep -E 'changed=|failed=' $POST_TEST_LOG

```

### Ansible kernel patching script

```
---
- name: Kernel Patching Playbook for Amazon Linux
  hosts: worker1
  become: true
  become_user: root
  
  vars:
    required_services: ['httpd', 'mysql', 'postgresql']  # Add other critical services
    max_reboot_wait_minutes: 5
    target_kernel_version: "kernel-5.10"  # Specify the kernel version to install
    
  tasks:
    - name: Verify critical services are stopped
      block:
        - name: Check running processes
          shell: |
            if ps -eaf | grep -E '{{ required_services|join("|") }}' | grep -v grep > /dev/null; then
              echo "process_running"
            else
              echo "process_not_running"
            fi
          register: app_proc_check
          changed_when: false
          
        - debug: 
            msg: "Process check result: {{ app_proc_check.stdout }}"
            verbosity: 1
            
        - name: Fail if services are running
          fail:
            msg: "{{ inventory_hostname }} has running services ({{ required_services }}). Please stop them before patching."
          when: app_proc_check.stdout == "process_running"
          
      rescue:
        - name: Handle process check failure
          debug:
            msg: "Error checking processes, proceeding with caution"
          when: app_proc_check is failed

    - name: Verify available disk space (min 1GB)
      shell: >
        if [ $(df -k / | awk 'NR==2 {print $4}') -lt 1048576 ]; then
          echo "disk_space_low";
        else 
          echo "disk_space_ok";
        fi
      register: disk_check
      changed_when: false
      ignore_errors: yes
      
    - name: Abort if low disk space
      fail:
        msg: "Insufficient disk space on {{ inventory_hostname }} for patching"
      when: disk_check.stdout == "disk_space_low"

    - name: Get current kernel version
      shell: uname -r
      register: curr_ker
      changed_when: false

    - name: Display current kernel info
      debug:
        msg: "Current kernel: {{ curr_ker.stdout }}"
        
    - name: Check available kernel versions
      shell: amazon-linux-extras list | grep kernel
      register: available_kernels
      changed_when: false
      
    - name: Display available kernel versions
      debug:
        msg: "Available kernel versions: {{ available_kernels.stdout_lines }}"

    - name: Verify target kernel version is available
      fail:
        msg: "Requested kernel version {{ target_kernel_version }} is not available"
      when: target_kernel_version not in available_kernels.stdout

    - name: Install specified kernel version
      shell: amazon-linux-extras install {{ target_kernel_version }} -y
      register: kernel_install
      
    - name: Verify kernel package installation
      shell: rpm -qa kernel | grep {{ target_kernel_version.split('-')[1] }}
      register: kernel_verify
      failed_when: kernel_verify.rc != 0
      
    - name: Check if reboot is required
      shell: |
        kernel_new=$(rpm -q --last kernel | head -1 | awk '{print $1}' | sed 's/kernel-//')
        kernel_now=$(uname -r)
        if [[ "$kernel_new" != "$kernel_now" ]]; then
          echo "reboot_required"
        else
          echo "no_reboot_needed"
        fi
      register: reboot_check
      changed_when: false
      
    - name: Reboot if needed
      block:
        - name: Initiate reboot
          reboot:
            msg: "Reboot initiated by Ansible after kernel update"
            connect_timeout: 5
            reboot_timeout: 600
            pre_reboot_delay: 30
            post_reboot_delay: 30
            
        - name: Wait for system to come back online
          wait_for_connection:
            delay: 60
            timeout: "{{ max_reboot_wait_minutes * 60 }}"
            
      when: reboot_check.stdout == "reboot_required"
      
    - name: Verify new kernel version
      shell: uname -r
      register: new_ker
      changed_when: false
      
    - name: Display patching results
      debug:
        msg: |
          Patching complete!
          Old kernel: {{ curr_ker.stdout }}
          New kernel: {{ new_ker.stdout }}
          Reboot status: {{ reboot_check.stdout }}
          Target kernel version: {{ target_kernel_version }}
          Kernel package verified: {{ kernel_verify.stdout }}

```




