Here’s a complete **README.md** file for your **JAVA-SPRINGBOOT-PROJECT** setup. It documents all the steps you outlined in a clean, professional, and beginner-friendly way:


# JAVA-SPRINGBOOT-PROJECT 🚀

This project demonstrates deploying a **Java Spring Boot backend** with a **Streamlit frontend** on AWS using **RDS (MySQL)** and **EC2 instances**.

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

---

## ⚙️ Step 2: Launch EC2 Instances
Launch **two EC2 instances**:

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
1. Connect to **Backend EC2**:
   ```bash
   sudo su -
   yum install git -y
   git clone https://github.com/chintu-cloud/JAVA-SPRINGBOOT-PROJECT.git
   yum install maven -y
   cd Java-springboot-project
   mvn clean package -Dspring.profiles.active=build
   cd target
   mv datastore-0.0.7.jar /root        

   ```
----
📌 Note:

Move the JAR file to the root directory using this command:
 ```
mv <filename> /root
 ```
----

- 

2. Run Spring Boot JAR with MySQL connection:
   ```bash
   MYSQL_HOST=jdbc:mysql://database-1.c6ricqgseec0.us-east-1.rds.amazonaws.com:3306/datastore?createDatabaseIfNotExist=true \
   MYSQL_USERNAME=admin \
   MYSQL_PASSWORD=chandan#1234 \
   nohup java -jar ./datastore-0.0.7.jar > /var/log/app/nohup.out 2>&1 &
   ```

3. Verify:
   ```bash
   ps aux | grep jar
   cat /var/log/app/nohup.out
   ```

---

## ⚙️ Step 4: Setup Frontend (Streamlit)
1. Connect to **Frontend EC2**:
   ```bash
   sudo su -
   yum install git -y
   git clone https://github.com/chintu-cloud/JAVA-SPRINGBOOT-PROJECT.git
   cd Java-springboot-project
   yum install python3-pip -y
   python3 -m venv venv
   source venv/bin/activate
   pip install --upgrade pip
   pip install streamlit requests
   ```

2. Create **systemd service**:
   ```bash
   vi /etc/systemd/system/frontend.service
   ```

   Add the following: (/etc/systemd/system/frontend.service --> inside enter below code)
   ```ini
   [Unit]
   Description=Streamlit Frontend App
   After=network.target

   [Service]
   User=root
   WorkingDirectory=/root/Java-springboot-project/frontend
   ExecStart=/root/Java-springboot-project/frontend/venv/bin/python -m streamlit run /root/Java-springboot-project/frontend/app.py --server.port=8501 --server.address=0.0.0.0
   Environment=API_URL=http://<BACKEND_PRIVATE_IP>:8084
   Restart=always
   RestartSec=5

   [Install]
   WantedBy=multi-user.target
   ```

3. Reload and start service:
   ```bash
   systemctl daemon-reload
   systemctl enable frontend
   systemctl start frontend
   systemctl status frontend
   ```

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
```

---

Would you like me to also add a **diagram (architecture flow)** in the README so it visually shows how RDS, Backend, and Frontend connect? That would make it even more beginner-friendly and professional.
