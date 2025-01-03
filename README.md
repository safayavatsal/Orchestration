# Container-Orchestration
Develop Kubernetes deployment files for both frontend and backend components, ensuring seamless deployment and scalability.

Create a HELM chart to streamline the deployment process, allowing for easy configuration and management.

Write Jenkins Groovy code to automate the build and deployment process, ensuring consistency and efficiency in your CI/CD pipeline.

The provided GitHub repositories contain the frontend and backend codebases, which you will use as the basis for your deployment configurations.

https://github.com/UnpredictablePrashant/learnerReportCS_frontend
https://github.com/UnpredictablePrashant/learnerReportCS_backend

Please ensure to document your process thoroughly, including any challenges faced and solutions implemented. Your submission should demonstrate your understanding of Kubernetes deployment, HELM charts, and Jenkins automation in the context of a MERN stack application.

-------------------------------------------------------------------------------
Solution:

* clone both the repository and set up in local 

# Backend deployment

* go to config.env file and set up your mongodb url.
* move to index.js file and configure the port there or in .env file "port = process.env.PORT || 4200" 
* Docker file is exposed with the port 4200 build the docker file and push that into docker repositort.
```
docker build -t learnersreport_backend:v1.0 .
```
* After pushed the docker image create another folder and try to write kubernets file.
* Create backend_deployment.yaml and backend_service.yaml file.
* After created those files test that file whether cluster is launching fine or not.
```
kubectl apply -f backend_deployment.yaml
kubectl apply -f backend_service.yaml
# Command to chech service and pods are running fine
kubectl get svc
kubectl get pods
# To get minikube running url
minikube service backend-service --url 
```
* you will get output as mentioned below.
![alt text](backend_deployment.PNG)

# Frontend Deployment

* Docker file is exposed with the port 4200 build the docker file and push that into docker repositort.
* After pushed the docker image create another folder and try to write kubernets file.
* Create frontend_deployment.yaml and frontend_service.yaml file.
* Follow the kubernets deployment steps as same as backend
* you will get output as mentioned below.
![alt text](frontend_deployment.PNG)

# Helm Deployment 

* Create a helm chart 
```
helm create learner_helm 
```
* Create a seperate deployment and service file for both frontend and backend.
* Create all th values in value.yaml file.
* Then install the helm chart 
```
helm install learnerreport learner_helm
```
* you will get the result mentioned below
![alt text](helm_install.PNG)

# Jenkins Automation 

* Created new EC2 instance with jenkins, docker, kubernetes, helm, git awscli everything instance.
* once jenkins grt ready it will run in port 8080
```
EC2_instance_public_ip:8080
```
* Go to Credencials and added your aws, docker credencials.
* Then created the pipeline Jenkins file are updated above.
* Clone th ecode, build the docker image pushed that into repo, then created EKS cluster deploy the helm chart there.
* congrats your done with helm deployment

Happy Learning !!!!!!
