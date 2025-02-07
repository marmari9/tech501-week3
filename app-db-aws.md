# Two Tier Architecture on AWS:

## 1. **Launch MongoDB EC2 Instance**
   - Log into AWS Console and launch a new EC2 instance with an appropriate AMI (Ubuntu 22.04 SSD standard).
   - Ensure proper security group settings to allow inbound traffic on SSH (port 22)
   - Create a key pair and use the .pem file to access your instance.
  ```bash
  ssh -i "/c/Users/Maram/Downloads/tech501-maram-key-2.pem" ubuntu@34.245.14.9
```


## install mongodb:
- Mongo db version 7.0.6.
- Install gnupg and curl:
    - sudo apt-get install gnupg curl
- Import GPG key:
    - curl -fsSL https://www.mongodb.org/static/pgp/server-7.0.asc | \
   sudo gpg -o /usr/share/keyrings/mongodb-server-7.0.gpg \
   --dearmor

- Create the list file:
    - echo "deb [ arch=amd64,arm64 signed-by=/usr/share/keyrings/mongodb-server-7.0.gpg ] https://repo.mongodb.org/apt/ubuntu jammy/mongodb-org/7.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-7.0.list
- Reload the package database:
    - sudo apt-get update
- Install MongoDB Community Server.
  - sudo apt-get install -y mongodb-org=7.0.6 mongodb-org-database=7.0.6 mongodb-org-server=7.0.6 mongodb-mongosh mongodb-org-mongos=7.0.6 mongodb-org-tools=7.0.6
- Status of Mongo db:
  - sudo systemctl status mongod
- Start MongoDB:
  - sudo systemctl start mongod 
- Configure the bindip:
  - sudo nano /etc/mongod.conf
  - change the bindIP to: 0.0.0.0 (only for testing purposes which accepts connection from any IP address). 
  - ctrl + s followed by ctrl + x
- Restart the mongod db service:
  - sudo systemctl restart mongod
  - sudo systemctl status mongod
  - Node:
 
sudo DEBIAN_FRONTEND=noninteractive bash -c "curl -fsSL https://deb.nodesource.com/setup_20.x | bash -"

sudo DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs

sudo apt-get install npm

## 3. **Configure MongoDB for Remote Access**
   - Edit MongoDB configuration (`/etc/mongod.conf`):
     ```bash
     sudo nano /etc/mongod.conf
     ```
   - Bind MongoDB to listen on all IP addresses (or specific IP):
     ```bash
     bindIp: 0.0.0.0
     ```
   - Restart MongoDB:
     ```bash
     sudo systemctl restart mongodb
     ```

# Creating the app vm:
   - Log into AWS Console and launch a new EC2 instance with an appropriate AMI (Ubuntu 22.04 SSD standard).
   - Ensure proper security group settings to allow inbound traffic on SSH (port 22) and HTTP(80)
   - Create a key pair and use the .pem file to access your instance.


- 1. Update and Upgrade Packages
- sudo apt-get upgrade -y
- sudo apt-get update -y 

- 2. Install Nginx and Node.js

```bash
- sudo apt install nginx -y
```

- check if it's running : 
```bash
sudo systemctl status nginx or check public IP of vm
``` 
- For Ngnix to work we need to install node js and npm as it is a dependency.

```bash
- sudo DEBIAN_FRONTEND=noninteractive bash -c "curl -fsSL https://deb.nodesource.com/setup_20.x | bash -" && \
sudo DEBIAN_FRONTEND=noninteractive apt-get install -y nodejs
```

- 3.Verify Installation of node js and npm:
- node -v
- npm -v

- 4. Check status of Nginx:
sudo systemctl status nginx

- 5.Copy the App Folder into my app instance:
- git clone https://github.com/marmari/tech501-sparta-app.git
- cd tech501-sparta-app


# when in app folder: 
- cd app
- npm install
- to run the app two ways: 
    1- node app.js
    2- npm start 
- http://public IP:3000 (app should appear). ps; allow port 3000 in NSG on app vm.
 

 # on the app vm:

- environmanetal variable. use this command:
    - export DB_HOST=mongodb://<private IP>:27017/posts
    - export DB_HOST=mongodb://10.0.3.4:27017/posts
then printenv DB_HOST to check if its correct. 
- npm install
- node app.js
- the IP address: http://PublicIP:3000/posts


## Creating a reverse proxy:
- on the app VM:
1- Create a backup file of the configuration file
2- Edit Nginx Configuration:
    - sudo nano /etc/nginx/sites-available/default
3- change the location and add this:
    - On Location just remove the line and add this: proxy_pass http://localhost:3000;
    - this would allow access on port 80. it was only allowed on port 3000.
    - Ctrl + S; Ctrl + x
4- Test the Configuration:
    - sudo nginx -t
5- restart the nginx:
    - sudo systemctl restart nginx
6- Start the app:
    - npm start
7- Verify:
    - http://<your-public-ip>, http://172.187.176.32


