# 🔹 Templates in Ansible (Jinja2 Templating)

Templates allow you to create **dynamic configuration files** using variables.
Ansible uses **Jinja2 templating engine** for this.

## 1️⃣ Why Templates Are Needed?

Without template ❌ (Static file)

```
server {
  listen 80;
  server_name example.com;
}
```

If you want different ports/domains for dev, test, prod → you must create multiple files.

With template ✅

```
server {
  listen {{ http_port }};
  server_name {{ domain_name }};
}
```

Now values come from variables.

---

## 2️⃣ Template File Extension

Template files usually end with:

```
.j2
```

Example:

```
nginx.conf.j2
```

---

## 3️⃣ Using Template Module

```yaml
- name: Deploy nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
```

Explanation:

| Parameter | Meaning                          |
| --------- | -------------------------------- |
| src       | Template file                    |
| dest      | Target location on remote server |

---

## 4️⃣ Where Templates Are Stored?

### In normal project:

```
project/
 ├── templates/
 │     └── nginx.conf.j2
 └── site.yml
```

### Inside role:

```
roles/
 └── nginx/
      └── templates/
           └── nginx.conf.j2
```

✔ If using roles → template must be inside role’s `templates/` directory.

---

## 5️⃣ Basic Template Example

### Template file: nginx.conf.j2

```nginx
server {
  listen {{ http_port }};
  server_name {{ domain_name }};
}
```

### Playbook:

```yaml
- hosts: web
  vars:
    http_port: 80
    domain_name: example.com

  tasks:
    - name: Deploy nginx config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
```

✔ Values automatically replaced.

---

## 6️⃣ Template + Handler (Real DevOps Pattern) ⭐

Very common production usage.

```yaml
- name: Deploy nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart nginx
```

Handler:

```yaml
handlers:
  - name: Restart nginx
    service:
      name: nginx
      state: restarted
```

✔ Service restarts only if file changes
✔ Efficient automation

Interview Favorite ⭐

---


## 7️⃣ Where Variables Come From?

Template can use variables from:

* vars in playbook
* group_vars
* host_vars
* inventory
* set_fact
* facts
* extra vars

Everything available to playbook is available in template.

---

## 8️⃣ Accessing Facts in Template

```jinja
Server Name: {{ ansible_hostname }}
OS: {{ ansible_distribution }}
```

✔ Automatically collected system data

---

## 9️⃣ Common Errors in Templates

❌ Missing variable
Fix:

```jinja
{{ variable | default('value') }}
```

❌ Wrong indentation (YAML issue)
❌ Incorrect Jinja syntax (`{% %}` vs `{{ }}`)

---

## 1️⃣0️⃣ Advanced: Template Validation ⭐

You can validate before applying:

```yaml
- name: Deploy nginx config safely
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
    validate: "nginx -t -c %s"
```

✔ Ensures config syntax is valid before replacing
✔ Production best practice

---

## 1️⃣1️⃣ Real Production Example

```yaml
- hosts: web
  vars:
    http_port: 80
    domain_name: myapp.com
    backend_servers:
      - 10.0.0.1
      - 10.0.0.2

  tasks:
    - name: Deploy config
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/nginx.conf
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

✔ Dynamic
✔ Safe
✔ Idempotent

---

## 🔥 Interview Questions ⭐

### Q1: Which module is used for templates?

`template`

---

### Q2: What templating engine does Ansible use?

Jinja2

---

### Q3: Difference between `{{ }}` and `{% %}`?

| Syntax | Purpose         |
| ------ | --------------- |
| {{ }}  | Print variable  |
| {% %}  | Logic (if, for) |

---

### Q4: Why use handler with template?

To restart service only if config changes.

---

### Q5: Where are templates stored in roles?

`roles/<role_name>/templates/`

---

# 🚀 Quick Revision Summary

* Templates = Dynamic config files
* Use `.j2` extension
* Use `template` module
* Supports conditions & loops
* Works with handlers
* Variables auto available
* Use validate for production safety

