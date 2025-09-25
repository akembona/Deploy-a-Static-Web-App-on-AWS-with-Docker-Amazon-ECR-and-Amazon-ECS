![Alt text](/Host a Static Web App On AWS Using Docker.jpeg)


# Static Web App Deployment on AWS with Docker, Amazon ECR, and Amazon ECS

This project demonstrates the deployment of a **Static HTML Web Application on Amazon Web Services (AWS) using Docker, Amazon Elastic Container Registry (ECR), and Amazon Elastic Container Service (ECS)**.

The repository includes a **reference architecture diagram** and **deployment scripts** that walk through the process of containerizing the application, storing images in registries, and deploying to AWS ECS with high availability and secure communication.

---

## 🚀 Project Overview

The key objectives of this project were:

1. Containerize a static HTML application using **Docker**.
2. Store container images in **Docker Hub** and **Amazon ECR**.
3. Deploy the containerized application on **Amazon ECS** with load balancing and security.
4. Use **Route 53** and **AWS Certificate Manager** to enable a custom domain with HTTPS.

---

## 🛠️ AWS Resources Used

1. **VPC** with public and private subnets across **two Availability Zones**.
2. **Internet Gateway**, **NAT Gateway**, and **Bastion Host** for networking and secure access.
3. **Security Groups** for both the **Application Load Balancer (ALB)** and ECS containers.
4. **Application Load Balancer (ALB)** for distributing traffic to ECS tasks.
5. **Amazon ECS Cluster** with a Task Definition and Service.
6. **Amazon ECR Repository** to store container images.
7. **Route 53** for domain name registration and DNS record sets.
8. **AWS Certificate Manager (ACM)** for SSL/TLS certificates to secure communication.

---

## 📦 Deployment Workflow

1. **Dockerfile Creation**

   * A Dockerfile was created to define the web server environment and copy application files.

2. **Build and Run Container Locally**

   ```bash
   docker build -t static-web-app .
   docker run -p 80:80 static-web-app
   ```

3. **Push Image to Docker Hub**

   * Tag the image:

     ```bash
     docker tag static-web-app <your-dockerhub-username>/static-web-app
     ```
   * Push to Docker Hub:

     ```bash
     docker push <your-dockerhub-username>/static-web-app
     ```

4. **Push Image to Amazon ECR**

   * Authenticate Docker to Amazon ECR:

     ```bash
     aws ecr get-login-password --region <region> | docker login --username AWS --password-stdin <account_id>.dkr.ecr.<region>.amazonaws.com
     ```
   * Tag and push:

     ```bash
     docker tag static-web-app:latest <account_id>.dkr.ecr.<region>.amazonaws.com/static-web-app:latest
     docker push <account_id>.dkr.ecr.<region>.amazonaws.com/static-web-app:latest
     ```

5. **Create ECS Cluster & Task Definition**

   * ECS Task uses the container image from **Amazon ECR**.
   * ECS Service ensures high availability and scaling across subnets.

6. **Setup Application Load Balancer**

   * ALB distributes traffic across ECS containers.
   * Security Groups restrict inbound traffic to port 80/443.

7. **Configure Route 53 and ACM**

   * Route 53 maps the domain name to the ALB.
   * ACM provisions SSL/TLS certificate for HTTPS.

---

## 📜 Dockerfile

```dockerfile
FROM amazonlinux:latest

# Install dependencies
RUN yum update -y && \
    yum install -y httpd && \
    yum search wget && \
    yum install wget -y && \
    yum install unzip -y

# change directory
RUN cd /var/www/html

# download webfiles
RUN wget https://github.com/azeezsalu/jupiter/archive/refs/heads/main.zip

# unzip folder
RUN unzip main.zip

# copy files into html directory
RUN cp -r jupiter-main/* /var/www/html/

# remove unwanted folder
RUN rm -rf jupiter-main main.zip

# exposes port 80 on the container
EXPOSE 80

# set the default application that will start when the container starts
ENTRYPOINT ["/usr/sbin/httpd", "-D", "FOREGROUND"]
```

---

## 🔧 How to Reproduce

1. Clone this repository:

   ```bash
   git clone https://github.com/<your-username>/<your-repo>.git
   cd <your-repo>
   ```

2. Build and run the Docker container locally.

3. Push the image to **Docker Hub** and **Amazon ECR**.

4. Deploy to **Amazon ECS** with a Task Definition and Service.

5. Configure **ALB, Route 53, and ACM** for traffic routing and security.

---

## 📊 Architecture Diagram

📌 *(Reference architecture diagram included in the repository)*

---

## ✅ Key Features

* Containerized deployment with **Docker**
* Scalable infrastructure with **Amazon ECS**
* Secure communication via **ACM Certificates**
* High availability with **Multi-AZ deployment**
* Custom domain setup with **Route 53**

---

## 📌 Repository Structure

```
.
├── Dockerfile
├── deployment-scripts/
│   ├── vpc-setup.sh
│   ├── ecs-deploy.sh
│   └── ecr-push.sh
├── architecture-diagram.png
└── README.md
```

---

## 👨‍💻 Author

**Emmanuel Azure**
DevOps Engineer | Cloud Enthusiast

