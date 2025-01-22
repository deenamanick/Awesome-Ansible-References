### Benefits of Using Jinja2 with Ansible
Makes configurations dynamic and reusable.
Reduces duplication in tasks and playbooks.
Adapts easily to different environments or host-specific settings.
Supports complex logic using conditionals and loops.
This approach is powerful for managing configurations across multiple servers and applications

## 1. Variable Substitution

### Template Example (example.j2):

```
Welcome to {{ ansible_facts['hostname'] }} hosted on {{ ansible_facts['default_ipv4']['address'] }}.

```
```
---
- name: Server Information Summary
  hosts: all
  tasks:
   - name: Create server-info.txt using Jinja2
     template:
       src: templates/example.j2
       dest: /tmp/server-info.txt

```

## 2. Conditionals
```
{% if enable_feature %}
Feature is enabled.
{% else %}
Feature is disabled.
{% endif %}
```
```
- name: Use conditionals in templates
  hosts: all
  vars:
    enable_feature: true
  tasks:
    - name: Render the conditional template
      template:
        src: templates/conditional.j2
        dest: /tmp/conditional.txt

```

## 3. Loops

```
Available users:``

{% for user in users %}
- {{ user }}
{% endfor %}

```
```
- name: Use loops in templates
  hosts: all
  vars:
    users:
      - alice
      - bob
      - charlie
  tasks:
    - name: Render the loop template
      template:
        src: templates/loop_list.j2
        dest: /tmp/loop_list.txt

```


``
