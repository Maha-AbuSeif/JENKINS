# Automated CI/CD Pipeline: Jenkins-Driven Docker Image Build and Deployment on EKS with Amazon Aurora MySQL and Load Balancer Integration

![image](https://github.com/user-attachments/assets/add8a955-dcb3-4d07-aea8-1b4e50f0bae1)
![image](https://github.com/user-attachments/assets/804afca9-27ae-412b-bb7a-f696669af863)
![image](https://github.com/user-attachments/assets/d77dc3ee-8ac9-41d8-9619-a9c451a22604)
![image](https://github.com/user-attachments/assets/8e3d969a-f271-40ed-a119-baf566399afd)
![image](https://github.com/user-attachments/assets/3bfb1e10-f85e-4cdc-85e3-df72018fb223)
![image](https://github.com/user-attachments/assets/273353d7-c34f-48f4-908d-4587fbdb9d52)

![deployment (2)](https://github.com/user-attachments/assets/d1acbbbd-5e63-4e27-acc6-4fed51936f7e)


# 🚀 Jenkins CI/CD Pipeline Documentation

This pipeline automates the build, test, delivery, and deployment process for a Dockerized application using Jenkins, Docker Hub, AWS EKS and AWS Aurora Mysql.


## 🧱 Stage 1: Build & Test

- 🔨 Builds the Docker image from the `/app` directory.
- 🏷️ Tags the image using the current Git commit (`${GIT_COMMIT}`).

---

## 🚚 Stage 2: Delivery (Push to Docker Hub)

- 🔐 Uses Jenkins stored credentials (`Docker_Creds`).
- 🔑 Authenticates to Docker Hub.
- 📤 Pushes the built image with the unique Git commit tag.

---

## 🛢️ Stage 3: Update DB Endpoint

- 🔒 Retrieves database endpoint from Jenkins credentials (`db_endpoint`).
- 📄 Replaces template variables in `mysql-service.yaml` using `envsubst`.

---

## ☁️ Stage 4: Deploy to AWS EKS

- 🔑 Uses AWS credentials stored in Jenkins (`aws-cred`).
- 🔄 Replaces database info and credetianls references in deployment manifest from Jenkins credentials.
- 🔄 Replaces image reference in deployment manifest from Jenkins credentials.
- 🌐 Connects to the EKS cluster (`python-app-cluster`) via AWS CLI.
- 🚢 Applies the Kubernetes manifests:
  - `mysql-service.yaml`
  - `app-deployment.yaml`
  - `app-loadbalancer-service.yaml`

---

## 📊 Summary Table

| Stage               | Description                                  | Tools Involved                           |
|---------------------|----------------------------------------------|-------------------------------------------|
| 🧱 Build & Test      | Build Docker image from codebase             | Docker                                     |
| 🚚 Delivery          | Push Docker image to Docker Hub              | Docker, Jenkins credentials: `Docker_Creds`🐳                |
| 🛢️ Update DB Endpoint| Inject database endpoint into YAML template | `envsubst`, Jenkins credentials: `db_endpoint`🌐           |
| 🔑 Update DB Credentials    | Inject database credentials in Kubernetes deployment | `envsubst`, Jenkins credentials: `DB_HOST` 🏠, `DB_USER` 👤, `DB_PASS` 🔑, `DB_DATABASE` 🗄️ |
| ☁️ Deploy to EKS     | Deploy app and services to AWS EKS           | AWS CLI, `kubectl`, `envsubst`, Kubernetes, Jenkins credentials: `aws-cred` ☁️|


---

> ✅ **Tip:** Make sure your credentials (`Docker_Creds`, `db_endpoint`, `aws-cred`,`DB_HOST`,`DB_USER`,`DB_PASS`,`DB_DATABASE`) are set up in Jenkins before running this pipeline. Also install these 2 jenkins plugins: AWS Credentials and Pipeline: AWS Steps 



