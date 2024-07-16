# Admission-Controller-Kubernetes

## Create Your own Cluster 
kind create cluster --name tutorial --image kindest/node:v1.20.2

## You need To configure TLS Certificate 
For invoking the this webhook by kubernetes, You need a TLS Certificate
You can use your CA or a self Signed Certificate
in our case we'll be using a Self Signed Certificate

And For this we'll be using **CloudFlare SSL**

excute this commands : 
cd Admission-Controller-Kubernetes/
docker run -it --rm -v ${PWD}:/work -w /work debian bash
apt-get update && apt-get install -y curl &&
curl -L https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssl_1.5.0_linux_amd64 -o /usr/local/bin/cfssl && \
curl -L https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssljson_1.5.0_linux_amd64 -o /usr/local/bin/cfssljson && \
chmod +x /usr/local/bin/cfssl && \
chmod +x /usr/local/bin/cfssljson

### we'll generate our ROOT CA 
In the tls folder : there'll be CA File that will define how our ROOT CA will look like 
and another file that will define the configuration like the expiration date and the profile

Generate the ROOT CA using this command 

cfssl gencert -initca ./tls/ca-csr.json | cfssljson -bare /tmp/ca

Now Generate 



