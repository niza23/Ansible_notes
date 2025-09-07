
# 🔐 Authentication in Ansible

## 🔹 Why is Authentication Required?
Ansible connects from the **Control Node** (your laptop/server) to the **Managed Nodes** (servers you want to configure).  
To run tasks remotely, Ansible needs a **secure authentication method**.  

Without authentication:
- Ansible cannot log in to remote systems.
- Commands and playbooks cannot be executed.

---

## 🔹 Authentication Methods in Ansible
1. **SSH Key-based Authentication (Recommended ✅)**
   - More secure, no need to enter password each time.
   - Uses a private key (on control node) and a public key (on managed node).
   - Ideal for automation and CI/CD.

2. **Password-based Authentication**
   - Requires entering a password each time unless stored in a vault.
   - Less secure and harder to manage at scale.
   - Sometimes needed when key-based authentication is not possible.

---

## 🔹 Example Setup
We will configure **two EC2 instances** as managed nodes:

- **Control Node**: Local system with Ansible installed.
- **Managed Node 1 (Web Server)**: EC2 with **SSH Key Authentication**.
- **Managed Node 2 (DB Server)**: EC2 with **Password Authentication**.

---

## 🔹 Step 1: SSH Key-based Authentication

### 1. Generate SSH Key (if not already done)
```bash
ssh-keygen -t rsa -b 4096
````

This will create:

* Private key: `~/.ssh/id_rsa`
* Public key: `~/.ssh/id_rsa.pub`

### 2. Copy Public Key to Managed Node 1

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ec2-user@<EC2_PUBLIC_IP>
```

Alternatively (for AWS EC2):

```bash
ssh -i my-ec2-key.pem ec2-user@<EC2_PUBLIC_IP>
```

### 3. Verify Login Without Password

```bash
ssh ec2-user@<EC2_PUBLIC_IP>
```

✅ If it logs in without asking for a password, key-based authentication is working.

---

## 🔹 Step 2: Password-based Authentication

For Managed Node 2, we’ll allow Ansible to connect using a password.

### 1. Enable SSH Password Authentication (on EC2)

Edit `/etc/ssh/sshd_config` on Managed Node 2:

```
PasswordAuthentication yes
```

Restart SSH:

```bash
sudo systemctl restart sshd
```

### 2. Verify Password Login

```bash
ssh user@<EC2_PUBLIC_IP>
# Enter password manually
```

---

## 🔹 Step 3: Ansible Inventory Configuration

Create an `inventory.ini` file:

```ini
[webserver]
ec2-web ansible_host=<EC2_IP_1> ansible_user=ec2-user ansible_ssh_private_key_file=~/.ssh/id_rsa

[dbserver]
ec2-db ansible_host=<EC2_IP_2> ansible_user=ubuntu ansible_password=MyStrongPassword123
```

---

## 🔹 Step 4: Testing Connections

Ping all servers:

```bash
ansible all -i inventory.ini -m ping
```

Expected output:

* `ec2-web` responds with `"pong"` via **SSH key**
* `ec2-db` responds with `"pong"` via **password authentication**

---

## ✅ Summary

* Authentication is required for Ansible to connect to managed nodes.
* **SSH key-based auth** is the preferred and secure way.
* **Password-based auth** works but is less secure and harder to scale.
* You can manage a mix of servers with different authentication methods by configuring the inventory file.

```

