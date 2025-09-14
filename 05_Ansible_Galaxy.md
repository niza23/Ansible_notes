
# 🌍 Ansible Galaxy – Sharing and Using Roles

This repository explains how to **publish your own Ansible roles to Ansible Galaxy** and how to **reuse roles from others**.

---

## 🔹 What is Ansible Galaxy?

* **Ansible Galaxy** is the official hub for finding, reusing, and sharing Ansible roles and collections.
* Think of it as **Docker Hub but for Ansible automation**.
* Instead of reinventing the wheel, you can pull roles written by the community (e.g., installing nginx, configuring databases).

👉 Example:

```bash
ansible-galaxy install geerlingguy.nginx
```

---
**how to install and use a role**.

---

## 🔹 Installing a Role

To install a role from **Ansible Galaxy**:

```bash
ansible-galaxy install geerlingguy.nginx
```

This will download the role into your local roles directory (usually `~/.ansible/roles/`).

---

## 🔹 Using the Role in a Playbook

Once installed, you can call the role inside a playbook:

```yaml
- hosts: webservers
  become: yes
  roles:
    - geerlingguy.nginx
```

Run it with:

```bash
ansible-playbook -i inventory.ini site.yml
```

That’s it ✅ — **one command to install, one snippet to use**.

---
**how to Publish a role in Galaxy**.

---
## 🔹 Why Publish to Galaxy?

✅ Share your automation with the community
✅ Reuse roles across multiple projects
✅ Standardize automation in your team
✅ Build credibility as a DevOps engineer

---

## 🔹 Role Structure Required for Galaxy

Before publishing, your role must follow the **standard directory structure**:

```
my_role/
├── defaults/        # Default variables (lowest priority)
│   └── main.yml
├── files/           # Static files
├── handlers/        # Handlers (triggered actions)
│   └── main.yml
├── meta/            # Metadata (author, license, dependencies)
│   └── main.yml
├── tasks/           # Main tasks executed by the role
│   └── main.yml
├── templates/       # Jinja2 templates
├── tests/           # Basic tests for the role
│   ├── inventory
│   └── test.yml
└── vars/            # Variables (higher priority than defaults)
    └── main.yml
```

📌 Not all folders are required — but **meta/main.yml is mandatory** for publishing.

---

## 🔹 Step 1 – Verify Ansible Galaxy CLI

Make sure the CLI is installed:

```bash
ansible-galaxy --version
```

---

## 🔹 Step 2 – Push Role to GitHub

Your role must be hosted in a **public GitHub repo**.

```bash
cd <role-name>
git init
git remote add origin https://github.com/<your_username>/<role-name>.git
git add .
git commit -m "Initial commit"
git push -u origin main
```

---

## 🔹 Step 3 – Import Role into Ansible Galaxy

Use the Ansible Galaxy CLI:

```bash
ansible-galaxy role import <your_github_username> <role-name>
```

Or use the [Galaxy Web UI](https://galaxy.ansible.com/):

1. Sign in with GitHub
2. Link your repo
3. Import the role

---

## 🔹 Step 4 – Use the Role from Galaxy

Once published, others can install your role with:

```bash
ansible-galaxy install <your_github_username>.<role-name>
```

Example:

```bash
ansible-galaxy install myuser.nginx
```

---

## 🔹 Example Workflow

1. Create a role:

```bash
ansible-galaxy init nginx
```

2. Write tasks inside `roles/nginx/tasks/main.yml`:

```yaml
- name: Install nginx
  apt:
    name: nginx
    state: present
```

3. Push to GitHub.
4. Import into Ansible Galaxy.
5. Install from Galaxy on another machine:

```bash
ansible-galaxy install myuser.nginx
```

---

## ✅ Summary

* **Ansible Galaxy** is where you share and consume roles.
* Roles must follow the **standard directory structure**.
* Publishing involves:

  1. Create role (`ansible-galaxy init`)
  2. Push to GitHub
  3. Import into Galaxy
  4. Install and reuse across projects

---


