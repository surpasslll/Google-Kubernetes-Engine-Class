# Google-Kubernetes-Engine-Class
Notes of Google Kubernetes Engine Class

# Lab1
## Overview 
Containers are a way of isolating programs or processes from each other. The primary aim of containers is to make programs easy to deploy in a way that doesn't cause them to break.
ref: docker build option: https://docs.docker.com/engine/reference/commandline/build/ 
dockerfile: https://docs.docker.com/engine/reference/builder/#expose


# Clusters, Nodes, and Pods
. Kubernetes adds the ability to define how many machines to run, how many containers to deploy, how to scale them, where persistent disks reside, and how to deploy a group of containers as a unit.
. Kublets itself is the agent of Kubernetes
. A pod is analogous to a VM, sort of, in a group of containers sharing a network and storage that are separated from the nodes themselves. Underneath, you have the OS, you have the hardware, you have the NIC, you have an IP outside, inside you have the pod, and then inside the pod, you have the actual containers, and then the network interfaces for the containers.
. defined a pod in yaml file
. labele is very useful for searching in k8s


# Volume
. volume do not provide sharing between containers or lifecycles management
. 

# Lab2
## Bash Completion (Optional)
source <(kubectl completion bash)
This feature makes using kubectl even easier.

## Use the kubectl get command to view the new service.
kubectl get services
.It may take a few seconds before the ExternalIP field is populated for your service. This is normal—just re-run the kubectl get services command every few seconds until the field is populated.
.Use the external IP address with the curl command to test your demo application.
curl http://<External IP>:80
Kubernetes supports an easy-to-use workflow out of the box using the kubectl run, expose, and scale commands.

## Explore the built-in pod documentation.
kubectl explain pods
kubectl explain pods.xxx.xx could view more info

## List all pods running in the default namespace.
kubectl get pods

## Get more information about the monolith pod.
kubectl describe pods monolith
(Useful for troubleshooting)

## To quit kubectl port-forward and kubectl logs in terminal 2 and 3, press Ctrl+C

## Deployments and Rolling Updates
#   
