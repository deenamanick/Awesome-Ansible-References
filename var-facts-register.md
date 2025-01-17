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
