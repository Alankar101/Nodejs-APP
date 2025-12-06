# Deploy Node.js App on AWS (Using Nginx Reverse Proxy)

This guide explains how to deploy a simple Node.js application on an AWS EC2 Ubuntu instance using **Nginx as a reverse proxy**.

---
## 🧾 Application Files

### `index.js`
```js
var express = require('express');
var app = express();

app.get('/', function (req, res) {
  res.send('successfully deployed nodeapp............');
});

app.listen(process.env.PORT || 3000);
module.exports = app;
```

### `package.json`
```json
{
  "name": "node-app",
  "description": "hello jenkins test app",
  "version": "0.0.1",
  "private": true,
  "dependencies": {
    "express": "3.12.0"
  }
}
```

---
## 🚀 Deployment Steps

### 1️⃣ Launch EC2 Ubuntu Instance
- Type: Ubuntu 22.04
- Security group inbound allow: **HTTP (80), SSH (22)**

### 2️⃣ Install Node.js & npm
```bash
sudo apt update
sudo apt install -y nodejs npm
```

### 3️⃣ Upload Your App to Server
```bash
git clone <repo-url>   # or upload manually
cd node-app
npm install
```

### 4️⃣ Run the App (Test Before Proxy)
```bash
node index.js
```
Check in browser:
```
http://YOUR_PUBLIC_IP:3000
```
You should see:
```
successfully deployed nodeapp............
```

Stop it (Ctrl + C) before next step.

---
## 🌐 Configure Nginx Reverse Proxy

### Install Nginx
```bash
sudo apt install -y nginx
```

### Configure Proxy
```bash
sudo nano /etc/nginx/sites-available/default
```
Replace the `location /` block with:
```nginx
location / {
    proxy_pass http://localhost:3000;
    proxy_http_version 1.1;
    proxy_set_header Upgrade $http_upgrade;
    proxy_set_header Connection 'upgrade';
    proxy_set_header Host $host;
    proxy_cache_bypass $http_upgrade;
}
```

### Restart Nginx
```bash
sudo systemctl restart nginx
```

---
## ▶️ Run Node App in Background (Recommended)
```bash
sudo npm install -g pm2
pm2 start index.js
pm2 startup
pm2 save
```

---
## 🏁 Final Check
Open browser:
```
http://YOUR_PUBLIC_IP
```
You should get:
```
successfully deployed nodeapp............
```

---
## 📌 Useful Commands
| Task | Command |
|------|---------|
| Restart Nginx | `sudo systemctl restart nginx` |
| Check Nginx status | `sudo systemctl status nginx` |
| Stop app | `pm2 stop index` |
| Logs | `pm2 logs` |

---
## 🎉 Deployment Successful!
Your Node.js app is now deployed on AWS using Nginx reverse proxy.
