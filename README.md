
# 📘 JAVA-SPRINGBOOT-PROJECT Deployment Guide

This project demonstrates deploying a **Java Spring Boot backend** with a **Streamlit frontend** on AWS using **RDS (MySQL)** and **EC2 instances**.

---

## 🎨 Architecture Overview
```text
[Web Browser]
     ↓
[Frontend EC2 Instance]
     ↓
[Backend EC2 Instance (Spring Boot)]
     ↓
[ROS Layer: Single A2 Deployment]
     ↓
[MySQL Database]
```

<img width="1061" height="240" alt="Untitled Diagram drawio (7)" src="https://github.com/user-attachments/assets/dfd27271-7b48-45c9-9073-38dc50f0a535" />


---


## 📁 File Structure
 ```
java-springboot-project/
.
├── Backend-creation-process
├── Dockerfile
├── Dockerfile1
├── Dockerfile2
├── Frontend
│   ├── app.py
│   └── creation-process
├── Jenkinsfile
├── Jenkinsfile-2
├── README.md
├── compose
│   ├── docker-compose-one.yaml
│   └── docker-compose.yaml
├── helper
├── logs
│   └── datastore.log
├── mvnw
├── mvnw.cmd
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── com
│   │   │       └── datastore
│   │   │           └── person
│   │   │               ├── DataStoreApplication.java
│   │   │               ├── controller
│   │   │               │   └── StudentController.java
│   │   │               ├── pojo
│   │   │               │   └── Student.java
│   │   │               └── repository
│   │   │                   └── StudentRepository.java
│   │   └── resources
│   │       ├── application-build.properties
│   │       └── application.properties
│   └── test
│       └── java
│           └── com
│               └── datastore
│                   └── person
│                       └── DataStoreApplicationTests.java
└── target
    ├── classes
    │   ├── application-build.properties
    │   ├── application.properties
    │   └── com
    │       └── datastore
    │           └── person
    │               ├── DataStoreApplication.class
    │               ├── controller
    │               │   └── StudentController.class
    │               ├── pojo
    │               │   └── Student.class
    │               └── repository
    │                   └── StudentRepository.class
    ├── datastore-0.0.7.jar
    ├── datastore-0.0.7.jar.original
    ├── maven-archiver
    │   └── pom.properties
    ├── maven-status 
    │   └── maven-compiler-plugin
    │       ├── compile
    │       │   └── default-compile
    │       │       ├── createdFiles.lst
    │       │       └── inputFiles.lst
    │       └── testCompile
    │           └── default-testCompile
    │               ├── createdFiles.lst
    │               └── inputFiles.lst
    ├── surefire-reports
    │   ├── 2025-11-10T20-43-53_965.dumpstream
    │   ├── TEST-com.datastore.person.DataStoreApplicationTests.xml
    │   └── com.datastore.person.DataStoreApplicationTests.txt
    └── test-classes
        └── com
            └── datastore 
                └── person
                    └── DataStoreApplicationTests.class

38 directories, 40 files
 ```

---



## 📌 Architecture Overview
- **AWS RDS (MySQL)** → Database
- **EC2 Backend (Spring Boot)** → REST API service
- **EC2 Frontend (Streamlit)** → Web UI consuming backend API

---

## ⚙️ Step 1: Create RDS Database
1. Go to **AWS RDS** → Create Database
   - **Creation method**: Full configuration  
   - **Engine**: MySQL  
   - **Version**: MySQL 8.0.43  
   - **Deployment**: Single-AZ DB instance  
   - **DB Identifier**: `database-1`  
   - **Master Username**: `admin`  
   - **Master Password**: `chandan#1234`  
   - **Public Access**: Yes  

2. Click **Create Database**.
   
<img width="1349" height="124" alt="Screenshot 2025-11-27 134417" src="https://github.com/user-attachments/assets/671b0be9-dea5-45fb-b4ae-8e08bd62a87f" />
<img width="1363" height="295" alt="Screenshot 2025-11-27 134540" src="https://github.com/user-attachments/assets/b19c07a8-6c6a-4e4f-ad17-8c996cfc72cb" />
<img width="1378" height="628" alt="Screenshot 2025-11-27 134825" src="https://github.com/user-attachments/assets/ce5cbf6a-a055-4324-b4d6-5725aa694a70" />
<img width="1338" height="117" alt="Screenshot 2025-11-27 135534" src="https://github.com/user-attachments/assets/974184fc-e8e4-4bc1-917b-1509bfbcbc0d" />
<img width="1355" height="572" alt="Screenshot 2025-11-27 135631" src="https://github.com/user-attachments/assets/fd33bf9b-8f0e-4b88-abf9-dbbc160857c5" />

---

## ⚙️ Step 2: Launch EC2 Instances
Launch **two EC2 instances**:
###  Backend : java code run
### Frontend : python code run
---
### Backend Instance
- Name: `Backend`  
- Type: `t3.micro`  
- Networking: Default  
- Security Group: Default (All traffic `0.0.0.0/0`)  
- Keypair: Not required  

### Frontend Instance
- Name: `Frontend`  
- Type: `t3.micro`  
- Networking: Default  
- Security Group: Default (All traffic `0.0.0.0/0`)  
- Keypair: Not required  

---

## ⚙️ Step 3: Setup Backend (Spring Boot)

---



## 1️⃣ Connect to **Backend EC2**:
```bash
sudo su -
```
- Switch to the root user for full permissions.  
- ⚠️ **Note:** Always ensure you’re on the correct EC2 instance (backend server).

---

## 2️⃣ Install Git
```bash
yum install git -y
```
- Installs Git for cloning repositories.  
- `-y` auto-confirms installation.

---

## 3️⃣ Clone the Project Repository
```bash
git clone https://github.com/chintu-cloud/JAVA-SPRINGBOOT-PROJECT.git
```
- Clones the Spring Boot project into your EC2 instance.  
- ⚠️ **Reminder:** Verify repo URL is correct and accessible.

---

## 4️⃣ Install Maven
```bash
yum install maven -y
```
- Installs Apache Maven for building the project.  
- ⚠️ **Tip:** Run `mvn -v` to confirm installation.

---

## 5️⃣ Navigate into Project Directory
```bash
ls
cd Java-springboot-project
ls
```
- `ls` helps confirm the folder exists before entering.  
- ⚠️ **Pitfall:** Directory name is case-sensitive (`Java-springboot-project`).

---

## 6️⃣ Build the Project
```bash
mvn clean package -Dspring.profiles.active=build
```
- Cleans old builds and packages the project using the `build` profile.  
- ⚠️ **Note:** Ensure `pom.xml` has the correct profile defined.

---

## 7️⃣ Navigate to Target Directory
```bash
ls
cd target
ls
```
- The compiled `.jar` file will be inside `target/`.

---

## 8️⃣ Move JAR File to Root Directory
```bash
mv datastore-0.0.7.jar /root
```
- Moves the packaged JAR to `/root` for easy access.  
- ⚠️ **Reminder:** Replace `datastore-0.0.7.jar` with the actual filename if different.  
- Use `ls` to confirm the file exists before moving.

---

## 9️⃣ Return to Project Root
```bash
cd ..
cd ..
ls
```

----

<img width="1230" height="209" alt="Screenshot 2025-11-27 141511" src="https://github.com/user-attachments/assets/ac29c4ab-4a25-4bcf-8734-25506740173f" />

----

- 

2. Run Spring Boot JAR with MySQL connection:

 ## run into root/ dictory inside
   ```bash
   MYSQL_HOST=jdbc:mysql://database-1.c6ricqgseec0.us-east-1.rds.amazonaws.com:3306/datastore?createDatabaseIfNotExist=true \
   MYSQL_USERNAME=admin \
   MYSQL_PASSWORD=chandan#1234 \
   nohup java -jar ./datastore-0.0.7.jar > /var/log/app/nohup.out 2>&1 &
   ```
<img width="1882" height="160" alt="Screenshot 2025-11-27 142413" src="https://github.com/user-attachments/assets/7f7683db-1541-4022-b762-2a90cbb58769" />


3. Verify:
   ```bash
   ps aux | grep jar
   cat /var/log/app/nohup.out
   ```
<img width="1889" height="675" alt="Screenshot 2025-11-27 145936" src="https://github.com/user-attachments/assets/3dec9b1c-cc53-4b17-8243-acb05ff11b45" />

---


---

# ⚙️ Step 4: Setup Frontend (Streamlit)

## 1️⃣ Connect to **Frontend EC2** and install dependencies

```bash
sudo su -
```
- Switch to root user for full permissions.  
- ⚠️ **Reminder:** Ensure you’re on the correct EC2 instance (frontend server).

---

##  Install Git
```bash
yum install git -y
```
- Installs Git for cloning repositories.  
- `-y` auto-confirms installation.

---

##  Clone the Project Repository
```bash
git clone https://github.com/chintu-cloud/JAVA-SPRINGBOOT-PROJECT.git
```
- Clones the Spring Boot + Streamlit project into your EC2 instance.  
- ⚠️ **Tip:** Double-check repo URL accessibility.

---

##  Verify Project Directory
```bash
ls
cd Java-springboot-project
ls
```
- Confirms the project folder exists before entering.  
- ⚠️ **Pitfall:** Directory name is case-sensitive (`Java-springboot-project`).

---

##  Install Python & Pip
```bash
yum install python3-pip -y
```
- Installs Python 3 and pip package manager.  
- ⚠️ **Note:** Run `python3 --version` and `pip3 --version` to confirm installation.

---

##  Create Virtual Environment
```bash
python3 -m venv venv
source venv/bin/activate
```
- Creates and activates a Python virtual environment named `venv`.  
- ⚠️ **Reminder:** Always activate the environment before installing dependencies.

---

##  Upgrade Pip
```bash
pip install --upgrade pip
```
- Ensures pip is updated to the latest version.  
- ⚠️ **Tip:** Avoid dependency issues by keeping pip current.

---

##  Install Required Python Packages
```bash
pip install streamlit requests
```


## 2️⃣ Create **systemd service** for Streamlit

Open service file:
```bash
     vi /etc/systemd/system/frontend.service
```

Paste the following configuration:

```ini
[Unit]
Description=Streamlit Frontend App
After=network.target

[Service]
User=root
WorkingDirectory=/root/Java-springboot-project/frontend
ExecStart=/root/Java-springboot-project/venv/bin/python -m streamlit run /root/Java-springboot-project/frontend/app.py --server.port=8501 --server.address=0.0.0.0
Environment=API_URL=http://<BACKEND_PRIVATE_IP>:8084
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
```

### 🔑 Key Notes:
- **WorkingDirectory** → points to your frontend folder.  
- **ExecStart** → must use the Python binary inside your virtual environment.  
- **Environment** → replace `<BACKEND_PRIVATE_IP>` with the **private IP of your backend EC2**.  
- **Restart=always** → ensures the service auto‑restarts if it crashes.  

---

## 3️⃣ Reload and start service
```bash
# Reload systemd to recognize new service
systemctl daemon-reload

# Enable service to start on boot
systemctl enable frontend

# Start service immediately
systemctl start frontend

# Check service status
systemctl status frontend
```

---

## ✅ Verification
- Open browser → `http://<Frontend_Public_IP>:8501`  
- You should see the **Streamlit frontend** running and connected to backend.  
- If service fails, check logs:
```bash
journalctl -u frontend -f
```

---


<img width="1374" height="259" alt="Screenshot 2025-11-27 151207" src="https://github.com/user-attachments/assets/0cb5dc6a-689e-4d03-b04e-66b7b40e14f7" />

---

## ⚙️ Step 5: Access Application
- Copy **Frontend EC2 Public IP** and open in browser:
  ```
  http://<Frontend_Public_IP>:8501
  ```

Example:
```
http://3.235.170.36:8501
```

---

## ✅ Output
You should now see the **Streamlit frontend** running and connected to the **Spring Boot backend** with MySQL database.
<img width="1901" height="809" alt="Screenshot 2025-11-27 150736" src="https://github.com/user-attachments/assets/3905a144-1bd5-4a7a-836d-618578eee41e" />
<img width="1910" height="815" alt="Screenshot 2025-11-27 150710" src="https://github.com/user-attachments/assets/0342ed28-1858-45e1-b114-f9e4cc0a66b9" />

---

## 🛠️ Tech Stack
- **Java Spring Boot** (Backend API)
- **MySQL (AWS RDS)** (Database)
- **Streamlit (Python)** (Frontend UI)
- **AWS EC2** (Hosting)

---

## 📖 Notes
- Ensure **Backend Private IP** is correctly set in `frontend.service` under `API_URL`.
- Security groups allow **all traffic (0.0.0.0/0)** for simplicity. For production, restrict access.
- Logs for backend are stored in `/var/log/app/nohup.out`.

---

## 🎯 Author
Created by **Chandan Mohanty**  
DevOps Engineer | Full-Stack Developer | Documentation Craftsman


---

## 🏗️ Project Architecture

                ┌───────────────────────────┐
                │         End User          │
                │   Browser (Port 8501)     │
                └─────────────▲─────────────┘
                              │
                              │ HTTP Request
                              │
                ┌─────────────┴─────────────┐
                │   Frontend EC2 (t3.micro) │
                │   Streamlit App (Python)  │
                │   Port: 8501              │
                └─────────────▲─────────────┘
                              │
                              │ REST API Calls (HTTP)
                              │
                ┌─────────────┴─────────────┐
                │   Backend EC2 (t3.micro)  │
                │   Spring Boot App (Java)  │
                │   Port: 8084              │
                └─────────────▲─────────────┘
                              │
                              │ JDBC Connection
                              │
                ┌─────────────┴─────────────┐
                │    AWS RDS (MySQL)        │
                │ DB Identifier: database-1 │
                │    Port: 3306             │
                └───────────────────────────┘
                   
---
## 🏁 THE END
This completes the deployment setup for both Backend and Frontend EC2 instances with a clear architecture view. 🎉

