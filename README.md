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
