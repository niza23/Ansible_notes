
# 📘 Ansible Playbooks

## 🔹 What is YAML?
- **YAML** = “YAML Ain’t Markup Language”  
- A **human-readable data format** used for configuration files.  
- Ansible Playbooks are written in YAML.  
- Uses **indentation (spaces, not tabs)** to represent structure.  

### ✅ YAML Basics
**Key-Value**
```yaml
name: Ansible
version: 2.16
````

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

⚠️ **Rules of YAML**

* Use spaces, not tabs.
* Indentation matters (2 spaces is common).
* Strings don’t need quotes unless special characters are used.

---

## 🔹 What is a Playbook?

* A **Playbook** is a YAML file that defines a set of instructions (tasks) to run on managed nodes.
* It is the **core of Ansible automation**: reusable, repeatable, and human-readable.
* Think of it like a **script of ad-hoc commands**, but structured and version-controlled.

---

## 🔹 Syntax of a Playbook

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

### Explanation

* **`- name`** → description of the play.
* **`hosts`** → target servers (from inventory).
* **`become`** → run with sudo privileges (`yes` or `no`).
* **`vars`** → define variables inside the play.
* **`tasks`** → list of steps to execute.
* **Modules** → actual building blocks (e.g., `apt`, `yum`, `service`, `copy`).

---

## 🔹 Example Playbook

`install_nginx.yaml`

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

Run the playbook:

```bash
ansible-playbook -i inventory.ini install_nginx.yaml
```

---

## 🔹 Commonly Used Modules in Playbooks

* **ping** → check connectivity
* **command** → run shell commands
* **apt / yum** → install packages
* **service** → start/stop/restart services
* **copy** → copy files
* **template** → copy files with Jinja2 variables
* **user** → manage users

---

## ✅ Summary

* **YAML** is the format used for Playbooks.
* A **Playbook** = Plays → Tasks → Modules.
* **Syntax** includes → `name`, `hosts`, `become`, `vars`, `tasks`.
* **Modules** are the building blocks that perform actual actions.

👉 Next: We’ll explore **Variables and Handlers** to make playbooks more powerful.

