
# DevOps-TwoTierApplication
Deploying two-tier application on aws

### **1. Project Overview**
This document outlines the step-by-step process for deploying two tier (Flask - MySQL) on AWS EC2 instance. Deployment is containerized using docker and compose. A full CI/CD pipeline is established using jenkins to automate build and deployment process whenever new code is pushed into github repository.

### **2. Architecture Diagram**

```
+-----------------+      +----------------------+      +-----------------------------+
|   Developer     |----->|     GitHub Repo      |----->|        Jenkins Server       |
| (pushes code)   |      | (Source Code Mgmt)   |      |  (on AWS EC2)               |
+-----------------+      +----------------------+      |                             |
                                                       | 1. Clones Repo              |
                                                       | 2. Builds Docker Image      |
                                                       | 3. Runs Docker Compose      |
                                                       +--------------+--------------+
                                                                      |
                                                                      | Deploys
                                                                      v
                                                       +-----------------------------+
                                                       |      Application Server     |
                                                       |      (Same AWS EC2)         |
                                                       |                             |
                                                       | +-------------------------+ |
                                                       | | Docker Container: Flask | |
                                                       | +-------------------------+ |
                                                       |              |              |
                                                       |              v              |
                                                       | +-------------------------+ |
                                                       | | Docker Container: MySQL | |
                                                       | +-------------------------+ |
                                                       +-----------------------------+
```

---

### **3. Step 1 : AWS EC2 Instance preparation**
1. Launch EC2 Instance:
  Navigate to EC2 console -- Launch new instance using ubuntu -- Select t2.micro instance type for free tier eligibility -- Create and assign a new key pair for ssh access

  <img width="1180" height="552" alt="Screenshot 2026-07-16 204010" src="https://github.com/user-attachments/assets/8a8af8e9-da56-4617-9f92-9f81b01a0466" />

2. Configure security group:
    * Create a security group with the following inbound rules:
        * **Type:** SSH, **Protocol:** TCP, **Port:** 22, **Source:** Your IP
        * **Type:** HTTP, **Protocol:** TCP, **Port:** 80, **Source:** Anywhere (0.0.0.0/0)
        * **Type:** Custom TCP, **Protocol:** TCP, **Port:** 5000 (for Flask), **Source:** Anywhere (0.0.0.0/0)
        * **Type:** Custom TCP, **Protocol:** TCP, **Port:** 8080 (for Jenkins), **Source:** Anywhere (0.0.0.0/0)


<img width="1566" height="691" alt="Screenshot 2026-07-16 204645" src="https://github.com/user-attachments/assets/053c201a-7c35-439c-96b6-e8a1113be5bb" />


3. Connect to EC2 Instance:
 * Use SSH to connect to the instance's public IP address.
     ```bash
    ssh -i /path/to/key.pem ubuntu@<ec2-public-ip>
    ```
### **4. Step 2: Install Dependencies on EC2**

1.  **Update System Packages:**
    ```bash
    sudo apt update && sudo apt upgrade -y
    ```

2.  **Install Git, Docker, and Docker Compose:**
    ```bash
    sudo apt install git docker.io docker-compose-v2 -y
    ```

3.  **Start and Enable Docker:**
    ```bash
    sudo systemctl start docker
    sudo systemctl enable docker
    ```

4.  **Add User to Docker Group (to run docker without sudo):**
    ```bash
    sudo usermod -aG docker $USER
    newgrp docker
    ```

---
