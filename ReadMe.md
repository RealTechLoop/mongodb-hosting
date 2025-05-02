# 🚀 MongoDB Remote Setup on VPS

This guide walks you through installing MongoDB on a VPS, enabling remote access, and connecting via MongoDB Compass.

---

## 🛠️ Step 1: Install MongoDB on the VPS

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install -y gnupg

curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | sudo gpg --dearmor -o /usr/share/keyrings/mongodb-server-7.0.gpg

echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list

sudo apt update
sudo apt install -y mongodb-org

sudo systemctl start mongod
sudo systemctl enable mongod
sudo systemctl status mongod

mongod --version
```

---

## 🔐 Configure MongoDB for Remote Access

By default, MongoDB only listens on `localhost (127.0.0.1)`. Change this:

```bash
sudo nano /etc/mongod.conf
```

**Update this section:**

```yaml
net:
  port: 27017
  bindIp: 0.0.0.0  # from 127.0.0.1 to 0.0.0.0
```

Then restart MongoDB:

```bash
sudo systemctl restart mongod
```

---

## 🔥 Open Firewall Port (if using UFW)

```bash
sudo ufw allow 27017/tcp
```

If using a cloud provider like AWS/GCP, ensure port `27017` is open in their security group/firewall settings.

---

## 🧑‍💻 Create Admin User

```bash
mongosh

use admin

db.createUser({
  user: "admin",
  pwd: "your_secure_password",
  roles: [ { role: "root", db: "admin" } ]
})

exit
```

---

## 🔒 Enable Authentication

```bash
sudo nano /etc/mongod.conf
```

**Add or uncomment:**

```yaml
security:
  authorization: "enabled"
```

Restart MongoDB:

```bash
sudo systemctl restart mongod
```

---

## 🖥️ Step 2: Connect with MongoDB Compass

1. **Install MongoDB Compass**
   [Download here](https://www.mongodb.com/products/tools/compass)

2. **Get VPS IP Address**
   Run on VPS:

   ```bash
   curl ifconfig.me
   ```

3. **Connect using this URI**

   ```
   mongodb://admin:<your_secure_password>@<VPS_PUBLIC_IP>:27017/?authSource=admin
   ```

---

## 🧩 Troubleshooting

### ❌ Connection Refused

* Ensure MongoDB is running:

  ```bash
  sudo systemctl status mongod
  ```
* Check port:

  ```bash
  sudo netstat -tuln | grep 27017
  ```

### 🔑 Authentication Errors

* Verify credentials and `authSource` in Compass.
* Ensure `authorization: "enabled"` is configured.

### 📶 Bind IP Issues

* Confirm `bindIp: 0.0.0.0` is correctly set.

---

## ✅ Done!

You can now connect to your MongoDB instance remotely and securely.


Let me know if you'd like me to add badges, deployment instructions, or markdown styling for GitHub Pages.
