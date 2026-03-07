# 🔹 Users and Groups in Ansible

In Ansible, users and groups are managed using built-in modules:

* `user` → Manage system users
* `group` → Manage system groups

These modules work on Linux systems.

## 1️⃣ Managing Groups in Ansible

### Create a Group

```yaml
- name: Create developers group
  group:
    name: developers
    state: present
```

- Creates group if not exists
- Idempotent (safe to run multiple times)
### Delete a Group

```yaml
- name: Remove developers group
  group:
    name: developers
    state: absent
```
### Create Group with Specific GID
```yaml
- name: Create group with custom GID
  group:
    name: appgroup
    gid: 1050
    state: present
```

---

## 2️⃣ Managing Users in Ansible

### Create a User

```yaml
- name: Create user john
  user:
    name: john
    state: present
```
### Create User with Custom Shell

```yaml
- name: Create user with shell
  user:
    name: john
    shell: /bin/bash
    state: present
```
### Create User with Home Directory
```yaml
- name: Create user with home
  user:
    name: john
    home: /home/john
    create_home: yes
```
### Add User to a Group
```yaml
- name: Add user to group
  user:
    name: john
    groups: developers
    append: yes
```
⚠ Important:

* `append: yes` → Adds to existing groups
* Without append → It replaces all groups
### Delete a User

```yaml
- name: Remove user
  user:
    name: john
    state: absent
```
### Delete User with Home Directory

```yaml
- name: Remove user completely
  user:
    name: john
    state: absent
    remove: yes
```

✔ `remove: yes` deletes home directory

---

## 3️⃣ Setting Password for User

Password must be encrypted (hashed).

Example using SHA512:

```yaml
- name: Create user with password
  user:
    name: john
    password: "{{ 'MyPassword123' | password_hash('sha512') }}"
```

⚠ Best Practice: Use Ansible Vault for passwords.

---

## 4️⃣ Create Multiple Users Using Loop ⭐

```yaml
- hosts: web
  vars:
    users:
      - dev1
      - dev2
      - dev3

  tasks:
    - name: Create users
      user:
        name: "{{ item }}"
        state: present
      loop: "{{ users }}"
```

✔ Real-world automation example

---

## 5️⃣ Advanced Example (Real DevOps Style)

```yaml
- hosts: web
  vars:
    users:
      - name: dev1
        group: developers
      - name: dev2
        group: testers

  tasks:
    - name: Create groups
      group:
        name: "{{ item.group }}"
        state: present
      loop: "{{ users }}"
      loop_control:
        label: "{{ item.group }}"

    - name: Create users and assign groups
      user:
        name: "{{ item.name }}"
        groups: "{{ item.group }}"
        append: yes
        state: present
      loop: "{{ users }}"
```

✔ Creates groups first
✔ Then creates users
✔ Assigns them properly

---

## 6️⃣ Important Parameters of `user` Module

| Parameter   | Purpose                |
| ----------- | ---------------------- |
| name        | Username               |
| state       | present / absent       |
| shell       | User shell             |
| home        | Home directory         |
| create_home | Create home directory  |
| groups      | Assign groups          |
| append      | Add to existing groups |
| uid         | Custom user ID         |
| password    | Encrypted password     |
| remove      | Remove home directory  |

---

## 7️⃣ Important Parameters of `group` Module

| Parameter | Purpose          |
| --------- | ---------------- |
| name      | Group name       |
| state     | present / absent |
| gid       | Custom group ID  |

---

# 🔥 Best Practices

* Always create group before user (if custom group needed)
* Use loops for bulk user creation
* Use Ansible Vault for passwords
* Avoid hardcoding credentials
* Use append: yes carefully

---

# 🚀 Quick Revision

* `group` → Manage groups
* `user` → Manage users
* `append: yes` prevents overwriting groups
* `remove: yes` deletes home directory
* Use loops for multiple users
* Password must be hashed

---
