## Collection of ansible Commands

```

ansible IP -m shell -a 'cat /etc/passwd | grep root'

ansible ip -m copy -a "src=/etc/passwd dest=/tmp/1.txt"

ansible ip -m copy -a "src=/etc/passwd dest=/tmp/1.txt owner=root group=root mode=0755"

ansible ip -m shell -a "/bin/bash /tmp/ansib.sh"

ansible ip -m cron -a "name='test_test' job='/bin/bash /usr/local/sbin/scrip.sh' weekday=6"

ansible ip -m cron -a "name='test_test' state=absent"

ansible 192.168.96.135 -m cron -a "name='test_test' job='/bin/bash /usr/local/sbin/1.sh' minute=15 hour=03 day=1,3,5 month=1,3,5 weekday=6"

install package
=============

ansible ip -m yum -a "name=httpd"

ansible ip -m service -a "name=httpd state=started enabled=yes"

ansible ip -m shell -a "echo test123 | passwd --stdin deena"

ansible all -m file -a "path=/tmp/hidir state=directory owner=nobody mode=777"

ansible all -m file -a "path=/tmp/mytest.txt state=absent"

ansible all -m get_url -a "url=http://tar.gz dest=/tmp"

ansible all -m yum -a "name=git state=latest"

ansible all -m yum -a "list=nginx"

ansible all -m service -a "name=nginx enabled=true state=started"

ansible testhost -m yum -a "name=httpd state=removed"

create_user.yaml
---
- name: create_user
  hosts: test
  user: root
  gather_facts: false
  vars:
    - user: "test"
  tasks:
    - name: create user
      user: name="{{ user }}"

ansible testhost -m shell -a "touch /tmp/{1,2,3}.txt"

playbook

---
- hosts: test
  user: root
  tasks:
    - name: create test files
      file: path=/tmp/{{ item }} state=touch mode=600
      with_items:
        - 1.txt
        - 2.txt
        - 3.txt




Restart apache task
----


---

- hosts: webservers

vars:

http_port: 80

max_clients: 200

remote_user: root

tasks:

- name: ensure apache is at the latest version

yum: pkg=httpd state=latest

- name: write the apache config file

template: src=/srv/httpd.j2 dest=/etc/httpd.conf

notify:

- restart apache

- name: ensure apache is running

service: name=httpd state=started

handlers:

- name: restart apache

service: name=httpd state=restarted

----


Used on the `ansible` command line, or in playbooks.

* `all` (or `*`)
* hostname: `foo.example.com`
* groupname: `webservers`
* or: `webservers:dbserver`
* exclude: `webserver:!phoenix`
* intersection: `webservers:&staging`


YAML; given inventory file at `./hosts`:

* `./group_vars/foo`: variable definitions for all members of group `foo`
* `./host_vars/foo.example.com`: variable definitions for foo.example.com


Playbooks consist of a list of one or more 'plays' and/or inclusions:

    ---
    - include: playbook.yml
    - <play>
    - ...



Plays consist of play metadata and a sequence of task and handler
definitions, and roles.

    - hosts: webservers
      remote_user: root
      sudo: yes
      sudo_user: postgress
      su: yes
      su_user: exim
      gather_facts: no
      accelerate: no
      accelerate_port: 5099
      any_errors_fatal: yes
      max_fail_percentage: 30
      connection: local
      serial: 5
      vars:
        http_port: 80
      vars_files:
        - "vars.yml"
        - [ "try-first.yml", "try-second-.yml" ]
      vars_prompt:
        - name: "my_password2"
          prompt: "Enter password2"
          default: "secret"
          private: yes
          encrypt: "md5_crypt"
          confirm: yes
          salt: 1234
          salt_size: 8
      tags: 
        - stuff
        - nonsence
      pre_tasks:
        - <task>
        - ...
      roles:
        - common
        - { role: common, port: 5000, when: "bar == 'Baz'", tags :[one, two] }
        - { role: common, when: month == 'Jan' }
        - ...
      tasks:
        - include: tasks.yaml
        - include: tasks.yaml foo=bar baz=wibble
        - include: tasks.yaml
          vars:
            foo: aaa 
            baz:
              - z
              - y
        - { include: tasks.yaml, foo: zzz, baz: [a,b]}
        - include: tasks.yaml
          when: day == 'Thursday'
        - <task>
        - ...
      post_tasks:
        - <task>
        - ...
      handlers:
        - include: handlers.yml
        - <task>
        - ...



------------------
Each task definition is a list of items, normally including at least a
name and a module invocation:

    - name: task
      remote_user: apache
      sudo: yes
      sudo_user: postgress
      sudo_pass: wibble
      su: yes
      su_user: exim
      ignore_errors: True
      delegate_to: 127.0.0.1
      async: 45
      poll: 5
      always_run: no
      run_once: false
      meta: flush_handlers
      no_log: true
      environment: <hash>
      environment:
        var1: val1
        var2: val2
      tags: 
        - stuff
        - nonsence
      <module>: src=template.j2 dest=/etc/foo.conf
      action: <module>, src=template.j2 dest=/etc/foo.conf
      action: <module>
      args:
          src=template.j2
          dest=/etc/foo.conf
      local_action: <module> /usr/bin/take_out_of_pool {{ inventory_hostname }}
      when: ansible_os_family == "Debian"
      register: result
      failed_when: "'FAILED' in result.stderr"
      changed_when: result.rc != 2
      notify:
        - restart apache

`delegate_to: 127.0.0.1` is implied by `local_action:`

The forms `<module>: <args>`, `action: <module> <args>`, and `local_action: <module> <args>` are mutually-exclusive.
-----------------


## Standard:

    - user: name={{ item }} state=present groups=wheel
      with_items:
        - testuser1
        - testuser2
       
    - name: add several users
      user: name={{ item.name }} state=present groups={{ item.groups }}
      with_items:
        - { name: 'testuser1', groups: 'wheel' }
        - { name: 'testuser2', groups: 'root' }


---------------
Create file in Ansible

- hosts: all
  tasks:
  - name: Ansible create file if it doesn't exist example
    file:
      path: "/Users/mdtutorials2/Documents/Ansible/devops_server.txt"
      state: touch

------
Create a file with content

- hosts: all
  tasks:
  - name: Ansible create file with content example
    copy:
      dest: "/Users/mdtutorials2/Documents/Ansible/remote_server.txt"
      content: |
        dog1
        tiger
-----
Create multiple file name

- hosts: all
  tasks:
  - name: Ansible create multiple files example
    file: 
      path: "{{ item }}"
      state: touch
      mode: 0775
    with_items:
    - int1.txt
    - int2.txt
    - int3.txt
    - int4.txt
-----

Replace string and file

Input.txt
---------
Hello how are you
hello1 case change
hey hello there

Code
------
- hosts: all
  tasks:
  - name: Ansible replace string example
    replace:
      path: /Users/mdtutorials2/Documents/Ansible/intext.txt
      regexp: 'hello'
      replace: "world"
-----
Replace string in multiple files
tasks:
  - name: Ansible multiple replace example
    replace:
      path: "{{ item.path }}"
      regexp: "{{ item.regexp1 }}"
      replace: "{{ item.replace }}"
    with_items:
      - { path: '/Users/mdtutorials2/Documents/Ansible/intext.txt', regexp1: 'cat', replace: 'dog'}
      - { path: '/Users/mdtutorials2/Documents/Ansible/intext2.txt', regexp1: 'orange', replace: 'apple'}
----
Install multiple packages

- hosts: all
  tasks:
  - name: yum
    yum:
      name: "{{ item }}"
      state: present
    with_items:
    - git
    - httpd
    - mysql
----
yum -y update

- hosts: all
  tasks:
  - name: Upgrade all yum packages ansible.
    yum:
      name: "*"
      state: latest
---
To exclude 

- hosts: all
  tasks:
  - name: Exclude some packages from upgrade in Ansible.
    yum:
      name: "*"
      state: latest
      exclude: git*

---
Install through requirements
- hosts: all
  tasks:
  - name: Installing python libraries using requirements file in Ansible
    pip:
      requirements: req.txt
      chdir: /tmp

req.txt
-------
nltk==3.0.0
numpy<2.0.0 scipy>=1.0.0

---

Reinstall pip library

- hosts: all
  tasks:
  - name: Reinstalling a python library in Ansible
    pip:
      name: nltk
      version: 3.0.0
      state: forcereinstall

-------------
Remove pip library

- hosts: all
  tasks:
  - name: Removing Python libraries
    pip:
      name: NumPy,SciPy
      state: absent

---------
home variable in remote server

- hosts: all
  gather_facts: true
  tasks:
    - name: Remote server ansible variables
      debug:
        msg: "{{ ansible_env.HOME }}"


------
- hosts: all
  gather_facts: true
  tasks:
    - name: Printing all the environment? variables in Ansible
      debug:
        msg: "{{ ansible_env }}"

------

ansible masters -a '/usr/local/bin/master-restart controllers controllers'

ansible masters -a '/usr/local/bin/master-logs api api' 

ansible masters -a '/usr/local/bin/master-restart api api'

ansible masters -a 'master-restart etcd'

ansible OSEv3 -m copy -a 'src="./node-dnsmasq.conf" dest="/etc/dnsmasq.d/" mode=0644'

ansible OSEv3 -m service -a "name=docker state=restarted"

ansible OSEv3 -a 'systemctl status docker' | grep Active

ansible masters[0] -m fetch -a 'src=/etc/resolv.conf dest=./'

ansible servername -m copy -a 'src="./playbooks/whitelist.yaml" dest="/root"'

ansible all -m archive -a “path=/etc/origin dest=/etc/origin.certbkp_29102019.tgz”


```
