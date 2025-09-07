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

## 🔹 Example Inventory File (Mix of Both)

You can mix both methods in your Ansible `inventory.ini`:

```ini
[webserver]
ec2-web ansible_host=<EC2_IP_1> ansible_user=ubuntu ansible_ssh_private_key_file=~/.ssh/id_rsa

[dbserver]
ec2-db ansible_host=<EC2_IP_2> ansible_user=ubuntu ansible_password=MyStrongPassword123
```

---

## 🔹 Testing Authentication

Run a simple Ansible ping test:

```bash
ansible all -i inventory.ini -m ping
```

Expected result:

* `ec2-web` responds with **pong** using SSH key authentication.
* `ec2-db` responds with **pong** using password authentication.

---

## ✅ Summary

* Authentication is mandatory for Ansible to connect to managed nodes.
* **SSH keys** are secure and preferred.
* **Passwords** work but are less secure and harder to automate.
* On AWS EC2:

  * You can import your own key using `ssh-copy-id`.
  * You can enable password authentication by editing SSH config.


---
