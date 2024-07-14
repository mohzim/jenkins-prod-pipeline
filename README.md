# Jenkins Production CI Pipeline

## Overview
This a sample production Jenkins pipeline for deploying  java-spring-boot application in AWS Kubernetes clusters. This uses GitOps approach for CI/CD using Jenkins and ArgoCD respectively. 

## Prerequisite
1. Create an EC2 instance in AWS:
    Go to AWS Console > EC2 > Instances (running) > Launch Instances > Name Jenkins EC2 >  Select Ubuntu 22.04 (ami-0c2af51e265bd5e0e) > Instance type t2.large > Select/Create Key Pair >  Launch Instance. Make a note of the Public IPv4 DNS - EC2 > Instances (running) > Select above EC2 > Find Public IPv4 DNS
2. Update Security Group:
    Go to EC2 > Select Jenkins EC2 > Scroll to Security tab > Select Security group (sg-*) > Edit Inbound Rules > Add Rule > Custom TCP, Port 8080 , Custome Source 0.0.0.0/0 > Save Rule

## Installation Steps
1. Connect to your EC2 instance through SSH
    ```
    sudo ssh -i <Your Key Pair>.pem ubuntu@<EC2 Public IPv4 DNS>
    ```
2. Install Java and verify version
    ```
    sudo apt update
    sudo apt install openjdk-11-jre
    java -version
    ```
3. Install Jenkins: 
    ```
    curl -fsSL https://pkg.jenkins.io/debian/jenkins.io-2023.key | sudo tee \
    /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
    https://pkg.jenkins.io/debian binary/ | sudo tee \
    /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt-get update
    sudo apt-get install jenkins
    ```
4. Save initial Jenkisn admin password:
    ```console
    sudo cat /var/lib/jenkins/secrets/initialAdminPassword
    ```
5. Install Docker (to use Jenkins Docker Agent later)
    ```
    sudo apt update
    sudo apt install docker.io
    sudo su - 
    usermod -aG docker jenkins
    usermod -aG docker ubuntu
    systemctl restart docker
    ```
6. Insall SonarQube on EC2
    ```
    apt install unzip
    adduser sonarqube
    sudo su sonarqube
    wget https://binaries.sonarsource.com/Distribution/sonarqube/sonarqube-9.4.0.54424.zip
    unzip *
    chmod -R 755 /home/sonarqube/sonarqube-9.4.0.54424
    chown -R sonarqube:sonarqube /home/sonarqube/sonarqube-9.4.0.54424
    cd sonarqube-9.4.0.54424/bin/linux-x86-64/
    ./sonar.sh start
    ```


Login into Jenkins:
    URL: http://<EC2 Public IPv4 DNS>:8080
    Username:admin
    Password: <check step 5>

7. Install Suggested Plugins
8. Skip step to create new admin user and launch Jenkins
9. Go to Manage Jenkins > Manage Plugins > Search below in Available plugins > Select and install.
    - Docker Pipeline
    - Git plugin
    - Maven Integration plugin
    - Pipeline plugin
    - Kubernetes Continuous Deploy plugin


10. Restart Jenkins after the plugin is installed.
    ```
    http://<EC2 Public IPv4 DNS>:8080/restart
    ```

## Test the build through Docker

## Jenkins Pipeline

## Attribution
This repository is adapted from [Jenkins-Zero-To-Hero](https://github.com/iam-veeramalla/Jenkins-Zero-To-Hero).

## To Do List
1. Convert AWS Jenkins setup into Terraform Script. 
2. Create shell script for Jenkins installation and setup. 
3. Add steps to configure SonarQube Integration. Generate token in SonarQube and add as secret in Jenkins