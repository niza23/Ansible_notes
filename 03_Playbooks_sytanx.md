

# 📘 Ansible – Playbooks & Core Concepts

This repository is a **comprehensive guide to Ansible basics and core concepts**, written in simple terms with practical examples.
It is designed to be **DevOps interview-ready** and a quick refresher for day-to-day automation.

---

## 🔹 YAML Basics

Ansible Playbooks are written in **YAML (YAML Ain’t Markup Language)**.
It is a **human-readable data format** widely used for configuration files.

### ✅ Examples

**Key-Value**

```yaml
name: Ansible
version: 2.16
```

**List**

```yaml
fruits:
  - apple
  - banana
  - mango
```

**Dictionary (Nested)**

```yaml
server:
  name: web01
  ip: 192.168.1.10
```

### ⚠️ Rules of YAML

* Use **spaces**, not tabs
* Indentation matters (2 spaces is common)
* Strings don’t need quotes unless they contain special characters

---

## 🔹 Playbooks

A **Playbook** is a YAML file that defines a set of automation steps (tasks) for managed nodes.
It is the **heart of Ansible automation** – reusable, repeatable, and human-readable.

Think of it as a **script of ad-hoc commands**, but **structured and version-controlled**.

### 🔑 Syntax of a Playbook

```yaml
- name: <Play Name>
  hosts: <target-hosts>
  become: <yes/no>
  vars:
    key: value
  tasks:
    - name: <Task Name>
      <module>:
        key1: value1
        key2: value2
```

**Explanation**

* `name` → description of the play
* `hosts` → target servers (from inventory)
* `become` → run with sudo privileges (yes/no)
* `vars` → define variables inside the play
* `tasks` → list of steps to execute
* `modules` → actual building blocks (e.g., `apt`, `yum`, `service`, `copy`)

---

## 🔹 Example Playbook

**install\_nginx.yaml**

```yaml
- name: Install and configure Nginx
  hosts: webserver
  become: yes
  vars:
    pkg_name: nginx

  tasks:
    - name: Install nginx
      apt:
        name: "{{ pkg_name }}"
        state: present

    - name: Copy custom index page
      copy:
        src: files/index.html
        dest: /var/www/html/index.html

    - name: Start and enable nginx service
      service:
        name: nginx
        state: started
        enabled: yes
```

▶️ Run the playbook:

```bash
ansible-playbook -i inventory.ini install_nginx.yaml
```

---

## 🔹 Core Ansible Concepts

### 1. Play

A **Play** is a single execution unit inside a playbook.
It defines **which hosts** to target and **what tasks** to run.

```yaml
- name: Install and configure Nginx
  hosts: webservers
  tasks:
    - name: Install Nginx
      apt:
        name: nginx
        state: present
```

---

### 2. Task

A **Task** is an individual action within a play.
Each task uses a **module** to perform work on managed nodes.

```yaml
- name: Install Nginx
  apt:
    name: nginx
    state: present

- name: Start Nginx service
  service:
    name: nginx
    state: started
```

---

### 3. Module

A **Module** is a reusable building block that performs a specific action.
Examples: `apt`, `yum`, `service`, `copy`, `template`, `user`.

```yaml
- name: Install Nginx
  apt:
    name: nginx
    state: present
```

---

### 4. Collection

A **Collection** is a package format that bundles roles, modules, and plugins together.
It makes automation content easier to share and reuse.

**Example Structure**

```
my_collection/
├── roles/
│   └── my_role/
│       └── tasks/
│           └── main.yml
├── plugins/
│   └── modules/
│       └── my_module.py
└── README.md
```

**Usage Example**

```yaml
- name: Use a custom module from a collection
  community.general.my_module:
    option: value
```

---

## 🔹 Commonly Used Modules

* `ping` → check connectivity
* `command` → run shell commands
* `apt` / `yum` → install packages
* `service` → start/stop/restart services
* `copy` → copy files
* `template` → copy files with Jinja2 variables
* `user` → manage users

---

## ✅ Summary

* **YAML** is the language for Ansible Playbooks
* A **Playbook** is a collection of **Plays**
* A **Play** contains **Tasks**, and each task uses a **Module**
* **Collections** package roles, modules, and plugins for sharing
* Ansible enables **idempotent, repeatable, infrastructure automation**

---
