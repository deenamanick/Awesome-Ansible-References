## Loops: For Repeating Tasks Efficiently in Playbooks
Purpose: Loops allow you to repeat a task multiple times, reducing redundancy and improving efficiency when performing similar actions on multiple items.

### Why Use Them:

To avoid writing repetitive tasks for similar actions.
To simplify managing lists, dictionaries, or ranges in your playbook.
To dynamically iterate over items, such as users, packages, or files.

### Example Scenarios:

Installing multiple software packages.
Creating users or directories in bulk.
Copying or managing multiple configuration files.

### Example 1: Installing Multiple Packages

```
- name: Install multiple packages
  hosts: all
  tasks:
    - name: Install required packages
      apt:
        name: "{{ item }}"
        state: present
      loop:
        - git
        - curl
        - vim

```
### Example 2: Creating Multiple Users

```
- name: Create multiple users
  hosts: all
  tasks:
    - name: Add users to the system
      user:
        name: "{{ item.name }}"
        state: present
        shell: "{{ item.shell }}"
      loop:
        - { name: "user1", shell: "/bin/bash" }
        - { name: "user2", shell: "/bin/zsh" }

```
### Example 3:  Using a Loop with a Range

```
- name: Create numbered directories
  hosts: all
  tasks:
    - name: Create directories in /tmp
      file:
        path: "/tmp/dir{{ item }}"
        state: directory
      loop: "{{ range(1, 6) | list }}"

```


