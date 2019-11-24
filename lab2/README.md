
# EKS Immersion Day - Lab 2
### (Working with EKS)

In the session, we will be provisiong a Amazon Linux machine on AWS as a stand-in for your local machine. This will act as a local workstation to manage and deploy to EKS. All the AWS CLI commands used here can be executed in your Local machine, after you have 
![configured](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) your AWS CLI with proper credentails assoicated with a IAM User.
<br/><br/>
We will also be using this machine as a Jenkins Server in the following lab.


> *Please make sure you have always selected "us-west-2"* as your AWS Region

> *Substitute userX in doumentation or user1 in screenshot with your user id*

     
     
----
Step 1 - Provisioning an EC2 Instance on AWS (to act as local workstation)
----

Log into the AWS Console, Navigate to EC2. Launch Instance

Pick the following AMI (The first "Ubuntu" AMI you see on the list)

<br/>
<br/>


`Ubuntu Server 18.04 LTS (HVM), SSD Volume Type - ami-06d51e91cea0dac8d (64-bit x86) / ami-02cbed67225579b2c (64-bit Arm)`

<br/>
<br/>

Pick the following Instance Type

<br/>
<br/>


`t2.micro`

<br/>
<br/>


In the next page, use the following screenshot to pick your settings. You may leave everything else in it's default choice

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab1/instance_settings.png)

<br/><br/><br/>

Create a name tag with Key "Name" and Value "userX-minikube"

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab1/name.png)


<br/><br/><br/>

Create a new Security Group by Opening the Port 22 to "My IP"

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab1/sg.png)

<br/><br/><br/>

Create a new Key Pair "userX_kp", click download

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab1/kp.png)

<br/><br/><br/>

----
Step 2 - Connect to the Instance via SSH
----


For Mac Users

Prepare the private key

chmod 600 <PATH-TO-KEY>/userX_kp.pem 

After the instance is ready

ssh -i <PATH-TO-KEY>/userX_kp.pem ec2-user@<PUBLIC-IP>

For Windows Users

Connecting via SSH for Windows Users
https://linuxacademy.com/guide/17385-use-putty-to-access-ec2-linux-instances-via-ssh-from-windows/

----
Step 3 - Install necessary tools
----


sudo apt-get update 
sudo apt-get install docker.io -y

curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x ./kubectl && sudo mv kubectl /usr/local/bin/

curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/

----
Step 4 - Start Minikube
----

sudo minikube start --extra-config=kubeadm.ignore-preflight-errors=NumCPU --force --cpus 2 --vm-driver=none

sudo chown -R ubuntu:ubuntu /home/ubuntu/.kube
sudo chown -R ubuntu:ubuntu /home/ubuntu/.minikube

Make sure minikube has installed a Kubernetes configuration file at ~/.kube/config

minikube status
kubectl get nodes
kubectl get all


----
Step 4 - Deploy and Test the Sample Application
----



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


