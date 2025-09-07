# 🔐 Authentication in Ansible

## 🔹 Why is Authentication Required?
Ansible connects from the **Control Node** (your laptop/server) to the **Managed Nodes** (servers you want to configure).  
To run tasks remotely, Ansible needs a **secure authentication method**.  

Without authentication:
- Ansible cannot log in to remote systems.
- Commands and playbooks cannot be executed.

---

## 🔹 Authentication Methods
1. **SSH Key-based Authentication (Recommended ✅)**
   - Secure and reusable for automation.
   - No need to type passwords each time.
   - Works by sharing your **public key** with the managed node.

2. **Password-based Authentication**
   - Requires entering a password on each login.
   - Easier to set up but less secure and harder to scale.
   - Can be useful for testing or when key-based auth isn’t available.

---

## 🔹 Using Public Key Authentication (EC2 Example)

Normally with AWS EC2 you log in with a `.pem` file:

```bash
ssh -i my-key.pem ubuntu@<INSTANCE_PUBLIC_IP>
````

But we can simplify this with `ssh-copy-id`:

```bash
ssh-copy-id -f "-o IdentityFile <PATH_TO_PEM_FILE>" ubuntu@<INSTANCE_PUBLIC_IP>
```

Explanation:

* `ssh-copy-id` → copies your **local public key** to the remote server.
* `-f` → forces overwrite if needed.
* `-o IdentityFile` → specifies the private key (`.pem`) to connect with initially.
* `ubuntu@<INSTANCE_PUBLIC_IP>` → default username for Ubuntu EC2.

✅ Once done, you can log in without needing the `.pem` file every time:

```bash
ssh ubuntu@<INSTANCE_PUBLIC_IP>
```

---

## 🔹 Using Password Authentication (EC2 Example)

By default, EC2 instances **disable password authentication**.
To enable it:

1. Edit the SSH config file:

   ```bash
   sudo nano /etc/ssh/sshd_config.d/60-cloudimg-settings.conf
   ```

2. Update this line:

   ```
   PasswordAuthentication yes
   ```

3. Restart the SSH service:

   ```bash
   sudo systemctl restart ssh
   ```

4. Now you can log in with:

   ```bash
   ssh ubuntu@<INSTANCE_PUBLIC_IP>
   # You’ll be prompted for the password
   ```

---

## 🔹 Ansible Inventory

The **inventory** is how Ansible knows *which hosts* to connect to and *how to connect*.
It can be written in **two main formats**:

### 1. INI-style Inventory (classic)

```ini
[webserver]
ec2-web ansible_host=<EC2_IP_1> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

[dbserver]
ec2-db ansible_host=<EC2_IP_2> ansible_user=ubuntu ansible_password=MyStrongPassword123
```

* Groups like `[webserver]` and `[dbserver]` help organize hosts.
* You can mix SSH key and password authentication.

### 2. YAML-style Inventory (modern)

```yaml
all:
  children:
    webserver:
      hosts:
        ec2-web:
          ansible_host: <EC2_IP_1>
          ansible_user: ubuntu
          ansible_ssh_private_key_file: ~/.ssh/id_rsa
    dbserver:
      hosts:
        ec2-db:
          ansible_host: <EC2_IP_2>
          ansible_user: ubuntu
          ansible_password: MyStrongPassword123
```

* More structured and better for large environments.
* YAML is also used in playbooks, so this keeps everything consistent.

---

## 🔹 Ad-hoc Commands Basics

Once authentication and inventory are set up, we can start running **ad-hoc commands**.
These are **one-liners** that let you execute tasks across one or more managed nodes — no playbook required.

### Syntax

```bash
ansible <host-pattern> -i <inventory> -m <module> -a "<arguments>"
```

* `<host-pattern>` → the target (e.g., `all`, `webserver`, `dbserver`)
* `-i` → specify the inventory file
* `-m` → the Ansible module to use
* `-a` → arguments for the module

---

### 1. Test Connectivity (ping module)

```bash
ansible all -i inventory.ini -m ping
```

Expected result:

```json
"ping": "pong"
```

---

### 2. Run Shell Commands (command module)

```bash
ansible webserver -i inventory.ini -m command -a "uptime"
```

---

### 3. Install Packages (apt/yum module)

On Ubuntu/Debian:

```bash
ansible webserver -i inventory.ini -m apt -a "name=nginx state=present" --become
```

On CentOS/RHEL:

```bash
ansible webserver -i inventory.ini -m yum -a "name=httpd state=present" --become
```

---

### 4. Copy Files (copy module)

```bash
ansible dbserver -i inventory.ini -m copy -a "src=~/myfile.txt dest=/tmp/myfile.txt"
```

---

### 5. Restart Services (service module)

```bash
ansible webserver -i inventory.ini -m service -a "name=nginx state=restarted" --become
```
---

## 🔹 Ad-hoc vs Playbook

### 🔸 Ad-hoc Commands
- One-liners executed directly from the CLI.
- Great for **quick tests** or **one-time tasks**.
- Do not get stored for reuse (unless you copy/paste somewhere).
- Example:
  ```bash
  ansible webserver -i inventory.ini -m apt -a "name=nginx state=present" --become
````

### 🔸 Playbooks

* Written in **YAML files** and stored in your repo.
* Define a **set of tasks** that can be reused and version-controlled.
* Ideal for **repeatable automation** and complex orchestration.
* Example (`install_nginx.yaml`):

  ```yaml
  - name: Install and configure nginx
    hosts: webserver
    become: yes
    tasks:
      - name: Install nginx
        apt:
          name: nginx
          state: present

      - name: Start and enable nginx service
        service:
          name: nginx
          state: started
          enabled: yes
  ```

Run with:

```bash
ansible-playbook -i inventory.ini install_nginx.yaml
```

---

### ✅ Key Differences

| Feature         | Ad-hoc Commands           | Playbooks                             |
| --------------- | ------------------------- | ------------------------------------- |
| **Format**      | CLI one-liners            | YAML files                            |
| **Use Case**    | Quick tasks, testing      | Repeatable automation, orchestration  |
| **Reusability** | Not reusable              | Fully reusable & version-controlled   |
| **Complexity**  | Simple, single action     | Multiple tasks, handlers, roles, etc. |
| **Best For**    | Immediate fixes or checks | Production-ready automation           |

---

## ✅ Summary

* Authentication is mandatory for Ansible to connect to managed nodes.
* **SSH keys** are secure and preferred.
* **Passwords** work but are less secure and harder to automate.
* Inventories can be written in **INI** or **YAML** formats.
* Ad-hoc commands are quick, powerful one-liners for testing and small tasks.
* Use **ad-hoc commands** for **quick checks or temporary fixes**.
* Use **playbooks** when you need **repeatability, structure, and collaboration**.
* In real DevOps projects, playbooks are the foundation, while ad-hoc commands are mostly for testing or troubleshooting.
