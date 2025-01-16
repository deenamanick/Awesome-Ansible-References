```
#!/bin/bash

# Variables
MASTER_IP="192.168.59.24"
SLAVES=(
    "192.168.59.25 worker1.ansibledev.com"
    "192.168.59.26 worker2.ansibledev.com"
    "192.168.59.26 worker3.ansibledev.com"
    "192.168.59.26 worker4.ansibledev.com"
)
PLAYBOOK_DIR="$HOME/plays"
INVENTORY_FILE="$PLAYBOOK_DIR/inventory.ini"
CONFIG_FILE="$PLAYBOOK_DIR/ansible.cfg"
REMOTE_USER="your_remote_user" # Replace with the SSH username for your nodes
PASSWORD="your_remote_password" # Replace with the SSH password for your nodes

# Step 1: Install Ansible and sshpass
echo "Installing Ansible and sshpass..."
sudo apt update && sudo apt install -y ansible sshpass

# Step 2: Generate SSH key pair
echo "Generating SSH key pair..."
if [ ! -f ~/.ssh/id_rsa ]; then
    ssh-keygen -t rsa -b 2048 -f ~/.ssh/id_rsa -N ""
else
    echo "SSH key pair already exists. Skipping generation."
fi

# Step 3: Create the plays directory
echo "Setting up the plays directory..."
mkdir -p "$PLAYBOOK_DIR"

# Step 4: Copy SSH key to all slave nodes
echo "Copying SSH key to slave nodes..."
for SLAVE in "${SLAVES[@]}"; do
    HOST_IP=$(echo $SLAVE | awk '{print $1}')
    HOST_NAME=$(echo $SLAVE | awk '{print $2}')

    # Add to /etc/hosts if not already present
    if ! grep -q "$HOST_IP" /etc/hosts; then
        echo "$HOST_IP $HOST_NAME" | sudo tee -a /etc/hosts
    fi

    # Copy the SSH key
    sshpass -p "$PASSWORD" ssh-copy-id -o StrictHostKeyChecking=no -i ~/.ssh/id_rsa.pub $REMOTE_USER@$HOST_IP
done

# Step 5: Create inventory file
echo "Creating inventory file at $INVENTORY_FILE..."
cat > "$INVENTORY_FILE" <<EOL
[master]
master.ansibledev.com ansible_host=$MASTER_IP ansible_user=$REMOTE_USER

[workers]
worker1.ansibledev.com ansible_host=192.168.59.25 ansible_user=$REMOTE_USER
worker2.ansibledev.com ansible_host=192.168.59.26 ansible_user=$REMOTE_USER
worker3.ansibledev.com ansible_host=192.168.59.26 ansible_user=$REMOTE_USER
worker4.ansibledev.com ansible_host=192.168.59.26 ansible_user=$REMOTE_USER
EOL

# Step 6: Create ansible.cfg
echo "Creating ansible.cfg at $CONFIG_FILE..."
cat > "$CONFIG_FILE" <<EOL
[defaults]
inventory = ./inventory.ini
remote_user = $REMOTE_USER
host_key_checking = False
deprecation_warnings = False
EOL

# Step 7: Test Ansible connection
echo "Testing Ansible connectivity..."
cd "$PLAYBOOK_DIR"
ansible all -m ping

# Final message
echo "Ansible setup complete! Inventory and configuration are available in $PLAYBOOK_DIR."


```
