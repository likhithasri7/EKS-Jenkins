<p align="center">

![Kubernetes](https://img.shields.io/badge/kubernetes-%23326ce5.svg?style=for-the-badge&logo=kubernetes&logoColor=white) ![AWS](https://img.shields.io/badge/AWS-%23FF9900.svg?style=for-the-badge&logo=amazon-aws&logoColor=white) ![Terraform](https://img.shields.io/badge/terraform-%235835CC.svg?style=for-the-badge&logo=terraform&logoColor=white) ![Docker](https://img.shields.io/badge/docker-%230db7ed.svg?style=for-the-badge&logo=docker&logoColor=white) ![Nginx](https://img.shields.io/badge/nginx-%23009639.svg?style=for-the-badge&logo=nginx&logoColor=white) ![Jenkins](https://img.shields.io/badge/jenkins-%232C5263.svg?style=for-the-badge&logo=jenkins&logoColor=white) ![Shell Script](https://img.shields.io/badge/shell_script-%23121011.svg?style=for-the-badge&logo=gnu-bash&logoColor=white)

![Stars](https://img.shields.io/github/stars/likhithasri7/EKS-Jenkins?style=for-the-badge) ![Forks](https://img.shields.io/github/forks/likhithasri7/EKS-Jenkins?style=for-the-badge) ![Issues](https://img.shields.io/github/issues/likhithasri7/EKS-Jenkins?style=for-the-badge) ![License](https://img.shields.io/github/license/likhithasri7/EKS-Jenkins?style=for-the-badge)

</p>

# 🚀 EKS-Jenkins-CICD

Automate CI/CD by deploying **Jenkins** on an **AWS EKS Kubernetes Cluster** using **Terraform** and **Helm**. 

Leverage **Jenkins Configuration as Code (JCasC)** to automatically configure Jenkins. Authentication and authorization are handled using the **GitHub OAuth** plugin and the **Matrix-Auth** plugin. Automate CI/CD pipelines by setting up a GitHub App and scanning GitHub Repositories for the presence of a `Jenkinsfile` using the **GitHub Branch Source** plugin. Finally, configure dynamic **Kubernetes Pod Agents** on the EKS cluster to execute pipeline stages on demand.

---

## 🛠️ Prerequisites & Dependencies

* **Docker & Docker Desktop** installed and running.
* **AWS User** with programmatic access and IAM permissions for EKS and S3.
* Existing **[EKS Kubernetes Cluster](https://github.com/likhithasri7/EKS-Terraform)** with Terraform remote state stored in S3.
* **[NGINX Ingress Controller](https://github.com/likhithasri7/EKS-Nginx-Ingress)** installed on the cluster.

---

## ⚙️ Configuration Setup

### 1. GitHub OAuth App Setup
Follow the [GitHub OAuth Plugin Guide](https://plugins.jenkins.io/github-oauth/):
1. Visit [GitHub Developer Settings → OAuth Apps → Register a new application](https://github.com/settings/applications/new).
2. Set the Authorization Callback URL to: `https://jenkins.example.com/securityRealm/finishLogin` (replace `jenkins.example.com` with your actual domain).
3. Copy your **Client ID** and **Client Secret**.

### 2. GitHub App Setup
Follow the [CloudBees GitHub App Guide](https://docs.cloudbees.com/docs/cloudbees-ci/latest/traditional-admin-guide/github-app-auth#_adding_the_jenkins_credential) to generate your **App ID**, **ID**, and **Private Key (`.pem`)**.

### 3. Project Configuration
1. Clone the repository:
   ```bash
   git clone https://github.com/likhithasri7/EKS-Jenkins.git
   cd EKS-Jenkins
   ```
2. Create the external Docker shared volume:
   ```bash
   docker volume create aws-credentials
   ```
3. Update [chart_values.yaml](chart_values.yaml):
   - Set `hostName` and `jenkinsUrl` under `controller.ingress`.
   - Update `clientID` and `clientSecret` under `securityRealm.github`.
   - Fill in `appID`, `id`, and paste `privateKey` under `credentials.system`.
4. Update [kubernetes.tf](kubernetes.tf):
   - Configure the AWS S3 bucket name and key for your remote EKS state.

---

## 🚀 Execution Guide

### 1. Configure AWS CLI inside Docker
```powershell
docker-compose run --rm aws configure --profile terraform
docker-compose run --rm aws sts get-caller-identity
```

### 2. Run Terraform via Docker
On **Windows (PowerShell)**:
```powershell
docker-compose run --rm terraform init
docker-compose run --rm terraform workspace select default
docker-compose run --rm terraform plan
docker-compose run --rm terraform apply
```

On **Linux / WSL / Bash**:
```bash
chmod +x run-docker-compose.sh
./run-docker-compose.sh terraform init
./run-docker-compose.sh terraform plan
./run-docker-compose.sh terraform apply
```

### 3. Verify Deployment
```powershell
docker-compose run --rm kubectl get all -n cicd
docker-compose run --rm kubectl get ingress -n cicd
```

---

## 🔐 Accessing Jenkins & Running Pipelines

1. Open `https://jenkins.<your-domain>.com` in your browser.
2. Sign in with your **GitHub** account via OAuth.
3. Create a **GitHub Organization** item in Jenkins:
   - Select the **GitHub App** credential created earlier.
   - Click **Scan Organization Now**.
4. Jenkins will automatically detect any repository containing a `Jenkinsfile` and spin up dynamic Kubernetes agent pods on your EKS cluster to execute the pipeline stages.
5. Visualize pipeline progress in real-time using **Jenkins BlueOcean**.

---

## 👤 Author

* **likhithasri7** - [GitHub Profile](https://github.com/likhithasri7)

---

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.
