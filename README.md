# Employee Management Fullstack App - AWS Deployment Guide

A comprehensive guide to deploying a full-stack Employee Management application on AWS with Spring Boot backend, React frontend, MySQL (RDS), and MongoDB Atlas.



## 🔷 PHASE 1: AWS Infrastructure Setup

### Step 1: Create Backend EC2 Instance (Spring Boot)

**EC2 Configuration:**

**Connect to EC2:**

```bash
ssh -i your-key.pem ubuntu@<BACKEND_EC2_PUBLIC_IP>
```

---

### Step 2: Install Backend Requirements

```bash
# Update package list
sudo apt update
```
```bash
# Install Java 11
sudo apt install openjdk-11-jdk -y
```
```bash
# Install Maven
sudo apt install maven -y
```
```bash
# Verify installation
java -version
mvn -version
```
```bash
# Install MySQL client
sudo apt install mysql-client -y
```

---

## 🔷 PHASE 2: Database Setup (Managed Services)

### Step 3: Create MySQL Database (AWS RDS)

**RDS Configuration:**

| Setting | Value |
|---------|-------|
| Engine | MySQL |
| DB Name | employee_management |
| Username | admin |
| Public Access | Yes |

**Security Group Rules:**

- Allow port `3306` from Backend EC2 Security Group

**Test Connection from EC2:**

```bash
# Install MySQL client
sudo apt install mysql-client -y
```
```bash
# Connect to RDS
mysql -h <RDS_ENDPOINT> -u admin -p
```
```bash
# Create database
CREATE DATABASE employee_management;
```

---

### Step 4: Create MongoDB Database (MongoDB Atlas)

#### MongoDB Atlas Setup Steps

**Step 1: Create MongoDB Atlas Account**

1. Visit: [https://www.mongodb.com/atlas](https://www.mongodb.com/atlas)
2. Click **Try Free**
3. Sign up using Google, GitHub, or Email

**Step 2: Create a Free Cluster**

1. Choose **Shared** → **Free (M0)**
2. Cloud Provider: **AWS**
3. Region: Closest to your location
4. Click **Create**
5. ⏳ Wait 1–3 minutes for cluster creation

**Step 3: Create Database User**

1. Go to **Security** → **Database Access**
2. Click **Add New Database User**
3. Set privilege: **Read and write to any database**
4. Save username and password

**Step 4: Allow Network Access**

1. Go to **Security** → **Network Access**
2. Click **Add IP Address**
3. Select **Allow Access from Anywhere** → `0.0.0.0/0`
4. ⚠️ **Note:** For learning only. Restrict in production.

**Step 5: Get Connection String**

1. Go to **Database** → **Connect** → **Drivers**
2. Choose **Java**
3. Copy the connection string:

```
mongodb+srv://<username>:<password>@cluster0.xxxxx.mongodb.net/
```

> **Note:** MongoDB database is created automatically when data is inserted.

---

## 🔷 PHASE 3: Backend Deployment (Spring Boot)

### Step 5: Clone Backend Code

```bash
git clone https://github.com/hoangsonww/Employee-Management-Fullstack-App.git
```
```bash
cd Employee-Management-Fullstack-App/backend
```

---

### Step 6: Create config.properties(/backend/config.properties) (Secure Configuration)

```bash
nano config.properties
```

Add the following content:

```properties
# MySQL Configuration
MYSQL_HOST=employee-management.czym2myqmzzs.eu-north-1.rds.amazonaws.com
MYSQL_PORT=3306
MYSQL_DB=employee_management
MYSQL_USER=admin
MYSQL_PASSWORD=admin123
MYSQL_SSL_MODE=REQUIRED

# MongoDB Configuration
MONGO_URI=mongodb+srv://<username>:<password>@cluster0.mongodb.net/employee_management
```

> Replace `<username>` and `<password>` or Copy the connection string with your MongoDB Atlas credentials.


---

### Step 7: Build & Run Backend

```bash
# Build the application
mvn clean install -DskipTests
```
```bash

# Run the application
mvn spring-boot:run
```

**Verify Backend:**

Open in browser:
```
http://<BACKEND_EC2_IP>:8080/swagger-ui.html
```

---

## 🔷 PHASE 4: Frontend Deployment (React + Nginx)

### Step 8: Create Frontend EC2 Instance

**EC2 Configuration:**

**Security Group Rules:**

| Type | Protocol | Port | Source |
|------|----------|------|--------|
| SSH | TCP | 22 | My IP |
| HTTP | TCP | 80 | 0.0.0.0/0 |

---

### Step 9: Install Nginx

```bash
sudo apt update
```
```bash
sudo apt install nginx -y
```

---

### Step 10: Clone Frontend Code

```bash
git clone https://github.com/hoangsonww/Employee-Management-Fullstack-App.git
```
```bash
cd Employee-Management-Fullstack-App/frontend
```

---

### Step 11: Configure Frontend Environment

Create `.env` file:

```bash
nano .env
```

Add:

```env
REACT_APP_API_URL=http://<BACKEND_EC2_IP>:8080/api
```

> ⚠️ **Important:** React reads environment variables at build time.

---

### Step 12: Install & Build Frontend

```bash
# Install Node.js and npm
sudo apt install npm -y
```
```bash

# Verify installation
node -v
npm -v
```
```bash
# Install dependencies
npm install
```
```bash
# Build the application
npm run build
```

Build output will be in `frontend/build` directory.

---

### Step 13: Deploy Frontend with Nginx

**Copy build files to Nginx directory:**

```bash
sudo rm -rf /var/www/html/*
```
```bash
sudo cp -r build/* /var/www/html/
```

**Configure Nginx(CONFIGURE NGINX FOR REACT ROUTING):**
```bash
sudo rm -f /etc/nginx/sites-available/default
```

```bash
sudo nano /etc/nginx/sites-available/default
```

Replace content with:

```nginx
server {
    listen 80;
    server_name _;
    root /var/www/html;
    index index.html;

    location / {
        try_files $uri /index.html;
    }
}
```

**Restart Nginx:**

```bash
sudo systemctl restart nginx
```

---

## 🔷 PHASE 5: Application Usage

### Step 14: Access Application

**Frontend:**
```
http://<FRONTEND_EC2_IP>
```
## 🔐 Login / Register (IMPORTANT)

- ❌ No default username or password  
- ✅ Users must **REGISTER first**  
- ✅ After registration, users can **LOGIN**  
- 📦 User credentials are securely stored in the database  


**Backend API:**
```
http://<BACKEND_EC2_IP>:8080/api
```

**Swagger UI:**
```
http://<BACKEND_EC2_IP>:8080/swagger-ui.html
```

---


