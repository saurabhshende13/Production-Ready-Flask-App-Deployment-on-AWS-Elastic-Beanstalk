### 🚀 **Production-Ready Flask App Deployment on AWS Elastic Beanstalk**  

Welcome to the **Flask App Deployment on AWS Elastic Beanstalk** project! In this guide, we'll walk through deploying a production-ready Flask application on **AWS Elastic Beanstalk**, configuring it with **Route 53** to point to a custom domain, and ensuring a robust deployment setup.  

## 🏗 **Project Overview**  
This project demonstrates how to deploy a Flask application on **AWS Elastic Beanstalk**, enabling auto-scaling, monitoring with CloudWatch, and domain configuration with **AWS Route 53**.  

## 🛠 **Architecture & AWS Services Used**  
- **Elastic Beanstalk** – Managed platform for deploying Flask applications  
- **EC2** – Virtual instances to host the application  
- **IAM Roles** – Access permissions for Elastic Beanstalk resources  
- **CloudWatch** – Logs and performance monitoring  
- **Route 53** – Domain management for custom domain mapping  
- **S3 (Optional)** – Log storage for better debugging and auditing  
- **RDS (Optional)** – If using a database for the Flask app  

---

## 🚀 **Step 1: Deploying the Flask App on AWS Elastic Beanstalk**  

### 1️⃣ **Create an Elastic Beanstalk Application**  
1. Navigate to the **AWS Elastic Beanstalk Console**.  
2. Click **Create Application** and enter:  
   - **Application Name**: `flask-app`  
   - **Environment Name**: `flask-app-env-prod`  
3. Select the **Platform**:  
   - Choose **Python** (since Flask is a Python-based framework).  
   - Use the latest available version.  

### 2️⃣ **Upload and Deploy the Flask Application**  
1. Compress your **Flask project folder** into a **ZIP file** (e.g., `flask-app.zip`).  
2. Upload the ZIP file to **Elastic Beanstalk**.  

### 3️⃣ **Set Up IAM Roles (Service Role & EC2 Instance Profile Role)**  

#### **A. Create the Elastic Beanstalk Service Role**  
1. Go to **IAM > Roles > Create Role**.  
2. Select **Elastic Beanstalk** as the trusted entity.  
3. Attach the following **Managed Policies**:  
   - `AWSElasticBeanstalkManagedUpdatesCustomerRolePolicy` (For managed updates)  
   - `AWSElasticBeanstalkService` (For managing Beanstalk resources)  
   - `AWSCodeDeployRoleForElasticBeanstalk` (For deployments)  
   - `AmazonS3FullAccess` (For S3 access if needed)  
   - `AmazonEC2FullAccess` (For managing EC2 instances)  
4. Click **Create Role** and name it: `aws-elasticbeanstalk-service-role`.  

#### **B. Create the EC2 Instance Profile Role**  
1. Go to **IAM > Roles > Create Role**.  
2. Select **EC2** as the trusted entity.  
3. Attach the following **Managed Policies**:  
   - `AWSElasticBeanstalkWebTier` (For EC2 access to Elastic Beanstalk)  
   - `CloudWatchAgentServerPolicy` (For logging and monitoring)  
   - `AmazonS3ReadOnlyAccess` (For accessing S3 logs, optional)  
   - `AmazonRDSFullAccess` (If using RDS, optional)  
4. Click **Create Role** and name it: `aws-elasticbeanstalk-ec2-role`.  
5. Navigate to **IAM > Instance Profiles > Create Instance Profile**, name it `aws-elasticbeanstalk-ec2-profile`, and attach the newly created role.  

### 4️⃣ **Configure Elastic Beanstalk Environment**  
1. **Assign IAM Roles**:  
   - Select **Service Role**: `aws-elasticbeanstalk-service-role`.  
   - Select **Instance Profile**: `aws-elasticbeanstalk-ec2-profile`.  
2. **Key Pair (Optional)**: Select a **Key Pair** if you want SSH access to the instance.  
3. **VPC & Subnet Configuration (Optional)**:  
   - If using a **private VPC** setup, provide **VPC and Subnet details**.  
   - Configure **RDS** if required.  
4. **Root Volume & Storage**:  
   - Use the **default container root volume** for deployment.  
5. **CloudWatch Monitoring**:  
   - Enable **Enhanced CloudWatch Logs** for better monitoring.  
6. **Security Group Assignment**:  
   - Assign an **appropriate security group** for inbound/outbound traffic.  
7. **Auto Scaling Configuration**:  
   - Configure auto-scaling policies based on CPU usage.  
8. **Instance Type & AMI**:  
   - Choose an **instance type** (e.g., `t3.micro` for low-cost or `t3.medium` for higher performance).  
   - Specify a custom **AMI ID** if required.  
9. **Health Reporting**:  
   - Select **Basic** or **Enhanced** health reporting.  
10. **Managed Updates & Notifications**:  
    - Keep **platform-managed updates disabled** initially.  
    - Configure **email notifications** for health alerts.  
11. **Proxy & Log Configuration**:  
    - Specify a **proxy server** (default: Nginx).  
    - Enable **S3 log storage** and **CloudWatch log streaming**.  

### 5️⃣ **Verify Deployment & Debug Issues**  
1. Once deployed, check the **Elastic Beanstalk URL** (`http://flask-app-env-prod.elasticbeanstalk.com`).  
2. If the app is not responding, restart the Flask server:  
   ```bash
   eb ssh flask-app-env-prod
   sudo systemctl restart flask
   ```  
3. You can also manually re-run commands inside `.ebextensions/flask.config` to apply missing configurations.  

---

## 🌍 **Step 2: Configure Route 53 for Custom Domain**  

### 1️⃣ **Create an A Record in Route 53**  
1. Open **AWS Route 53**.  
2. Select your hosted zone (e.g., `devildevops.live`).  
3. Create a new **A Record**:  
   - Type: **A Record (Alias)**  
   - Value: **Elastic Beanstalk Domain URL** (`flask-app-env-prod.elasticbeanstalk.com`).  

### 2️⃣ **Verify Domain Mapping**  
- Open your browser and navigate to `https://devildevops.live`.  
- It may take **a few minutes to several hours** for DNS changes to propagate.  

---

## 🎯 **Final Verification Checklist**  
✅ Flask app is accessible via the **Elastic Beanstalk domain**.  
✅ Custom domain (`devildevops.live`) resolves correctly.  
✅ CloudWatch logs show no critical errors.  
✅ Auto-scaling is working as expected.  

---

## 💡 **Troubleshooting Tips**  
❌ **App Not Loading?**  
- SSH into the instance and restart Flask.  
- Check logs in `/var/log/eb-engine.log`.  
- Verify `.ebextensions/flask.config` for misconfigurations.  

❌ **Domain Not Resolving?**  
- Flush local DNS cache (`ipconfig /flushdns` or `sudo systemd-resolve --flush-caches`).  
- Wait for DNS propagation (can take up to 24 hours).  

---

## 📌 **Conclusion**  
This guide covers the end-to-end deployment of a **Flask app on AWS Elastic Beanstalk** with a **custom Route 53 domain setup**. With this setup, your app is **scalable, monitored, and accessible via a user-friendly domain**. 🚀  

---

## 📜 **License**  
This project is open-source under the **MIT License**.  
---
