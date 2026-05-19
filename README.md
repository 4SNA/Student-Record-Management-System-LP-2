# Assignment 10 - Cloud-Based Student Record Management System

## Problem Statement

Deploy a web application that stores and manages student records using a database and backend API. Deploy the frontend interface and ensure users can perform operations such as adding, updating, and retrieving student data.

---

# Repository

```text
https://github.com/4SNA/Student-Record-Management-System-LP-2
```

---

# Technologies Used

- AWS EC2 Ubuntu Instance
- React 19 + Vite Frontend
- Node.js + Express.js Backend
- MongoDB Atlas Database
- Mongoose
- Git
- npm
- PM2 Process Manager

---

# Features

- Add Student Records
- View Student Records
- Update Student Records
- Delete Student Records
- Search and Filter Students
- Dashboard Statistics
- Responsive UI
- Cloud Deployment

---

# Architecture

```text
User Browser
     |
     v
AWS EC2 Instance
     |
     |-- React + Vite Frontend (Port 5173)
     |-- Express Backend API (Port 5000)
     |
     v
MongoDB Atlas Database
```

---

# Step 1 - Create AWS EC2 Instance

1. Open AWS Console.
2. Go to EC2.
3. Click Launch Instance.
4. Select Ubuntu Server 22.04.
5. Select t2.micro.
6. Create/select key pair.
7. Download `.pem` file.
8. Configure Security Group.

---

# Security Group Inbound Rules

| Type | Port | Source |
|---|---|---|
| SSH | 22 | My IP |
| HTTP | 80 | 0.0.0.0/0 |
| Custom TCP | 5000 | 0.0.0.0/0 |
| Custom TCP | 5173 | 0.0.0.0/0 |

---

# Step 2 - Connect to EC2

Open terminal or PowerShell where `.pem` exists.

```bash
chmod 400 your-key.pem
```

```bash
ssh -i your-key.pem ubuntu@YOUR_PUBLIC_IP
```

Example:

```bash
ssh -i student.pem ubuntu@15.206.xxx.xxx
```

---

# Step 3 - Update Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

---

# Step 4 - Install Required Software

Install Node.js, npm and Git:

```bash
sudo apt install nodejs npm git -y
```

Verify:

```bash
node -v
npm -v
git --version
```

Install PM2:

```bash
sudo npm install -g pm2
```

Verify:

```bash
pm2 --version
```

---

# Step 5 - MongoDB Atlas Setup

Use MongoDB Atlas cloud database instead of local MongoDB.

---

# MongoDB Atlas Details

```text
Username: lp2_user
Password: lp2password123
Database Name: student_record
Cluster Name: Cluster0
```

---

# Atlas Setup Steps

1. Open MongoDB Atlas.
2. Create free M0 cluster.
3. Create database user:
   ```text
   lp2_user
   ```
4. Set password:
   ```text
   lp2password123
   ```
5. Go to:
   ```text
   Network Access
   ```
6. Click:
   ```text
   Add IP Address
   ```
7. Click:
   ```text
   Allow Access From Anywhere
   ```
8. Confirm:
   ```text
   0.0.0.0/0
   ```

MongoDB Atlas automatically creates database `student_record` after first insertion.

---

# Step 6 - Clone Repository

```bash
git clone https://github.com/4SNA/Student-Record-Management-System-LP-2.git
```

```bash
cd Student-Record-Management-System-LP-2
```

Check files:

```bash
ls
```

Expected:

```text
backend
frontend
README.md
```

---

# Step 7 - Backend Setup

Go to backend:

```bash
cd backend
```

Install backend dependencies:

```bash
npm install
```

Create `.env`:

```bash
nano .env
```

Paste:

```env
NODE_ENV=production
PORT=5000
MONGO_URI=
JWT_SECRET=sarthak123
```

Save:

```text
CTRL + X → Y → Enter
```

Check `.env`:

```bash
cat .env
```

---

# Step 8 - Frontend Setup

Go to frontend:

```bash
cd ../frontend
```

Install frontend dependencies:

```bash
npm install
```

---

# Step 8.1 - Fix localhost API Issue

Search localhost references:

```bash
grep -R "localhost" .
```

If you find:

```text
http://localhost:5000
```

replace with your EC2 public IP.

Example:

```text
http://15.206.xxx.xxx:5000
```

Quick replace:

```bash
grep -rl "localhost:5000" . | xargs sed -i 's|http://localhost:5000|http://15.206.xxx.xxx:5000|g'
```

---

# Step 8.2 - Configure Vite Public Hosting

Open Vite config:

```bash
nano vite.config.js
```

OR

```bash
nano vite.config.ts
```

Ensure:

```js
server: {
  host: "0.0.0.0",
  port: 5173
}
```

Example:

```js
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

export default defineConfig({
  plugins: [react()],
  server: {
    host: "0.0.0.0",
    port: 5173
  }
})
```

---

# Step 8.3 - Run Frontend

```bash
npm run dev -- --host 0.0.0.0
```

Expected:

```text
Local:   http://localhost:5173
Network: http://172.xxx.xxx.xxx:5173
```

---

# Step 9 - Run Backend Using PM2

Open new SSH terminal or reconnect.

Go to backend:

```bash
cd ~/Student-Record-Management-System-LP-2/backend
```

Check files:

```bash
ls
```

If `server.js` exists:

```bash
pm2 start server.js --name student-records
```

Save PM2 process:

```bash
pm2 save
```

Check status:

```bash
pm2 list
```

---

# Step 10 - Test Backend Locally

```bash
curl http://localhost:5000
```

Expected:

```json
{
  "message": "API is running. Frontend is on port 5173."
}
```

---

# Step 11 - Open Application Publicly

## Backend API

```text
http://YOUR_PUBLIC_IP:5000
```

---

## Frontend UI

```text
http://YOUR_PUBLIC_IP:5173
```

Example:

```text
http://15.206.xxx.xxx:5173
```

---

# Step 12 - Verify MongoDB Atlas

1. Open MongoDB Atlas.
2. Go to Database.
3. Click Browse Collections.
4. Add one student record.
5. Database `student_record` should appear.
6. Collections/documents should be visible.

---

# API Endpoints

| Method | Endpoint | Description |
|---|---|---|
| GET | /api/students | Get all students |
| GET | /api/students/:id | Get single student |
| POST | /api/students | Add student |
| PUT | /api/students/:id | Update student |
| DELETE | /api/students/:id | Delete student |
| GET | /api/students/stats | Get statistics |

---

# Useful Commands

## Check PM2

```bash
pm2 list
```

---

## View Logs

```bash
pm2 logs
```

---

## Restart Backend

```bash
pm2 restart all
```

---

## Stop Backend

```bash
pm2 stop all
```

---

## Delete PM2 Processes

```bash
pm2 delete all
```

---

## Test Backend

```bash
curl http://localhost:5000
```

---

# Common Errors and Fixes

---

## 1. pm2 command not found

```bash
sudo npm install -g pm2
```

---

## 2. Website not opening publicly

Check Security Group.

Required:

| Type | Port |
|---|---|
| Custom TCP | 5000 |
| Custom TCP | 5173 |

Source:

```text
0.0.0.0/0
```

---

## 3. MongoDB Atlas connection failed

Check:

```text
Network Access → 0.0.0.0/0
```

Check `.env`:

```env
MONGO_URI=mongodb+srv://lp2_user:lp2password123@cluster0.tki1kaj.mongodb.net/student_record?retryWrites=true&w=majority&appName=Cluster0
```

Restart:

```bash
pm2 restart all
pm2 logs
```

---

## 4. Frontend works only on localhost

Fix Vite config:

```js
server: {
  host: "0.0.0.0",
  port: 5173
}
```

Run:

```bash
npm run dev -- --host 0.0.0.0
```

---

## 5. localhost API issue

Replace:

```text
http://localhost:5000
```

with:

```text
http://YOUR_PUBLIC_IP:5000
```

---

## 6. EADDRINUSE port 5000 already in use

Fix:

```bash
pm2 delete all
pm2 start server.js
```

---

## 7. Cannot find module

Run:

```bash
npm install
```

inside frontend/backend.

---

## 8. server.js not found

Check:

```bash
ls
```

If app.js exists:

```bash
pm2 start app.js
```

If index.js exists:

```bash
pm2 start index.js
```

---

# Viva Questions

## What is EC2?

AWS virtual machine service.

---

## What is MongoDB Atlas?

Cloud-based MongoDB database service.

---

## What is PM2?

Node.js process manager.

---

## Why ports 5000 and 5173?

- 5000 → Backend API
- 5173 → React + Vite frontend

---

## What is Security Group?

AWS firewall controlling traffic.

---

## Why `.env` file?

Stores environment variables.

---

# Output

The Student Record Management System is successfully deployed and publicly accessible using:

```text
http://YOUR_PUBLIC_IP:5173
```

---

# Conclusion

The Student Record Management System was successfully deployed on AWS EC2 Ubuntu using React + Vite frontend, Node.js/Express backend, and MongoDB Atlas database. The application supports CRUD operations and is publicly accessible through EC2 public IP.
