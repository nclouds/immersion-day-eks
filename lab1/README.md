
# EKS Immersion Day - Lab 1
### (Working with Kubernetes and Containers Locally)

In the session, we will be provisiong a Ubuntu machine on AWS as a stand-in for your local machine. We will be using minikube as your Kubernetes Cluster. All the kubectl and minkube command you use here can be used on the local minikube installation in our Mac/Windows Desktop


> *Please make sure you have always selected "us-west-2"* as your AWS Region

> *Substitute userX in doumentation or user1 in screenshot with your user id*

----
Step 1 - Provisioning on AWS (to act as local environment)
----


Navigate to EC2

Launch Instance

Pick the following AMI

Amazon Linux 2 AMI (HVM), SSD Volume Type - ami-0a85857bfc5345c38 (64-bit x86) / ami-0c155d582e592bec0 (64-bit Arm)



Pick the following Instance Type

t2.micro



image

Leave everything else as is

Create a name tag "userX-minikube"

Create a new Security Group by Opening the Port 22 to "My IP"

Create a new Key Pair "userX_kp", click download

For Mac Users

Prepare the private key

chmod 600 <PATH-TO-KEY>/userX_kp.pem 

After the instance is ready

ssh -i <PATH-TO-KEY>/userX_kp.pem ec2-user@<PUBLIC-IP>

For Windows Users

Connecting via SSH for Windows Users
https://linuxacademy.com/guide/17385-use-putty-to-access-ec2-linux-instances-via-ssh-from-windows/


sudo apt-get update 
sudo apt-get install docker.io -y

curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x ./kubectl && sudo mv kubectl /usr/local/bin/

curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/


sudo minikube start --extra-config=kubeadm.ignore-preflight-errors=NumCPU --force --cpus 2 --vm-driver=none

sudo chown -R ubuntu:ubuntu /home/ubuntu/.kube
sudo chown -R ubuntu:ubuntu /home/ubuntu/.minikube

Make sure minikube has installed a Kubernetes configuration file at ~/.kube/config

minikube status
kubectl get nodes
kubectl get all

git clone https://github.com/brentley/ecsdemo-nodejs.git

Checkout the contents of the sample application

cd ecsdemo-nodejs
cat kubernetes/deployment.yaml 
cat Dockerfile


kubectl apply -f kubernetes/deployment.yaml


kubectl get all
kubectl get pod <pod-name>
kubectl describe pod  <pod-name>
kubectl logs pod/<pod-name> | less


kubectl expose deployment ecsdemo-nodejs --type=NodePort 
kubectl get service ecsdemo-nodejs

Get the port number

wget http://localhost:<port-number>/


You may open the Security Group of this instance and open the port-number
to access the app via your local browser


