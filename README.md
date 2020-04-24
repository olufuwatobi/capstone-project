# Capstone Project

This is the working repository for my Udacity DevOps Capstone Nanadegree Project
## Project Tasks:

* Working in AWS
* Using Jenkins to implement Continuous Integration and Continuous Deployment
* Building pipelines
* Working with CloudFormation to deploy clusters
* Building Kubernetes clusters
* Building Docker containers in pipelines

## Installation

Jenkins Installation :

```bash
sudo apt-get update
sudo apt install -y default-jdk
wget -q -O - https://pkg.jenkins.io/debian/jenkins.io.key | sudo apt-key add -
sudo sh -c 'echo deb https://pkg.jenkins.io/debian-stable binary/ > /etc/apt/sources.list.d/jenkins.list'
sudo apt-get update
sudo apt-get install -y jenkins
```

I followed this [procedure](https://medium.com/@andresaaap/capstone-cloud-devops-nanodegree-4493ab439d48) to build my pipeline
