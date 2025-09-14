

# 📘 Ansible Roles 

This repository explains **Ansible Roles** in simple terms with examples and best practices.
It is designed for anyone starting with Ansible who wants to understand **why roles matter**, how they are structured, and how to use them in real-world automation.

---

## 🔹 What is an Ansible Role?

An **Ansible Role** is a **reusable, self-contained unit of automation**.
It organizes **tasks, variables, files, templates, and handlers** into a **structured format**.

👉 Think of it as a **“building block”** for automation:

* Instead of writing one big playbook with all tasks,
* You split it into smaller, reusable roles (e.g., one role for **nginx**, one for **postgres**, one for **users**).

This approach makes automation **modular, maintainable, reusable, and consistent** across projects.

---

## 🔹 Why Use Roles?

* **🧩 Modularity** → Break complex playbooks into smaller, logical parts
* **♻️ Reusability** → Use the same role across multiple playbooks/projects
* **🛠️ Maintainability** → Easy to update in one place, apply everywhere
* **📖 Readability** → Cleaner playbooks (abstracts details into roles)
* **🤝 Collaboration** → Teams can work on separate roles independently
* **✅ Consistency** → Apply the same configuration uniformly across environments

---

## 🔹 Key Components of a Role

Each role has several optional components:

* **Tasks** → The main list of actions the role performs
* **Handlers** → Triggered when a task changes something (e.g., restart nginx)
* **Files** → Static files to copy to managed hosts
* **Templates** → Dynamic files using **Jinja2** templating
* **Vars** → Variables defined for the role
* **Defaults** → Default variables (can be overridden later)
* **Meta** → Metadata about the role (dependencies, author, etc.)
* **Library** → Custom modules/plugins if needed
* **Module\_defaults** → Default module parameters
* **Lookup\_plugins** → Custom lookup plugins

---

## 🔹 Role Directory Structure

Ansible enforces a **standard structure** for roles.

```
<role_name>/
  ├── defaults/
  │   └── main.yml        # Default variables (lowest priority)
  ├── files/              # Static files
  ├── handlers/
  │   └── main.yml        # Handlers (e.g., restart service)
  ├── meta/
  │   └── main.yml        # Metadata (dependencies, author info)
  ├── tasks/
  │   └── main.yml        # Main list of tasks to execute
  ├── templates/          # Jinja2 templates
  ├── vars/
      └── main.yml        # Variables (higher priority than defaults)
```

📌 Not all folders are required — only use what you need.

---

## 🔹 Example Role Usage

Imagine you create a role called **nginx** to install and configure the web server.

**Playbook Example:**

```yaml
- hosts: webservers
  become: yes
  roles:
    - nginx
```

When you run this, Ansible will automatically look for the role’s **tasks/main.yml** and execute it.

---

## 🔹 Example – Tasks in a Role

**roles/nginx/tasks/main.yml**

```yaml
---
- name: Install nginx
  apt:
    name: nginx
    state: present

- name: Deploy nginx config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart nginx
```

**roles/nginx/handlers/main.yml**

```yaml
---
- name: Restart nginx
  service:
    name: nginx
    state: restarted
```

👉 With this setup:

* If the config file changes, the handler will restart nginx.
* If nothing changes, nginx won’t restart unnecessarily (**idempotency**).

---

## 🔹 Visual Overview – Roles in Action

Here’s how **Playbooks, Roles, Tasks, and Handlers** fit together:

```mermaid
flowchart TD
    A[Playbook] --> B[Role: nginx]
    B --> C[Tasks]
    B --> D[Handlers]
    B --> E[Files]
    B --> F[Templates]
    B --> G[Vars]
    B --> H[Defaults]
    B --> I[Meta]

    C --> C1[Install nginx package]
    C --> C2[Deploy config file]
    C2 --> D1[Restart nginx handler]
```

📌 This diagram shows that a **Playbook calls a Role**, which organizes everything into tasks, handlers, variables, and templates.

---

## 🔹 Best Practices for Roles

✅ Use roles for **any reusable logic** (e.g., DB setup, web server, monitoring agents)
✅ Keep roles **single-purpose** (don’t mix unrelated tasks)
✅ Use **defaults** for safe variable values
✅ Store sensitive data in **Ansible Vault**
✅ Publish reusable roles on **Ansible Galaxy** for others to use

---

## ✅ Summary

* **Ansible Roles** help you organize automation into **reusable, structured units**
* They contain **tasks, handlers, files, templates, vars, and defaults**
* Roles make automation **modular, consistent, and easy to maintain**
* They follow a **standard directory structure** recognized by Ansible
* Used correctly, roles save time and make collaboration easier

---

The `ansible-galaxy` command is used to **create, install, and manage roles**. When you want to create a new role, you use the **`init`** subcommand.

---

## 🔹 Create a New Role

```bash
ansible-galaxy init <role_name>
```

Example:

```bash
ansible-galaxy init nginx
```

This will create a directory structure like this:

```
nginx/
├── defaults/
│   └── main.yml
├── files/
├── handlers/
│   └── main.yml
├── meta/
│   └── main.yml
├── tasks/
│   └── main.yml
├── templates/
├── tests/
│   ├── inventory
│   └── test.yml
└── vars/
    └── main.yml
```

📌 By default, `ansible-galaxy init` creates **all the standard role directories** for you, even if you don’t use them all.

---

## 🔹 Using the Role in a Playbook

Once created, you can reference the role inside a playbook:

```yaml
- hosts: webservers
  become: yes
  roles:
    - nginx
```

When you run the playbook, Ansible automatically looks inside the `nginx/` role folder and executes `tasks/main.yml`.

---

## 🔹 Options You Can Use

* **`--init-path`** → specify where to create the role

```bash
ansible-galaxy init nginx --init-path ./roles
```

This will create the role inside a `roles/` directory (best practice).

* **`--offline`** → don’t check Ansible Galaxy (useful when offline).

* **`--force`** → overwrite if the role already exists.

---

## 🔹 Workflow Example

1. Create a roles directory (if not already present):

   ```bash
   mkdir roles
   cd roles
   ```

2. Create a new role:

   ```bash
   ansible-galaxy init nginx
   ```

3. Add tasks inside `roles/nginx/tasks/main.yml`:

   ```yaml
   ---
   - name: Install nginx
     apt:
       name: nginx
       state: present
   ```

4. Call the role in your playbook (`site.yml`):

   ```yaml
   - hosts: webservers
     become: yes
     roles:
       - nginx
   ```

5. Run the playbook:

   ```bash
   ansible-playbook -i inventory site.yml
   ```

---

✅ In short:

* Use `ansible-galaxy init <role>` → creates a role skeleton
* Add your tasks, handlers, templates, etc. inside that role
* Reference the role in your playbooks

---
