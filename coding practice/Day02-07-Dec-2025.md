# Day 02 - Services and Tags
## Ansible --> Install and enable Nginx

- Name: Install and start the nginx on Pathnex
  hosts: all
  become: yes

  tasks:
    - name: Install nginx
      yum:
        name: nginx
        state: present

    - name: Enable nginx
      Service:
        name: nginx
        state: started
        enabled: yes


## teraform --> EC2 with tags (t2.small)

provider "aws" {
      region = "ap-south-1" 
}

resource "aws_instance" "PathnexEC2" {
    ami = "ami-abc9013267544efg"
    instance_type = "t2.small"

    tags = {
      Name = "Pathnex-Server"
      Environment = "Training"
      Owner = "PathnexStudent"
    }
}


## Kubernetes Task --> Deployment with 2 replicas

apiVersion: apps/v1
kind: Deployment
metadata:
  name: pathnex-deployment
spec:
  replicas: 2                   # k8s will run 2 pod of nginx
  selector:                     #deployment use selector-->matchlabels to identify which pod it should control 
    matchLabels:
      app: pathnex-app          # this mean "find all pods that have the label app: pathnex-app" and manage only those
  template:                     #template define how each pod should look
    metadata:
      labels:                   # this label must match the selector above
        app: pathnex-app
    spec:                       # this define the conatiner inside the pod 
      containers:
        - name: app
          image: nginx
          ports:
            - containerPort: 80



## shells script--> Disk Usage 

#!/bin/bash
df -kh 


# Maven build 
## Jenkins pipeline --> Maven Build (used to learn how to compile, test and package a maven build) in groovy 

pipeline {
    agent any                   # run this pipeline in any available jenkins machine 
    tools {
        maven 'maven-3.8.1'     # It tell jenkins to use Maven 3.8.1 version for building the project 
        jdk 'JDK-17'            # Use java 17 for running Maven 
    }
    stages {
        stage('checkout') {
            steps {
                git url: 'https://github.com/Ravileo89/practice.git'
            }
        }
        stage ('compile') {
            steps {
                sh 'mvn clean compile'   #remove old files and compile the source code
            }
        }
        stage ('Test') {
            steps {
                sh 'mvn test'           # check if the code is working properly
            }
        }
        stage ('package') {
            steps {
                sh 'mvn package'        # creare a Jar/War file 
            }
        }
    }
}




## Gitlab CI--> Maven Build 

stages:
  - build
  - test
  - package

maven-build:
  stage: build
  image: maven:3.8.1-jdk-17
  script: 
    - git clone https://github.com/Ravileo89/practice.git
    - cd practice
    - mvn clean compile 

maven-test:
  stage: test
  image: maven:3.8.1-jdk-17
  script:
    - cd practice
    - mvn test

maven-package:
  stage: package
  image: maven:3.8.1-jdk-17
  script:
    - cd practice
    - mvn package


