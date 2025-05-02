### Step 1: Install MongoDB on the VPS

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
   

   Configure MongoDB for Remote Access:
   By default, MongoDB only listens on localhost (127.0.0.1):

   sudo vim /etc/mongod.conf
     net:
       port: 27017
       bindIp: 0.0.0.0         // from 127.0.0.1 -> 0.0.0.0


   Restart mongodb

   sudo systemctl restart mongod
   

   Open the Firewall (if enabled):
   If your VPS has a firewall (e.g., UFW), allow port 27017:

   sudo ufw allow 27017/tcp
   
   If you’re using a cloud provider’s firewall (e.g., AWS, GCP), ensure port 27017 is open in the security group or firewall rules.

  Create an Admin User (Recommended for Security):

   mongosh
   use admin
   db.createUser({
     user: "admin",
     pwd: "your_secure_password",
     roles: [ { role: "root", db: "admin" } ]
   })
   
   exit
   

    Enable Authentication:
    Edit the MongoDB config file again:
   
    sudo nano /etc/mongod.conf
    
    Add or uncomment the security section:
    security:
      authorization: "enabled"
    
    Restart MongoDB:
    sudo systemctl restart mongod
    

### Step 2: Connect to MongoDB from MongoDB Compass
1. Install MongoDB Compass:
   - Download MongoDB Compass from the [official website](https://www.mongodb.com/products/tools/compass) for your OS (Windows, macOS, or Linux).
   - Install it by following the installer instructions.

2. Get Your VPS IP Address:
   Find your VPS’s public IP address (e.g., via your VPS provider’s dashboard or by running curl ifconfig.me on the VPS).

3. Open MongoDB Compass:
   - Launch Compass on your local machine.
     Add connection string:
     mongodb://admin:your_secure_password@<VPS_PUBLIC_IP>:27017/?authSource=admin
     
     Replace <VPS_PUBLIC_IP> with your VPS’s public IP and your_secure_password with the password you set for the admin user.


### Step 3: Troubleshooting
- Connection Refused:
  - Ensure MongoDB is running (sudo systemctl status mongod).
  - Verify port 27017 is open (sudo netstat -tuln | grep 27017).
  - Check your VPS firewall and cloud provider’s security group rules.
- Authentication Errors:
  - Confirm the username, password, and authentication database are correct.
  - Ensure authorization: "enabled" is set in /etc/mongod.conf.
- Bind Address Issues:
  - Double-check that bindIp: 0.0.0.0 is set in /etc/mongod.conf.



If you encounter issues or need clarification, let me know your VPS OS, any error messages, or specific requirements!
