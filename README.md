# DEPI-Project
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
- 🔄 Replaces image reference in deployment manifest.
- 🌐 Connects to the EKS cluster (`python-app-cluster`) via AWS CLI.
- 🚢 Applies the Kubernetes manifests:
  - `secrests.yaml`
  - `mysql-service.yaml`
  - `app-deployment.yaml`
  - `app-loadbalancer-service.yaml`

---

## 📊 Summary Table

| Stage               | Description                                  | Tools Involved                           |
|--------------------|----------------------------------------------|-------------------------------------------|
| 🧱 Build & Test      | Build Docker image from codebase             | Docker                                     |
| 🚚 Delivery          | Push Docker image to Docker Hub              | Docker, Jenkins credentials                |
| 🛢️ Update DB Endpoint| Inject database endpoint into YAML template | `envsubst`, Jenkins credentials            |
| ☁️ Deploy to EKS     | Deploy app and services to AWS EKS           | AWS CLI, `kubectl`, `envsubst`, Kubernetes |

---

> ✅ **Tip:** Make sure your credentials (`Docker_Creds`, `db_endpoint`, `aws-cred`) are set up in Jenkins before running this pipeline. Also install these 2 jenkins plugins: AWS Credentials and Pipeline: AWS Steps 

```

