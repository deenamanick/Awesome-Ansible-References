## Use Ansible Handlers to Trigger Tasks Upon Change

### Why Use Them:

To execute tasks only when notified by another task.
To optimize playbooks by avoiding unnecessary task execution.
To trigger actions dynamically based on changes (e.g., restarting a service after a configuration change)


#### Basic Nginx Configuration (nginx.conf)

```
# Main configuration file
user www-data;
worker_processes auto;
pid /run/nginx.pid;

events {
    worker_connections 1024;
}

http {
    # Basic settings
    sendfile on;
    tcp_nopush on;
    tcp_nodelay on;
    keepalive_timeout 65;
    types_hash_max_size 2048;

    include /etc/nginx/mime.types;
    default_type application/octet-stream;

    # Logging
    access_log /var/log/nginx/access.log;
    error_log /var/log/nginx/error.log;

    # Gzip compression
    gzip on;
    gzip_disable "msie6";

    # Virtual host configurations
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}

```
### use handlers in ansible


```
- name: Use handlers to manage Nginx service restarts
  hosts: all
  tasks:
    - name: Copy the Nginx configuration file
      copy:
        src: /path/to/nginx.conf
        dest: /etc/nginx/nginx.conf
      notify: Restart Nginx

    - name: Install Nginx
      apt:
        name: nginx
        state: present

  handlers:
    - name: Restart Nginx
      service:
        name: nginx
        state: restarted


```

### Explanation:
Task 1: The copy task uploads the Nginx configuration file (nginx.conf) to the server. If the file changes, it notifies the Restart Nginx handler.
Task 2: Installs the Nginx package using apt.
Handler: The Restart Nginx handler restarts the Nginx service, ensuring the updated configuration is applied. It only triggers if the copy task detects a change in the configuration file.


