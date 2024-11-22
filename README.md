# Three-Tier Expense Management Application
## 🚀 Project Overview
The **Three-Tier Expense Management Application** is designed to help users track and manage their expenses effectively. The application follows a **three-tier architecture** consisting of:

**1) Frontend**: A user-friendly web interface served via Nginx.

**2) Backend**: A Node.js application handling business logic, API requests, and interacting with the database.

**3) Database**: A MySQL database used for storing user expenses and other related data.

This README will guide you through the steps to deploy the **Three-Tier Expense Management Application** on an AWS EC2 instance running Ubuntu.

## 🛠 Prerequisites
Before starting, make sure you have the following:

- An AWS EC2 instance running Ubuntu.
- Security groups configured to allow:
    - HTTP (port 80) for web traffic.
    - MySQL (port 3306) for database access.
- Root access (or sudo privileges) on the EC2 instance.

## 📦 Project Components

**1) Frontend: A simple, responsive user interface served through Nginx.**

**2) Backend: A Node.js API that handles business logic and connects to the MySQL database.**

**3) Database: MySQL for storing user data and expense information.**

## ⚙️ Setup Instructions

**Step 1: Set Up Frontend with Nginx**

*Update the system and install Nginx:*


```
sudo apt update -y
sudo apt install nginx -y
```

*Start and enable Nginx to run on boot:*

```
sudo systemctl enable nginx
sudo systemctl start nginx
```

*Check Nginx:*

Open your browser and visit http://EC2-PUBLIC-IP/ to verify that Nginx is working.

*Remove default content:*

```
sudo rm -rf /var/www/html/*
```


*Clone the frontend repository:*

```bash
sudo apt install git -y  # Install Git if not already installed
sudo git clone https://github.com/chiragpuniyanii/Expense-frontend.git /var/www/html
```

Verify frontend by visiting http://EC2-PUBLIC-IP/.

*Configure Nginx reverse proxy for the backend:*

Edit the Nginx config file:

```bash

sudo vim /etc/nginx/sites-available/expense.conf
```
Add the following content:


```nginx
server {
    listen 80;
    server_name <Instance-IP>;

    root /var/www/html/expense-frontend;
    index index.html;

    location /api/ {
        proxy_pass http://127.0.0.1:8080/;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    location / {
        try_files $uri $uri/ =404;
    }
}              
```
Replace <Instance-IP> with the backend server IP address.

*Restart Nginx:*

```bash
sudo systemctl restart nginx
```



## Step 2: Install MySQL
*Install MySQL Server:*

```bash
sudo apt install mysql-server -y
```

*Start MySQL and enable it to start at boot:*

```bash

sudo systemctl enable mysql
sudo systemctl start mysql
```

*Setting a root password:*

```bash
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY 'ExpenseApp@1';
FLUSH PRIVILEGES;
```

Set the root password to ExpenseApp@1 (or your preferred password).


## Step 3: Set Up Backend (Node.js)

*Install Node.js (version 20):*

```bash
curl -sL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```
*Create a user for the application:*

```bash
sudo useradd expense
```

*Create a directory for the backend application:*

```bash
sudo mkdir /app
sudo chown expense:expense /app
```

*Clone the repository into the /app directory:*

```bash
cd /app
sudo git clone https://github.com/chiragpuniyanii/expense-backend.git
```

*Navigate to the cloned backend directory:*

```bash
cd /app/expense-backend
```

*Install required dependencies:*

```bash
sudo npm install

*Create a Systemd service for the backend:*

```bash
sudo vim /etc/systemd/system/backend.service
```

Add the following content:

```ini

[Unit]
Description=Backend Service

[Service]
User=expense
Environment=DB_HOST="localhost"
ExecStart=/usr/bin/node /app/index.js
SyslogIdentifier=backend

[Install]
WantedBy=multi-user.target
                                
```

*Reload Systemd, enable, and start the backend service:*

```bash
sudo systemctl daemon-reload
sudo systemctl enable backend
sudo systemctl start backend
```


## Step 4: Load Database Schema
*Install MySQL client (if not already installed):*

```bash
sudo apt install mysql-client -y
```

*Load the schema into MySQL:*

```bash
mysql -h localhost -u root -pExpenseApp@1 < /app/schema/backend.sql
```

## 📝 Recap
- Frontend is served via Nginx.
- Backend is a Node.js service that interacts with the MySQL database.
- MySQL is used for storing user data and expenses.

## 🔄 Troubleshooting
*Nginx Reverse Proxy Not Working: Ensure the backend service is running and accessible by checking Nginx logs:*

```bash
sudo journalctl -u nginx
```

*MySQL Connection Issues: Check the connection details in the backend service (/etc/systemd/system/backend.service) and ensure MySQL is running.*

*Backend Not Starting: Review backend service logs to check for errors:*

```bash
sudo journalctl -u backend
```

## 💡 Additional Notes

- Always make sure that MySQL, Node.js, and Nginx are properly configured.

- Use secure passwords for your database and application.

- Regularly check application logs for any errors that may arise during runtime.

## 🎉 Conclusion
This README provides detailed instructions for setting up and deploying the Three-Tier Expense Management Application on an AWS EC2 Ubuntu instance. By following these steps, you will successfully deploy the frontend, backend, and MySQL database for the application.

This version reflects your repository name Three-Tier-Expense-Management-Application. Let me know if you'd like to make any further adjustments!


