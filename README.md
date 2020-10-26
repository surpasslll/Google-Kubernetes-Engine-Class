# Google-Kubernetes-Engine-Class
Notes of Google Kubernetes Engine Class

# Lab1
### Overview 

* Containers are a way of isolating programs or processes from each other. The primary aim of containers is to make programs easy to deploy in a way that doesn't cause them to break.
* ref: docker build option: https://docs.docker.com/engine/reference/commandline/build/ 
* dockerfile: https://docs.docker.com/engine/reference/builder/#expose


### Clusters, Nodes, and Pods
* Kubernetes adds the ability to define how many machines to run, how many containers to deploy, how to scale them, where persistent disks reside, and how to deploy a group of containers as a unit.
* Kublets itself is the agent of Kubernetes
* A pod is analogous to a VM, sort of, in a group of containers sharing a network and storage that are separated from the nodes themselves. Underneath, you have the OS, you have the hardware, you have the NIC, you have an IP outside, inside you have the pod, and then inside the pod, you have the actual containers, and then the network interfaces for the containers.
* defined a pod in yaml file
* labele is very useful for searching in k8s


# Lab2
### Bash Completion (Optional)
* source <(kubectl completion bash)
* This feature makes using kubectl even easier.

### Use the kubectl get command to view the new service.
* kubectl get services
* It may take a few seconds before the ExternalIP field is populated for your service. This is normal—just re-run the kubectl get services command every few seconds until the field is populated.
* Use the external IP address with the curl command to test your demo application.
* curl http://<External IP>:80
* Kubernetes supports an easy-to-use workflow out of the box using the kubectl run, expose, and scale commands.

### Explore the built-in pod documentation.
* kubectl explain pods
* kubectl explain pods.xxx.xx could view more info

### List all pods running in the default namespace.
* kubectl get pods

### Get more information about the monolith pod.
* kubectl describe pods monolith
(Useful for troubleshooting)

* To quit kubectl port-forward and kubectl logs in terminal 2 and 3, press Ctrl+C

# Lab3: Deployments and Rolling Updates
### Overview
* Goal: get you ready for scaling and managing containers in production.
* Deployments：abstract away the low level details of managing pods
* Deployments (and ReplicaSets) are powered by control loops. Control loops are a design pattern for distributed software that allows you to declaratively define your desired state and have the software implement the desired state for you based on the current state.

### Learn About Deployment Objects
* kubectl explain deployment
* kubectl explain deployment --recursive //to see all of the fields
* kubectl explain deployment.metadata.name // understand the structure of a deployment object and understand what the individual fields do
* kubectl create -f deployments/auth.yaml  // Create the deployment object
* kubectl get deployments // verify deploy
* kubectl get replicasets // verify the ReplicaSet for the deployment.
* kubectl get pods // view the pods created for your deployment
* kubectl create -f services/auth.yaml //With your pod running, it's time to put it behind a service
* created a ConfigMap and secret for the frontend.
kubectl create -f deployments/hello.yaml
kubectl create -f services/hello.yaml
kubectl create configmap nginx-frontend-conf --from-file=nginx/frontend.conf
kubectl create secret generic tls-certs --from-file tls/
kubectl create -f deployments/frontend.yaml
kubectl create -f services/frontend.yaml
* kubectl get services frontend  // Get its external IP.
* curl -ks https://<EXTERNAL-IP>  // get response
* curl -ks https://`kubectl get svc frontend -o=jsonpath="{.status.loadBalancer.ingress[0].ip}"`  // Use the output templating feature of kubectl to run curl as a one-line command.

### Scale a Deployment
* kubectl explain deployment.spec.replicas
* kubectl scale deployment hello --replicas=5 // update it
* kubectl get pods | grep hello- | wc -l   // verify there are 5 pods


### Rolling Updates
* Deployments update images to new versions through rolling updates
* kubectl edit deployment hello // to update The editor uses vi commands:
* kubectl get replicaset //see the new ReplicaSet that Kubernetes creates.
* kubectl rollout history deployment/hello // View the new entry in the rollout history
* kubectl rollout pause deployment/hello  // pause it
* kubectl rollout status deployment/hello
* kubectl rollout resume deployment/hello // resume it
* kubectl rollout undo deployment/hello // roll back it

### Canary Deployments
* Run a canary deployment to test a new deployment in production with a subset of users. This mitigates risk with new releases.
* Yaml: It includes the following:
the deployment hello-canary
1 pod (replica)
selectors app: hello and track: canary
an image with version 2.0.0.
* kubectl create -f deployments/hello-canary.yaml //create
* kubectl get deployments /// check
* kubectl delete deployment hello-canary


### Blue-Green Deployments
* it's more beneficial to modify load balancers to point to a new, fully-tested deployment all at once.
* A downside is you need double the resources to host both versions of your application during the switch.


# Lab 4: Deploy a continuous delivery pipeline
* Jenkins: used in Cloud service like AWS ; Spinnkers: more poplular
* This enables you to build, stage, test, and deploy your application, using automated triggers and manual approvals
### Overview
![image](https://github.com/surpasslll/Google-Kubernetes-Engine-Class/blob/master/CICD_pipeline.png)
* Provision a Jenkins environment on a Kubernetes Engine Cluster, using the Helm Package Manager
* Explore the features of a Jenkins application
* Create and exercise Jenkins pipelines
* Deploy an application using development, canary, and production pipelines
* Jenkins:
1. is an open-source automation server that lets you flexibly orchestrate your build, test, and deployment pipelines.
2. allows developers to iterate quickly on projects without worrying about overhead issues that can stem from continuous delivery.
* CI/CD:
1. Continuous Integration (CI) is a development practice that requires developers to integrate code into a shared repository several times a day. Each check-in is then verified by an automated build, allowing teams to detect problems early. 
2. Continuous Delivery is the ability to get changes of all types—including new features, configuration changes, bug fixes and experiments—into production, or into the hands of users, safely and quickly in a sustainable way. 

### Step
* Create a Service Account with permissions
* Create a Kubernetes cluster for Jenkins and your application
* Install Helm //Helm is a package manager that makes it easy to configure and deploy Kubernetes applications. 
* Configure and Install Jenkins
* Connect to Jenkins
* Deploy the Application to Kubernetes:
1. Production: The live site that your users access.
2. Canary: A smaller-capacity site that receives only a percentage of your user traffic. Use this environment to validate your software with live traffic before it's released to all of your users.
* Create a repository to host the sample app source code
* Create the Jenkins Pipeline
* Creating the Development Environment
