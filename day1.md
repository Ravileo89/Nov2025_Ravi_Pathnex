# Ansible Task --> Install Nginx on Pathnex server

---
- name: Install Nginx on Pathnex server
  hosts: all                    #Run this playbook on all servers listed in your Ansible inventory
  become: yes                   #Run all commands using sudo (root privileges) 

  tasks:                        #This section contains the actions Ansible should perform.
    - name: Install Nginx       #this is the name of the task and it describe what task it will do.
      yum:                      # it tell ansible to use yum package manager
        name: nginx             #This tell yum to install nginx package
        state: present          #Ensure that package is present, if not present, it will install


## Terraform Task — Create EC2 (c4.large)

provider "aws" {                  # It tell terraform to use aws as a cloud provider 
  region = "ap-south-1"           # Create all resources in region ap-south-1
}

resource "aws_instance" "PathnexEC2" {   # Creating EC2 VM of PathnexEC2 terraform internal name
  ami = "ami-abc9013267544def"           # choosing AMI for operating system
  instance_type = "c4.large"             # choosing hardware for EC2

tags = {
  Name = "PathnexEC2"
}

}


## Kubernetes Task - create Nginx pod

apiVersion: v1          # this tell k8s which api verison this object belong to. v1 is for basic pod
kind: Pod               # this tell k8s that i want to create a pod
metadata:               # this give my pod a name (pathnex)
  name: pathnex-pod
spec:                               # this section describe what is inside a pod 
  containers:                       # It tell cotainer will run inside the  pod   
    - name: web                     # name of the container
      image: nginx:latest           #pull the latest image from docker hub
      ports:                # It tell k8s this conatiner will listen on port 80 (default nginx port)
        - containerPort: 80   


## shell script - Print date and hostname 

#!/bin/bash                        # shebang , it tell system to run script using /bin/bash shell 
echo "Date: $(date)"               # echo --> print the string or text on screen
echo "Hostname: $(hostname)"       # $() --> mean "run this command and use it output here "



# Git Integration 
## Jenkins Pipeline-checkout git--> How to checkout a git repo and list file on groovy language

pipeline {                       #This start a jenkins pipeline
    agent any                    # tell jenkins to run this pipeline on any available jenkins agent 
    stages {                     #Stage tell the actions 
        stage('checkout') {      
            steps {
                git branch: 'main', url: 'https://github.com/Ravileo89/practice.git'   # Connect to github repository and pull the main branch 
            }
        }
        stage('List File') {
            steps {
                sh 'ls -la'               # Run the command to list the content
            }
        }
    }
}


## Gitlab CI- Checkout Git --> how to checkout the repo from Gitlab CI in Yaml 

stages:
  - checkout             # It tell gitlab that my pipeline has a stage called checkout

git-checkout:            # Gitlab will show this job in pipeline as git-checkout
  stage: checkout        # It will tell which stage this job belong to ( checkout, build, test, deploy)
  script:                #It will contain actual command that gitlab will execute 
    - git clone https://github.com/Ravileo89/practice.git
    - cd practice
    - ls -la




