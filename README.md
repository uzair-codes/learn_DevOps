# 👉 Overview
  What we'll cover:
  1. What is Ansible?
  2. Why Ansible?
  3. Key Advantages of Ansible
  4. Core Architecture of Ansible
  5. How Ansible Works (Step-by-Step)
  6. Ansible vs Other Tools
  7. Ansible Setup Overview
---

## 1️⃣ What is Ansible?

**Ansible is an open-source automation tool** used for:

* Configuration Management
* Application Deployment
* Server Management
* Infrastructure Automation

It helps you automate repetitive tasks like:

* Installing software
* Updating servers
* Creating users
* Deploying applications
* Configuring cloud resources

👉 Instead of logging into 20 servers manually, you write **one playbook** and Ansible does everything automatically.

### Simple Definition (Interview Version)

> **Ansible is an agentless configuration management and automation tool that uses YAML playbooks to manage servers over SSH.**

---
## 2️⃣ Why Ansible

Before using any tool, you must understand **why it exists**.

### 🔹 Problem Before Ansible

Imagine you have:

* 20 Linux servers
* Need to install NGINX
* Need to create users
* Need to update packages

Without automation:

* SSH into each server manually
* Run commands one by one
* High chance of mistakes
* Not scalable

### 🔹 Why Ansible Was Created

Ansible solves:

- Manual repetitive work
- Configuration inconsistency
- Human errors
- Slow deployments
- Complex automation scripting

### 🔹 Why Companies Use Ansible

* Simple YAML syntax
* No agents required
* Fast setup
* Easy to learn
* Powerful for large infrastructure

---
## 3️⃣ Key Advantages of Ansible

### 1. Agentless

* No agent installation required on target servers.
* Uses **SSH (Linux)** and **WinRM (Windows)**.
* Less maintenance
* No extra services running on servers

### 2. Simple YAML Syntax

Uses Ansible Playbooks, which are **YAML** files that define the desired state of the system and the tasks to achieve that state.
Ansible uses **YAML**, which is human-readable.

Example:

```yaml
- name: Installing Nginx
  hosts: web
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
```

Very easy to understand even for beginners.

### 3. Idempotent

**Idempotent means:**
Running the same playbook multiple times will not change the system if it is already in the desired state.

### 4. Push-Based Architecture

* Control node pushes configuration to servers.
* No need for servers to pull configuration.

### 5. Large Module Library

Ansible has 3000+ built-in modules for:

* Linux
* Windows
* AWS
* Azure
* Docker
* Kubernetes
* Network devices

Modules are reusable and can be used in playbooks to perform specific tasks.

---


## 4️⃣ Core Architecture of Ansible

Ansible architecture is very simple.

### 1. Control Node

* Machine where Ansible is installed.
* Executes playbooks.
* Connects to managed nodes.

### 2. Managed Nodes (Target Nodes)

* Servers being managed.
* No agent required.
* Must have SSH access.

### 3. Inventory

File that defines:

* Which servers or server groups to manage
* Lists IP addresses or hostnames of servers and variables associated with them.

Example:

```ini
[web]
server1 ansible_host=192.168.1.10
server2 ansible_host=192.168.1.11

[db]
database1 ansible_host=192.168.1.20

[web:vars]
ansible_ssh_private_key_file=/root/web-key.pem
[db:vars]
ansible_ssh_private_key_file=/root/db-key.pem
[all:vars]
ansible_user=ubuntu
```

### 4. Playbooks

YAML files that define:

* Which hosts to target
* What tasks to perform
* How to perform those tasks

Example:

```yaml
- hosts: web
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
```

### 5. Modules

Modules are Small programs that perform tasks.

Examples:

* `apt`
* `yum`
* `service`
* `copy`
* `file`
* `user`

### Architecture Summary

Control Node → SSH → Managed Nodes → Executes Modules → Returns Result

---

## 5️⃣ How Ansible Works (Step-by-Step)

Let’s understand the workflow:

### Step 1:

You write a playbook in YAML, for example `install-nginx.yml`.

### Step 2:

You define target servers in inventory.

### Step 3:

You run:

```bash
ansible-playbook install-nginx.yml
```

### Step 4:

Ansible:

* Connects to servers via SSH
* Copies module temporarily
* Executes it
* Removes it after execution
* Shows output

---

## 6️⃣ Ansible vs Other Tools

### ➡️ Ansible vs Puppet

| Feature        | Ansible   | Puppet      |
| -------------- | --------- | ----------- |
| Architecture   | Agentless | Agent-based |
| Language       | YAML      | Puppet DSL  |
| Communication  | Push      | Pull        |
| Learning Curve | Easy      | Medium      |

#### Interview Tip:

> Ansible is easier and agentless. Puppet requires agent installation and has more complex setup.

### ➡️ Ansible vs Chef

| Feature        | Ansible | Chef         |
| -------------- | ------- | ------------ |
| Language       | YAML    | Ruby         |
| Agent Required | No      | Yes          |
| Setup          | Easy    | Complex      |
| Complexity     | Simple  | More complex |

Chef uses Ruby DSL, which is harder for beginners.

### ➡️ Ansible vs Terraform

This is VERY IMPORTANT for interviews.

| Feature    | Ansible                   | Terraform                   |
| ---------- | ------------------------- | --------------------------- |
| Purpose    | Configuration Management  | Infrastructure Provisioning |
| State File | No central state required | Uses state file             |
| Best For   | Installing software       | Creating cloud resources    |

#### Simple Explanation

* **Terraform creates infrastructure**

  * EC2
  * VPC
  * Load Balancer

* **Ansible configures infrastructure**

  * Install NGINX
  * Configure app
  * Setup users

#### Example Real DevOps Flow

1. Terraform creates EC2 servers
2. Ansible installs Docker & deploys app

Both tools are used together in real projects.

---

## Final Quick Revision

- Ansible = Automation tool
- Python Based
- Managed by RedHat
- Agentless
- Uses YAML
- Push-based
- Idempotent 
- Control Node (Ansible Installed) + Managed Nodes
- SSH-based execution
- Used for configuration management

---

## 7️⃣ Ansible Setup Overview

Ansible setup is simple. It requires:
  1. Install Ansible on Control Node
  2. Create Inventory File
  3. Prepare Managed Nodes
  4. Write Playbooks
  5. Run Playbook

### ✅ Step 1: Install Ansible on Control Node

Control Node = The machine where you run Ansible commands.

Example:

* Your laptop
* A Linux VM
* A CI/CD server

#### Install Using pip (Recommended & Simple)

```bash
python3 -m pip install ansible
```

Verify:

```bash
ansible --version
```
**OR You can reffer to [Ansible Community Documentation](https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html) for more installation methods for different operating systems.**

### ✅ Step 2: Create Inventory File

Inventory file contains:

* List of target machines
* Grouping of servers

Example:

```ini
[web]
192.168.1.10
192.168.1.11

[db]
192.168.1.20
```

### ✅ Step 3: Prepare Managed Nodes

Each target machine must have:

* Python installed (usually pre-installed on Linux)
* SSH access enabled
* Passwordless SSH configured (recommended)

#### What is Passwordless SSH Access (VERY IMPORTANT)

Instead of typing password:

* SSH uses private/public key authentication.
* Secure and automated.

Ansible uses SSH to connect to Linux machines.
If SSH asks for password every time → Automation breaks.
So we configure **Passwordless SSH** using SSH keys.

#### 🔴 Step 1: Generate SSH Key on Control Node

```bash
ssh-keygen -t rsa -b 2048
```

Press Enter → It creates:

* Private key → `~/.ssh/id_rsa`
* Public key → `~/.ssh/id_rsa.pub`

#### 🔴 Step 2: Copy Public Key to Target Machine

##### 🔹 Method 1: Using ssh-copy-id (Recommended)

```bash
ssh-copy-id user@target_ip
```

Example:

```bash
ssh-copy-id ubuntu@192.168.1.10
```

This automatically adds the public key to:

```
~/.ssh/authorized_keys
```

##### 🔹 Method 2: Manual Copy

1. Open public key:

```bash
cat ~/.ssh/id_rsa.pub
```

2. Copy the output.

3. Paste into target machine:

```
~/.ssh/authorized_keys
```

##### 🔹 Method 3: Using Private Key File (Cloud Example)

When you create a compute instance on a cloud platform (like AWS, Azure, etc.), you select a **key pair** during creation.

The cloud platform automatically:

* Adds the **public key** to the server
* Stores it inside the `~/.ssh/authorized_keys` file of that machine

Because of this, you can connect to the server using the **private key** from your local machine.

In Ansible, you can enable passwordless SSH by using the built-in variables:

```ini
ansible_user=user
ansible_ssh_private_key_file=/path/to/private-key
```

Example in inventory file:

```ini
[web]
192.168.1.10 ansible_user=user ansible_ssh_private_key_file=~/.ssh/id_rsa
```

This tells Ansible to:

* Use the specified private key
* Authenticate without asking for a password

**Behind the scenes, Ansible connects to the target machine using SSH, just like we do manually.**

It runs a command similar to:

```bash
ssh -i /path/to/private-key user@ip-address
```

So basically:

* `-i` → Specifies the private key file
* `user` → Remote server username
* `ip-address` → Target machine IP

If using AWS EC2:

Example inventory:

```ini
[web]
18.209.43.91 ansible_user=ubuntu ansible_ssh_private_key_file=/root/my-key.pem
```

Important variables:

* `ansible_host` → IP address
* `ansible_user` → SSH user
* `ansible_ssh_private_key_file` → Path of private key

#### 🔴 Step 3: Test SSH Access

From control node:

```bash
ssh ubuntu@192.168.1.10
```

If it logs in without password → Success ✅

### 🔶 Verify Ansible Connection

Test using ping module:

```bash
ansible all -m ping
```

Expected output:

```
192.168.1.10 | SUCCESS => {
    "ping": "pong"
}
```

📌 Important:
This is not ICMP ping.
It is Ansible module test.

### 🔶 How Ansible Works Internally (During SSH)

When you run:

```bash
ansible all -m ping
```

Ansible:

1. Connects via SSH
2. Copies Python module temporarily
3. Executes it
4. Deletes module
5. Returns result

✔ No agent installed
✔ Temporary execution

### 🔶 Common Issues & Fixes

#### ❌ Permission Denied (Publickey)

Fix:

```bash
chmod 600 ~/.ssh/id_rsa
chmod 700 ~/.ssh
```

#### ❌ Python Not Found on Target

Install Python:

```bash
sudo apt install python3
```

#### ❌ SSH Connection Refused

Check:

* SSH service running?
* Security group open? (Cloud)
* Firewall rules?

### ✅ Step 4: Write Playbooks

Playbooks define automation tasks.

Example:

```yaml
- hosts: web
  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
```

### ✅ Step 5: Run Playbook

```bash
ansible-playbook install-nginx.yml
```

Ansible connects → Executes → Shows result.

---

## Quick Revision Summary

- Install Ansible on control node
- Create inventory file
- Setup SSH key authentication
- Ensure Python installed on targets
- Test using ansible all -m ping
- Run playbooks

---

## 8️⃣ Core Components of Ansible Configuration

When you install Ansible you get a default configuration directory `/etc/ansible`:

```bash
$ ls /etc/ansible/
ansible.cfg
hosts
roles/
```
These are the core configuration components. They control how Ansible connects, what systems it manages, and how automation is organized.

### 🔹 1. ansible.cfg (Main Configuration File)

It contains settings that control Ansible's behavior.
This file controls:

* Default inventory location
* SSH settings
* Logging
* Privilege escalation
* Module paths

Example snippet:

```ini
[defaults]
inventory = /etc/ansible/hosts
remote_user = ubuntu
host_key_checking = False
```

📌 Important:

Ansible checks config file in this order:

1. ansible.cfg (current directory)
2. ~/.ansible.cfg
3. /etc/ansible/ansible.cfg

### 🔹 2. hosts (Inventory File)

This is the default inventory file that defines:

* Target servers
* Server Groups
* Variables associated with them

Example:

```ini
[web]
192.168.1.10
192.168.1.11

[db]
192.168.1.20
```

With variables:

```ini
[web]
192.168.1.10 ansible_user=ubuntu
```

Inventory tells Ansible:
👉 “Where should I run my automation?”

### 🔹 3. roles Directory

Roles are used to organize automation code. By using roles, you can create reusable and modular automation code.

Instead of writing one big playbook, we divide tasks into reusable components.

Example role structure:

```
roles/
  nginx/
    tasks/
      main.yml
    handlers/
      main.yml
    templates/
    files/
    vars/
```

Why roles?

- Reusable
- Modular
- Clean structure
- Production-ready

Interview Tip:

> Roles are used to create reusable and organized automation units.

---

## 9️⃣ Most Commonly Used Ansible Commands

We divide into 3 categories:

1. Ad-hoc commands
2. Inventory commands
3. Playbook commands

### 1️⃣ Ad-hoc Commands (Quick One-Time Commands)

#### **General Structure**

```
ansible <host-pattern> -m <module> -a "<module-arguments>" -b --ask-become-pass
```

**Where:**

* `<host-pattern>` → Target host(s) or group(s) from your inventory
* `-m <module>` → Ansible module to use (e.g., ping, shell, command, yum, apt)
* `-a "<module-arguments>"` → Arguments to the module (optional for some modules)
* `-b --ask-become-pass` → Give Root privilege   

#### **Most Commonly Used Ad-Hoc Commands**

* `ansible localhost -m ping`  # Test connection to local machine
* `ansible all -m ping`  # Test connectivity to all hosts
* `ansible server -m shell -a "free -h"`  # Show memory info on target server
* `ansible server -m shell -a "uptime"`  # Show uptime of target server
* `ansible server -m shell -a "df -h"`  # Check disk usage
* `ansible server -m shell -a "who"`  # See logged in users
* `ansible server -m command -a "ls -l /etc"`  # Run simple command (doesn’t go through shell)
* `ansible server -m user -a "name=testuser state=present"`  # Create a new user
* `ansible server -m service -a "name=nginx state=started"`  # Start or stop a service
* `ansible server -m apt -a "name=vim state=latest"`  # Install/upgrade package on Ubuntu
* `ansible server -m yum -a "name=httpd state=present"`  # Install package on RHEL/CentOS
* `ansible server -m copy -a "src=/tmp/file.txt dest=/tmp/file.txt mode=0777"`  # Copy a file to target server
* `ansible server -m fetch -a "src=/tmp/file.txt dest=~/files"`  # Fetch file from target to control node

---

### ✅ Key Notes

* **Use `-m shell`** for shell commands that require pipes, redirection, or environment variables.
* **Use `-m command`** for simple commands without shell features (safer).
* Ad-hoc commands are good for **quick tasks** or testing connectivity.
* For **repeated tasks or multiple steps**, use **playbooks** instead.

### 🔴🔴 B. Inventory Commands

---

### 4️⃣ View Full Inventory

```bash
ansible-inventory --list
```

Shows:

* Hosts
* Groups
* Variables
* JSON format

---

### 5️⃣ View Inventory Graph

```bash
ansible-inventory --graph
```

Shows visual group structure:

```
@all:
  |--@web:
  |  |--192.168.1.10
```

Very helpful for debugging.

---

# 🔴🔴 C. Module Documentation Commands

---

### 6️⃣ List All Modules

```bash
ansible-doc -l
```

Shows all available modules.

---

### 7️⃣ Detailed Module Documentation

```bash
ansible-doc apt
```

Shows:

* Parameters
* Examples
* Usage

---

### 8️⃣ Short Summary of Module

```bash
ansible-doc -s apt
```

Shows quick usage format.

Very useful during interview preparation.

---

# 🔴🔴 D. Playbook Commands

---

### 9️⃣ Run Playbook

```bash
ansible-playbook site.yml
```

Executes full automation.

---

### 🔟 Dry Run (Check Mode)

```bash
ansible-playbook site.yml --check
```

✔ Shows what will change
✔ Does NOT apply changes

Very important for production.

---

### 1️⃣1️⃣ Show Changes (Diff Mode)

```bash
ansible-playbook site.yml --diff
```

Shows difference before and after changes.

Great for configuration files.

---

### 1️⃣2️⃣ Limit to Specific Host

```bash
ansible-playbook site.yml --limit web
```

Runs playbook only on web group.

---

### 1️⃣3️⃣ Run Specific Tags

```bash
ansible-playbook site.yml --tags nginx
```

Runs only tasks tagged as nginx.

Example:

```yaml
- name: Install nginx
  apt:
    name: nginx
  tags: nginx
```

---

### 1️⃣4️⃣ Start From Specific Task

```bash
ansible-playbook site.yml --start-at-task "Install nginx"
```

Useful if playbook failed halfway.

---

### 1️⃣5️⃣ Verbose Mode

```bash
ansible-playbook site.yml -v
```

More verbosity:

* -v
* -vv
* -vvv
* -vvvv (debug level)

Used for troubleshooting.

---

# 🚀 Quick Revision Summary

✔ Ansible solves manual configuration problem
✔ ansible.cfg controls behavior
✔ hosts file defines target systems
✔ roles organize automation
✔ ansible -m ping tests connectivity
✔ ansible-doc shows module help
✔ ansible-playbook runs automation
✔ --check = dry run
✔ --limit = run on selected hosts
✔ --tags = run specific tasks

---
