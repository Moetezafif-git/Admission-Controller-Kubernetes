# Admission-Controller-Kubernetes

## Create Your own Cluster 
kind create cluster --name cluster --image kindest/node:v1.20.2

# You need To configure TLS Certificate 
For invoking the this webhook by kubernetes, You need a TLS Certificate
You can use your CA or a self Signed Certificate
in our case we'll be using a Self Signed Certificate

And For this we'll be using **CloudFlare SSL**
