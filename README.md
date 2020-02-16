# Project: Continuous Integration & Deployment using Cloudformation, Jenkins, Docker & Kubernetes (EKS)

## Description

In this project continuous pipeline is created in order to demonstarte deployment & production stages.

The project is divided in three main parts:

* Creating a cloud netowrk and an EKS cluster in AWS with two nodes
* Code commits and pushed to docker hub
* Image pulled from docker registry and deployed to kubernetes pods

## Understanding Project

* First step is where we implemented clouformation stack to implement VPC, subnets, security group, an EKS cluster and launch configuration with two nodes.

* Second step includes an independent EC2 instance with jenkins and docker installed and configured.

* Third step include kiubernetes configured with AWS EKS cluster in cloud

* Forth step is the pipeline itself that take the commited code from github and push it to docker repository

* The pipeline include pulling the image from docker registry and deployment into kuberenetes pods through a load balancer

* Target group is assigned and health checks are performed on both instances. 

* Blue deployment is configured by default on three kubernetes pods

* After the new commit, code is passed to docker hub 

* Code is pulled from docker registry and to a temperory green deployment; initiated on runtime.

* Green deployment creates three new pods in the pipeline. Total of 6 pods now in pipeline

* Blue deployment is deleted, deleting three pods.

* In the mean time temperory green deployment is attached to load balancer showing updated website

* New deployment is created with the name of Blue with the new updates. 

* Temperory green deployment deleted to complete the circle and we are back on blue deployment with the updated website
    _Notes_
        * Project is configured as blue / green deployment in kubernetes pods where all the pods are connected to a single load balancer and service.


## Resources 

Project files are provided on github link. Clone the files using below link in terminal.
`$ git clone https://github.com/adeelbarki/capstone-clouddevops-docker-jenkins-aws-eks.git`

Make sure to `$ cd capstone-clouddevops-docker-jenkins-aws-eks` in terminal to access all files. 

## Run the code

Create Stack: 

* Create an EC2 instance with right permisions and user ID. 

* Clone the project into the EC2 instance. 

* Install all the required script. Script files can be found in folder 'scripts'. (Can be installed manualy too)

* Goto cfn folder and create stack using this command.
`aws cloudformation create-stack --stack-name eks-serv-node --region eu-central-1 --template-body file://amazon-eks-network.yml --parameters file://network-parameters.json --capabilities CAPABILITY_IAM`

    _Note:_ 
        * Before running this command, AWS configure must be configured with a user Acces key and Id. 
        * Single command will deploy network and server configuration on AWS. 
        * Region can be selected according to your location. In this case `eu-central-1` is selected.
        * AWS has limited instances in each region. In order to check your limit in specific region, goto EC2 > Limits in AWS console. This script is scaled to 4 instances and may not run if your EC2 limit is less than 4. In case of lower limit ask AWS help team to increase the limit. 

* Configure EKS cluster with EC2 instance using this command 
`aws eks update-kubeconfig --name nginxcluster`

* Copy role arn from cloudformation stack outputs into `aws-auth-cm.yml` file. 

* Configure aws-auth-cm.yml file in order to attach worker nodes to EKS cluster
`kubectl apply -f aws-auth-cm.yml` 

* Deploy initial code to kubernetes cluster and pods using blue deployment in Deployment
`kubectl apply -f blue-webapp-deploy.yml`

* Initial Deployment is created. Now we just have to create the continuous pipepline

* Configure Jenkins and do not forget to install blue ocean agregator and pipeline-aws in jenkins plugins
* Configure global credentials for aws and docker hub.
* Create pipeline using github token; it will automatically detect the jenkinsfile.
* In the porcess we can see all the steps in the pipeline that inlcudes linting, build, deploy and blue/green strategies. 


## Testing

In EC2, just goto Load balancer and select specific load balancer. Copy provided DNS and open it in chrome. 

DNS can also be obtained by typing `kubectl get svc` in command line.

Pods can be seen using command `kubectl get pods` and for nodes `kubectl get nodes

In order to test the pipeline, a new code is committed and it succefully update the website DNS in few minutes. 

