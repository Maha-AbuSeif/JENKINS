# Automated CI/CD Pipeline: Docker Image Build and Deployment on EKS with Amazon Aurora MySQL and Load Balancer , HPA Integration

![image](https://github.com/user-attachments/assets/add8a955-dcb3-4d07-aea8-1b4e50f0bae1)
![image](https://github.com/user-attachments/assets/804afca9-27ae-412b-bb7a-f696669af863)
![image](https://github.com/user-attachments/assets/3134327c-49d4-4054-a407-7770c3fd70b9)
![image](https://github.com/user-attachments/assets/79f97d37-7860-46e3-80d7-aec31172dd0a)
![image](https://github.com/user-attachments/assets/01e530bd-4b02-4ec1-bc11-6ab683fab395)
![image](https://github.com/user-attachments/assets/3bfb1e10-f85e-4cdc-85e3-df72018fb223)
![image](https://github.com/user-attachments/assets/c7de7419-e552-437a-93d2-0bd29475c8c0)
![image](https://github.com/user-attachments/assets/57b83c14-da88-4aab-9b78-fa00a2a6d54c)

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

## 📊 Stage 5: Deploy Metrics Server & HPA

- 🔑 Uses AWS credentials stored in Jenkins (`aws-cred`)
- 🛠️ Applies configuration from `metrics-server.yaml`
- ⚖️ Configures Horizontal Pod Autoscaler (HPA)
   - 🎯 Target CPU utilization: 75%
   - 🔽 Minimum pods: 2
   - 🔼 Maximum pods: 5
   - 🔄 Automatically scales based on CPU usage
- 📈 Enables resource monitoring and auto-scaling
- 🚀 Optimizes performance under varying workloads

---

## 📊 Summary Table

| Stage               | Description                                  | Tools Involved                           |
|---------------------|----------------------------------------------|-------------------------------------------|
| 🧱 Build & Test      | Build Docker image from codebase             | Docker                                     |
| 🚚 Delivery          | Push Docker image to Docker Hub              | Docker, Jenkins credentials: `Docker_Creds`🐳                |
| 🛢️ Update DB Endpoint| Inject database endpoint into YAML template | `envsubst`, Jenkins credentials: `db_endpoint`🌐           |
| 🔑 Update DB Credentials    | Inject database credentials in Kubernetes deployment | `envsubst`, Jenkins credentials: `DB_HOST` 🏠, `DB_USER` 👤, `DB_PASS` 🔑, `DB_DATABASE` 🗄️ |
| ☁️ Deploy to EKS     | Deploy app and services to AWS EKS           | AWS CLI, `kubectl`, `envsubst`, Kubernetes, Jenkins credentials: `aws-cred` ☁️|
| ⚖️ Deploy Metrics Server | Configure HPA and deploy Metrics Server    | `kubectl`, Metrics Server, HPA, Jenkins credentials: `aws-cred` 📊|


---

> ✅ **Tip:** Make sure your credentials (`Docker_Creds`, `db_endpoint`, `aws-cred`,`DB_HOST`,`DB_USER`,`DB_PASS`,`DB_DATABASE`) are set up in Jenkins before running this pipeline. Also install these 2 jenkins plugins: AWS Credentials and Pipeline: AWS Steps 



