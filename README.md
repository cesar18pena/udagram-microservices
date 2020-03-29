# Udagram Microservices - (Udacity Cloud Developer Nanodegree)

## Getting Started
Udagram is a simple cloud application developed as one of the Udacity Cloud Engineering Nanodegree. The idea of the project is to design, deploy and operate a cloud native photo sharing application.

### Requirement Tools:
You need to install the following programs in your computer:  
- [Docker](https://docs.docker.com/docker-for-mac/install/)  
- [AWS CLI](https://docs.aws.amazon.com/cli/latest/userguide/install-macos.html)  
- [Eksctl](https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html)  
- [AWS-iam-authenticator](https://docs.aws.amazon.com/eks/latest/userguide/install-aws-iam-authenticator.html)  
- [Kubectl](https://docs.aws.amazon.com/eks/latest/userguide/install-kubectl.html)  

### Installation
Check if your installation were successful with the following commands:  
- `docker --version`  
- `aws --version`  
- `eksctl version`  
- `kubectl version --short --client`  
- `aws-iam-authenticator version`  

![Setup Installed in my computer](screenshots/setting-installed.png)  


### Setup Environment Variables
Open your terminal profile to store your application variables: 
```
nano ~/.zshrc
```
Copy and paste the lines in your file with your values:
```
export POSTGRESS_USERNAME=(Username of the database);
export POSTGRESS_PASSWORD=(Password of the database);
export POSTGRESS_DB=(Name of the Database);
export POSTGRESS_HOST=(URL Endpoint provided by AWS);
export AWS_REGION=(Region where your database is hosted);
export AWS_PROFILE=(Name of the IAM profile with correct access);
export AWS_BUCKET=(Name of S3 bucket);
export JWT_SECRET=(Random String to use JWT);
```

```
source ~/.zshrc
```

*Note: I personally use ZSH terminal in case you use BASH terminal , you will need to enter those values in your `.bash_profile`*

### Setup Docker Environment

- First build the images: 
  - `docker-compose -f docker-compose-build.yaml build --parallel`  
  
- Second list your docker images to check if they have been built:
  - `docker images` 
   
![Build-images](screenshots/build-images.png)  

- Third Run your docker containers: 
  - `docker-compose up`
  - To exit run `control + C`  

![Running-containers](screenshots/running-images.png)  

- Fourth push your docker images:
  - `docker-compose -f docker-compose-build.yaml push`  
  
- Fifth check your Docker Hub account to check if the images were uploaded:
![DockerHub](screenshots/docker-hub-published-images.png)  

### Create a Kubernetes Cluster on Amazon EKS using eksctl
Copy and paste the script bellow with your cluster name and configuration variables:

```
eksctl create cluster \ 
--name udagram-microservices \
--version 1.14 \
--nodegroup-name standard-workers \
--node-type t3.medium \
--nodes 3 \
--nodes-min 1 \
--nodes-max 4 \
--node-ami auto
```

![Creating Cluster](screenshots/creating-kubernete-cluster.png) 

 ### Create Kubernetes Components (Configmaps and Secrets) and load secret values

- First: encrypt your AWS file, database username and password using base64 using the following commands:
  - `cat ~/.aws/credentials | base64`  
  - `echo POSTGRESS_USERNAME | base64`  
  - `echo POSTGRESS_PASSWORD | base64`  

- Second: now add these values in your `env-secret.yaml`, `aws-secret.yaml`, and `env-configmap.yaml`.

- Third: load secret files with following commands:
  - `kubectl apply -f aws-secret.yaml`
  - `kubectl apply -f env-secret.yaml`
  - `kubectl apply -f env-configmap.yaml`
  - `kubectl apply -f .`
  
![Loading Secrets](screenshots/loading-kubernetes-secrets.png) 

### Application is monitored by Amazon CloudWatch

To Enabling Control Plane Logs:
  - Open the [Amazon-EKS](https://console.aws.amazon.com/eks/home#/clusters) console.
  - Choose the name of your cluster to display cluster information.
  - Under Logging, choose Update.
  - For each individual log type, choose whether the log type should be Enabled or Disabled. By default, each log type is Disabled; choose  update to finish.

![CLUSTERS-EKS](screenshots/aws-clusters.png)

![AWS-CloudWatch](screenshots/aws-logging.png) 
