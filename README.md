# 👉 Overview
  What we'll cover:
  1. What is Ansible?
  2. Why Ansible?
  3. Key Advantages of Ansible
  4. Core Architecture of Ansible
  5. How Ansible Works (Step-by-Step)
  6. Ansible vs Other Tools
  7. Ansible Setup Overview
  8. Core Components of Ansible Configuration
  9. Most Commonly Used Ansible Commands
  10. Facts
  11. Handlers
  12. Tags
  13. Collections
  14. Ansible Vault
  15. Ansible Roles
  16. Ansible Galaxy
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

#### 🔹 Ansible Inventory File (INI Format)

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
#### 🔹 Ansible Inventory File (YAML Format)

Ansible inventory can be written in **YAML format** instead of traditional INI format.
YAML inventory is **cleaner**, supports **nested groups**, and is preferred in modern setups.

##### 1️⃣ Basic YAML Inventory Structure

```yaml
all:
  hosts:
    server1:
      ansible_host: 192.168.1.10
    server2:
      ansible_host: 192.168.1.11
```

##### Explanation:

* `all` → top-level group (default in Ansible)
* `hosts` → list of machines
* `ansible_host` → actual IP address

##### 2️⃣ YAML Inventory with Groups

```yaml
all:
  children:
    web:
      hosts:
        web1:
          ansible_host: 192.168.1.20
        web2:
          ansible_host: 192.168.1.21

    db:
      hosts:
        db1:
          ansible_host: 192.168.1.30
```

##### Structure Explanation

* `children` → defines groups
* `web`, `db` → group names
* `hosts` → hosts inside each group

##### 3️⃣ YAML inventory structure:

```
all
 ├── vars
 ├── hosts
 └── children
       ├── group1
       │     ├── hosts
       │     └── vars
       └── group2
```

##### 4️⃣ Running Playbook with YAML Inventory

```bash
ansible-playbook -i inventory.yml site.yml
```

#### 🔹 Static vs Dynamic Inventory in Ansible

There are **2 types** of inventory in Ansible:

1️⃣ Static Inventory
2️⃣ Dynamic Inventory

##### 1️⃣ Static Inventory

A manually created file where you define:

* Hostnames
* IP addresses
* Groups
* Variables

It does NOT change automatically.
Above inventory examples are static inventory.

###### ✅ When to Use Static Inventory?

* Small lab setup
* On-prem servers
* Fixed IP machines
* Practice environments
###### ❌ Problem with Static Inventory

In cloud environments like:

* Amazon Web Services
* Microsoft Azure
* Google Cloud

Servers are created and destroyed frequently.

Static inventory becomes outdated.

##### 2️⃣ Dynamic Inventory

Automatically generated host list from external source.
Dynamic inventory automatically fetches servers from:

* Cloud providers
* APIs
* Databases
* CMDB tools

Inventory updates automatically.

###### 🔹 Example: AWS Dynamic Inventory

Instead of manually writing servers, Ansible queries:
Amazon Web Services
And automatically gets:

* EC2 instances
* Tags
* IP addresses
* Regions
###### ✅ Example — AWS Dynamic Inventory File

```yaml
plugin: amazon.aws.aws_ec2
regions:
  - us-east-1
filters:
  instance-state-name: running
keyed_groups:
  - key: tags.Name
```

Then run:

```bash
ansible-inventory -i aws_ec2.yml --list
```

Ansible will dynamically fetch EC2 machines.
###### 🔹 How Dynamic Inventory Works

1️⃣ You configure plugin
2️⃣ Ansible connects to cloud API
3️⃣ Fetches running servers
4️⃣ Creates groups automatically

Example grouping by tags:

If instance has tag:

```
Environment = production
```

Ansible auto-creates group:

```
production
```
###### 🔹 Other Dynamic Inventory Sources

* Amazon Web Services
* Microsoft Azure
* Google Cloud
* Kubernetes clusters
* Terraform state file
* Custom Python script

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

### Final Quick Revision

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

### Quick Revision Summary

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

We divide into 4 categories:

1. Ad-hoc commands
2. Inventory commands
3. Module documentation Commands
4. Playbook commands

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
* `ansible server -m copy -a "src=/tmp/file.txt dest=/tmp/file.txt mode=0777"`  # Copy a file to target server
* `ansible server -m fetch -a "src=/tmp/file.txt dest=~/files"`  # Fetch file from target to control node
* `ansible server -m setup` # setup module performs **fact-gathering**

### ✅ Key Notes

* **Use `-m shell`** for shell commands that require pipes, redirection, or environment variables.
* **Use `-m command`** for simple commands without shell features (safer).
* Ad-hoc commands are good for **quick tasks** or testing connectivity.
* For **repeated tasks or multiple steps**, use **playbooks** instead.

### 2️⃣ Inventory Commands
* `ansible-inventory --list`  # Display full inventory in JSON format (hosts, groups, variables)
* `ansible-inventory --graph`  # Show inventory in tree/graph format (group hierarchy)
* `ansible-inventory --host <hostname>`  # Show variables assigned to a specific host
* `ansible-inventory -i <inventory_file> --list`  # Use a custom inventory file and display its content
* `ansible-inventory -i <inventory_file> --graph`  # Show graph view of a custom inventory file
* `ansible-inventory --yaml --list`  # Display inventory output in YAML format (easier to read than JSON)
* `ansible all --list-hosts`  # List all hosts that match the pattern (without running tasks)
* `ansible <group_name> --list-hosts`  # List hosts inside a specific group

### 3️⃣ Module Documentation Commands
* `ansible-doc -l` # List all available Ansible modules
* `ansible-doc <module_name>` # Show full detailed documentation of a specific module
* `ansible-doc -s <module_name>` # Show short syntax summary of a module (quick usage format)

### 4️⃣ Playbook Commands

#### General Syntax
```bash
ansible-playbook <playbook_name>.yml [options]
```
#### Most Common Playbook Commands
* `ansible-playbook site.yml`  # Run the playbook normally
* `ansible-playbook site.yml -i inventory`  # Use a custom inventory file
* `ansible-playbook site.yml --check`  # Dry run (simulate changes without applying them)
* `ansible-playbook site.yml --diff`  # Show differences between current and desired state
* `ansible-playbook site.yml --limit web`  # Run playbook only on "web" group
* `ansible-playbook site.yml --limit 192.168.1.10`  # Run playbook on a specific host
* `ansible-playbook site.yml --tags nginx`  # Run only tasks tagged as "nginx"
* `ansible-playbook site.yml --skip-tags db`  # Skip tasks tagged as "db"
* `ansible-playbook site.yml --start-at-task "Install nginx"`  # Start execution from a specific task
* `ansible-playbook site.yml -v`  # Verbose mode (more details)
* `ansible-playbook site.yml -vv`  # More verbosity
* `ansible-playbook site.yml -vvv`  # Debug-level verbosity
* `ansible-playbook site.yml --ask-pass`   # Ask for SSH password
* `ansible-playbook site.yml --ask-become-pass`  # Ask for sudo password
* `ansible-playbook site.yml --syntax-check`  # Check playbook syntax without running it

---

## 🔟 Facts (Ansible Facts)

Facts are **automatic information collected by Ansible** about managed nodes.
When a playbook runs, Ansible gathers:
* OS type
* IP address
* Hostname
* CPU
* Memory
* Disk
* Network interfaces

```bash
`ansible host-pattern -m setup`  
```
**Show all facts of a host**

### 🔹 Example

```yaml
- hosts: all
  tasks:
    - name: Show OS
      debug:
        msg: "{{ ansible_distribution }}"
```

Output example:

```
Ubuntu
```
### 🔹 Disable Facts (If Not Needed)

```yaml
- hosts: all
  gather_facts: no
```
✔ Improves performance.
Facts are stored in `ansible_facts` and accessible as variables.

---

## 1️⃣1️⃣ Handlers

Handlers are tasks that only run **only when notified**.
Used mainly for restarting services.

### 🔹 Example

```yaml
tasks:
  - name: Update nginx config
    copy:
      src: nginx.conf
      dest: /etc/nginx/nginx.conf
    notify: Restart nginx

handlers:
  - name: Restart nginx
    service:
      name: nginx
      state: restarted
```

- Handler runs only if file changed.
- Handlers improve efficiency by avoiding unnecessary service restarts.

---

## 1️⃣2️⃣ Tags

Tags allow running specific tasks.

### 🔹 Example

```yaml
- name: Install nginx
  apt:
    name: nginx
  tags: nginx
```

Run only nginx tasks:

```bash
ansible-playbook site.yml --tags nginx
```

Skip:

```bash
ansible-playbook site.yml --skip-tags nginx
```

---


## 1️⃣3️⃣ Collections

Collections are bundles of:

* Modules
* Plugins
* Roles

Example:

```bash
ansible-galaxy collection install amazon.aws
```

Used for AWS modules.

---

## 1️⃣4️⃣ Ansible Vault

Used to encrypt sensitive data.

### 🔹 Encrypt File

```bash
ansible-vault encrypt secrets.yml
```
### 🔹 Edit Encrypted File

```bash
ansible-vault edit secrets.yml
```
### 🔹 Run Playbook with Vault

```bash
ansible-playbook site.yml --ask-vault-pass
```

Vault protects:

* Passwords
* API keys
* Private data

---

## 1️⃣5️⃣ Roles

Ansible Roles is a way to organize playbooks into reusable components.

### 🔹 Role Structure

```
roles/
  nginx/
    tasks/
    handlers/
    templates/
    files/
    vars/
```
### 🔹 Use Role in Playbook

```yaml
- hosts: web
  roles:
    - nginx
```
### 🎯 Why Roles?

✔ Modular
✔ Reusable
✔ Production standard

---

## 1️⃣6️⃣ Ansible Galaxy

Ansible Galaxy is a public repository for roles and collections.

### 🔹 Install Role

```bash
ansible-galaxy install geerlingguy.nginx
```

### 🔹 Create New Role

```bash
ansible-galaxy init myrole
```

Galaxy helps reuse community-built automation.

---

### 🚀 Quick Interview Revision Summary

* Facts → System information
* Variables → Store values
* Handlers → Run when notified
* Tags → Select tasks
* Loops → Repeat tasks
* Conditions → Control execution
* User/Group → Manage system accounts
* Template → Dynamic configs
* Collections → Bundle of modules/plugins
* Vault → Encrypt secrets
* Roles → Modular automation
* Galaxy → Community repository

---

