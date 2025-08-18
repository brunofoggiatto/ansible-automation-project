# Ansible Automation Project (Ubuntu 22.04)
## This guide provides a practical, step-by-step introduction to using Ansible playbooks for automating common tasks on virtual machines with ssh connection. It's designed for beginners who want to get started with infrastructure automation.

## Table of Contents
- [Install Ansible](#install-ansible)
- [Generate Your SSH Key](#generate-your-ssh-key)
- [Copy the Public Key to Your VMs](#copy-the-public-key-to-your-vms)
- [Configure the Inventory](#configure-the-inventory)
- [Create a Simple Playbook](#create-a-simple-playbook)
- [Execute the Playbook](#execute-the-playbook)

## Install Ansible

First, you need to install Ansible on your local machine (the local computer you will use to manage other servers)

```bash
sudo apt update
sudo apt install ansible -y
```

# Generate Your SSH Key

Now, in your local machine, you need to generate your SSH key for instant connection to your virtual machines, this will allow you to connect without typing your password each time.
When prompted, press Enter to accept the default file location and when asked for a passphrase, press Enter twice to leave it empty.

```bash
ssh-keygen
```
**Security Note**: For production environments, it is highly recommended to use a strong passphrase to protect your private key.

# Copy the Public Key to Your VMs

Next, copy your public key to each virtual machine you want to manage.

```bash
ssh-copy-id [username]@[ip]
```
If you want to run commands as the root user directly, copy the key to the root account.
The first time you run this for each VM, you'll be prompted for the user's password. After this setup, you can SSH into the VM from your control machine without a password.

# Configure the Inventory

The inventory is a file that lists the servers (or "nodes") that Ansible will manage. Create a file named hosts in your project directory with the following format:

```bash
[ubuntu_servers]
192.168.1.101 
192.168.1.102
...
```
**Note**: The connection method and variables can differ for other operating systems. For example, managing Windows machines requires a different configuration in the inventory.

# Create a Simple Playbook

A playbook is a YAML file where you define the automation tasks. Create a file named system_upgrade.yml to update all the servers listed in your inventory.

**Important**: YAML syntax is very strict about indentation. Use spaces (not tabs) and ensure the structure is correct.

```bash
---
- name: Update and upgrade the system
  hosts: ubuntu_servers
  become: yes
  tasks:
    - name: Update package cache
      ansible.builtin.apt:
        update_cache: yes

    - name: Upgrade all packages
      ansible.builtin.apt:
        upgrade: dist
```
### How the Playbook Works

* **`hosts: ubuntu_servers`**: Tells Ansible to run the playbook on all servers listed in the `[ubuntu_servers]` group of your `hosts` inventory file.

* **`become: yes`**: Instructs Ansible to use privilege escalation (like `sudo`) to execute tasks, which is necessary for system updates.

* **`tasks:`**: This section lists the actions to be performed.
    * The first task, **Update apt package cache**, is equivalent to running `sudo apt update`.
    * The second task, **Upgrade all packages**, is equivalent to running `sudo apt dist-upgrade`.

# Execute the Playbook

Finally, run your playbook from your control machine using the ansible-playbook command.

```bash
ansible-playbook system_upgrade.yml -i ubuntu_servers
```
* `` `ansible-playbook` ``: The command to run a playbook.
* `` `-i hosts` ``: The `-i` this specifies the inventory file to use (in this case, the `hosts` file we created).
* `` `system_upgrade.yml` ``: The name of the playbook file to execute.

Ansible will now connect to each server in your inventory and execute the defined tasks.
