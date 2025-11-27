Here’s a complete **README.md** file for your **JAVA-SPRINGBOOT-PROJECT** setup. 
It documents all the steps you outlined in a clean, professional, and beginner-friendly way:

<img width="1200" height="771" alt="image" src="https://github.com/user-attachments/assets/cc7a3e8a-299d-4902-b05e-a6d64de0890e" />

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
<img width="1230" height="209" alt="Screenshot 2025-11-27 141511" src="https://github.com/user-attachments/assets/ac29c4ab-4a25-4bcf-8734-25506740173f" />

----

- 

2. Run Spring Boot JAR with MySQL connection:
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

<img width="432" height="29" alt="Screenshot 2025-11-27 143245" src="https://github.com/user-attachments/assets/60aac423-f72b-4bca-9e74-8085b7426b2b" />

   
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
<img width="1287" height="217" alt="Screenshot 2025-11-27 143519" src="https://github.com/user-attachments/assets/600208de-2dc3-4a4a-a29c-3017f3e85110" />

-----
3. Reload and start service:
   ```bash
   systemctl daemon-reload
   systemctl enable frontend
   systemctl start frontend
   systemctl status frontend
   ```
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
```

---

Would you like me to also add a **diagram (architecture flow)** in the README so it visually shows how RDS, Backend, and Frontend connect? That would make it even more beginner-friendly and professional.
