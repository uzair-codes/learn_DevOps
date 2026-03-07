# Variables in Ansible

Variables in Ansible are used to store values that can change.
They make playbooks **dynamic, reusable, and flexible**.
Instead of hardcoding values like usernames, ports, or package names, we use variables.

### ❌ Hardcoded

```yaml
- name: Install nginx
  apt:
    name: nginx
    state: present
```

### ✅ Using Variable

```yaml
- name: Install package
  apt:
    name: "{{ package_name }}"
    state: present
```

Now `package_name` can change without modifying the task.

## 1️⃣ How to Define Variables

Variables can be defined in multiple places.

- In playbook (vars)
- In inventory
- In group_vars and host_vars
- Command Line Variables Using extra vars (-e) ← Highest priority
- Using vars_files
- Using register
- Using vars_prompt
- Using environment variables
- Using role defaults e.g. roles/nginx/defaults/main.yml

### ✅ 1. In Playbook (vars section)

```yaml
- hosts: web
  vars:
    package_name: nginx
    http_port: 80

  tasks:
    - name: Install package
      apt:
        name: "{{ package_name }}"
        state: present
```

- Simple
- Good for small playbooks
- ❌ NOT good for production

---

### ✅ 2. In Inventory File

#### INI Format

```ini
[web]
server1 ansible_host=192.168.1.10 http_port=80
server2 ansible_host=192.168.1.11 https_port=443

[db]
database1 ansible_host=192.168.1.20

[web:vars]
ansible_ssh_private_key_file=/root/web-key.pem
[db:vars]
ansible_ssh_private_key_file=/root/db-key.pem
[all:vars]
ansible_user=ubuntu
```

#### Inventory file YAML Format

```yaml
all:
  vars:
    ansible_user: ubuntu

  children:

    web:
      hosts:
        web1:
          ansible_host: 192.168.1.20
          http_port: 8080
        web2:
          ansible_host: 192.168.1.21
      vars:
        max_clients: 200

    db:
      hosts:
        db1:
          ansible_host: 192.168.1.30
          db_port: 5432
      vars:
        db_engine: postgres
```

- `all` → top-level group (default in Ansible)
- `children` → defines groups
- `web`, `db` → group names
- `hosts` → hosts inside each group
- `ansible_host` → actual IP address
- **Variables Priority:**  *Host variables > Group variables > All variables* 
- Useful for environment-specific values

---

### ✅ 3. group_vars and host_vars

They are special directories that Ansible automatically reads for variables. You do NOT declare them manually.
If they exist in the correct location, Ansible loads them automatically.

#### 🔹 Standard Inventory Structure (Best Practice)

If your inventory contains:

```ini
[web]
server1
server2
```
```
project/
│
├── inventory/
│    ├── hosts.ini
│    ├── group_vars/
│    │     └── web.yml
│    │     └── all.yml
│    └── host_vars/
│          └── server1.yml
└── site.yml
```
#### 🔹 Important Rule ⭐

Ansible loads variables based on:

* Inventory hostname
* Inventory group name
If name does not match → variables will NOT load.

- So, the filenames `web.yml` and `server1.yml` must match the **group and host names exactly**
- Variables in `web.yml` applies to all hosts in group `web`.
- Variables in `server1.yml` applies only to `server1`.
- Variables in `all.yml` applies to `all target servers`

#### 🔹 Can group_vars and host_vars Be Placed Elsewhere?

Yes, They can be:

### Option 1 (Inside inventory folder)

```
inventory/group_vars/
inventory/host_vars/
```

### Option 2 (At project root level)

```
group_vars/
host_vars/
```

Both are valid if Ansible can find inventory correctly.

#### 🔹 Variable Precedence (Between Them)

If both define same variable:

| Source     | Priority |
| ---------- | -------- |
| group_vars | Lower    |
| host_vars  | Higher   |

Host-specific overrides group-specific.

---

### ✅ 4. Command Line Variables (Highest Priority ⭐)

```bash
ansible-playbook site.yml -e "package_name=apache2"
```
Or JSON format:

```bash
ansible-playbook site.yml -e '{"app_port":9090}'
```
---

### ✅ 5. From External File (vars_files)

`vars_files` can load variable files from **ANY path you specify**.

```yaml
vars_files:
  - vars/common.yml
  - /home/user/external_vars.yml
```

---

### ✅ 6. Registered Variables (From Task Output)

`register` captures output of a task into a variable.

```yaml
- name: Check disk space
  shell: df -h
  register: disk_output

- debug:
    var: disk_output.stdout
```
`disk_output` becomes a variable.
- Dynamic
- Used with conditions

---

### ✅ 7. Prompting User for Variables Using `vars_prompt` (User Input)

Ask user for value at runtime.

```yaml
- hosts: web
  vars_prompt:
    - name: username
      prompt: "Enter your username"
      private: no
```

Used when sensitive input is needed, like passwords etc.

---

### ✅ 8.  Environment Variables

You can fetch environment variable:

```yaml
app_port: "{{ lookup('env','APP_PORT') }}"
```

If system has:

```bash
export APP_PORT=8080
```

✔ Useful in CI/CD pipelines

---

### ✅ 9. Using Role Defaults

Inside role directory:

```
roles/
 └── nginx/
      └── defaults/
            └── main.yml
```

`defaults/main.yml`

```yaml
app_port: 80
```

- Lowest precedence
- Used for default values

---

### ✅ 10. Using Facts -  Special Built-in Variables (Facts)

Ansible automatically collects system information called **facts**.

Example:

```yaml
{{ ansible_hostname }}
{{ ansible_os_family }}
{{ ansible_ip_addresses }}
```

These are variables collected during fact gathering.

---

## 2️⃣ Variable Precedence (Very Important ⭐⭐)

When same variable is defined in multiple places, Ansible follows priority order.

From lowest → highest:

| Method           | When Used            | Priority  |
| ---------------- | -------------------- | --------- |
| Role defaults    | Default values       | Lowest    |
| Inventory        | Host/group config    | Medium    |
| group_vars       | Environment config   | High      |
| host_vars        | Host-specific config | High      |
| vars in playbook | Simple setups        | High      |
| register         | Store task output    | Higher    |
| Extra vars (-e)  | Override everything  | Highest ⭐ |

---

## 3️⃣ Variable Types in Ansible

Ansible uses YAML data types.

### 🔹 1. String

```yaml
name: "John"
```

### 🔹 2. Number

```yaml
port: 8080
```

### 🔹 3. Boolean

```yaml
enabled: true
```

### 🔹 4. List

```yaml
packages:
  - nginx
  - git
  - curl
```

Usage:

```yaml
name: "{{ packages }}"
```

---

### 🔹 5. Dictionary (Map)

```yaml
user:
  name: john
  shell: /bin/bash
```

Access:

```yaml
{{ user.name }}
```

---

## 4️⃣ Using Variables in Playbook

Syntax:

```yaml
{{ variable_name }}
```

Example:

```yaml
- name: Install package
  apt:
    name: "{{ package_name }}"
    state: present
```

**⚠ Always use double curly braces.**

---

## 5️⃣ Variable Filters (Modify Variables)

Filters change variable output.

Example:

```yaml
{{ name | upper }}
{{ list | length }}
```

Common filters:

* upper
* lower
* length
* default
* join
* replace

Example:

```yaml
{{ username | default('admin') }}
```

---

## 6️⃣ Best Practices ⭐

* Do NOT hardcode values
* Use group_vars for environment configs
* host_vars → host config
* vars_files → shared config file
* role vars → role-specific variables
* Use meaningful variable names
* Avoid duplicate definitions
* Keep secrets in Ansible Vault
* Use extra vars carefully

---

## 7️⃣ Real-World Example

```yaml
- hosts: web
  vars:
    package_name: nginx

  tasks:
    - name: Install package
      apt:
        name: "{{ package_name }}"
        state: present

    - name: Check service status
      command: systemctl status {{ package_name }}
      register: service_status

    - debug:
        var: service_status.stdout
```

---

# 🔥 Final Quick Revision Summary

* Variables store dynamic values
* Defined in playbook, inventory, group_vars, host_vars
* `-e` has highest priority
* Use `{{ variable }}` syntax
* Support strings, numbers, boolean, lists, dictionaries
* `register` captures task output
* Facts are auto-generated variables

