#  Create a bash script named adhoc-cmds.sh that will run few ad-hoc commands on your managed nodes. 
## Your script will accomplish the following three tasks: 
###  1. Install nginx on worker4. 
###  2. Displays the uptime on all the managed nodes. 
###  3. Create the file /tmp/hello.txt with the contents “Hello, Friend!” on worker3.

```
#!/bin/bash

# Define inventory and ansible user

# 1. Install Python on worker4
echo "Installing nginx on worker4..."
ansible worker4 --m raw -a "sudo apt update && sudo apt install -y nginx" --become

# 2. Display uptime on all managed nodes
echo "Displaying uptime on all managed nodes..."
ansible all -m command -a "uptime"

# 3. Create /tmp/hello.txt on worker3
echo "Creating /tmp/hello.txt with contents 'Hello, Friend!' on worker3..."
ansible worker3 -i $INVENTORY -u $USER -m copy -a "dest=/tmp/hello.txt content='Hello, Friend!'" --become

echo "All tasks completed."


```
