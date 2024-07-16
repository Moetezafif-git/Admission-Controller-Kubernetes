# Admission-Controller-Kubernetes

## Create Your own Cluster 
kind create cluster --name tutorial --image kindest/node:v1.20.2

## You need To configure TLS Certificate 
For invoking the this webhook by kubernetes, You need a TLS Certificate
You can use your CA or a self Signed Certificate
in our case we'll be using a Self Signed Certificate

And For this we'll be using **CloudFlare SSL**

excute this commands : 

$ cd Admission-Controller-Kubernetes/ 
#
$ docker run -it --rm -v ${PWD}:/work -w /work debian bash
#
$ apt-get update && apt-get install -y curl &&
 curl -L https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssl_1.5.0_linux_amd64 -o /usr/local/bin/cfssl && \
 curl -L https://github.com/cloudflare/cfssl/releases/download/v1.5.0/cfssljson_1.5.0_linux_amd64 -o /usr/local/bin/cfssljson && \
 chmod +x /usr/local/bin/cfssl && \
 chmod +x /usr/local/bin/cfssljson

### we'll generate our ROOT CA 
In the tls folder : there'll be CA File that will define how our ROOT CA will look like 
and another file that will define the configuration like the expiration date and the profile

Generate the ROOT CA using this command 

$ cfssl gencert -initca ./tls/ca-csr.json | cfssljson -bare /tmp/ca

Now Generate the certificate that will be used for our service and sign it with our ROOT CA
$ cfssl gencert \
  -ca=/tmp/ca.pem \
  -ca-key=/tmp/ca-key.pem \
  -config=./tls/ca-config.json \
  -hostname="example-webhook,example-webhook.default.svc.cluster.local,example-webhook.default.svc,localhost,127.0.0.1" \
  -profile=default \
  ./tls/ca-csr.json | cfssljson -bare /tmp/example-webhook


#save it as a kubernetes secret
$ cat <<EOF > ./tls/example-webhook-tls.yaml
apiVersion: v1
kind: Secret
metadata:
  name: example-webhook-tls
type: Opaque
data:
  tls.crt: $(cat /tmp/example-webhook.pem | base64 | tr -d '\n')
  tls.key: $(cat /tmp/example-webhook-key.pem | base64 | tr -d '\n') 
EOF

#generate CA Bundle + inject into template
ca_pem_b64="$(openssl base64 -A <"/tmp/ca.pem")"


