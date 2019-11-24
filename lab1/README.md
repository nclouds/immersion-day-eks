
# EKS Immersion Day - Lab 1
### (Working with Kubernetes and Containers Locally)

In the session, we will be provisiong a Ubuntu machine on AWS as a stand-in for your local machine. We will be using minikube as your Kubernetes Cluster. All the kubectl and minkube command you use here can be used on the local minikube installation in our Mac/Windows Desktop


> *Please make sure you have always selected "us-west-2"* as your AWS Region

> *Substitute userX in doumentation or user1 in screenshot with your user id*

   
     
     
----
Step 1 - Provisioning an EC2 Instance on AWS (to act as local environment)
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

Look up the Public IP address of your EC2 Instance

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab1/public_ip.png)


*For Mac Users*

```
   chmod 600 <PATH-TO-KEY>/userX_kp.pem 
   ssh -i <PATH-TO-KEY>/userX_kp.pem ubuntu@<PUBLIC-IP>
```

*For Windows Users*

   You will need to create a private key to use in PuTTY. Please follow the instructions [here](https://linuxacademy.com/guide/17385-use-putty-to-access-ec2-linux-instances-via-ssh-from-windows/)

<br/><br/><br/>

----
Step 3 - Install necessary tools
----

```
sudo apt-get update 
sudo apt-get install docker.io -y
curl -Lo https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x ./kubectl && sudo mv kubectl /usr/local/bin/
curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
```

<br/><br/><br/>

----
Step 4 - Start Minikube
----

```
sudo minikube start --extra-config=kubeadm.ignore-preflight-errors=NumCPU --force --cpus 2 --vm-driver=none
sudo chown -R ubuntu:ubuntu /home/ubuntu/.kube
sudo chown -R ubuntu:ubuntu /home/ubuntu/.minikube
```

Make sure minikube has installed a Kubernetes configuration file at ~/.kube/config. 
Verify that you can do the following commands

```
minikube status
kubectl get nodes
kubectl get all
```

<br/><br/><br/>

----
Step 4 - Deploy and Test the Sample Application
----

```
cd ~
git clone https://github.com/brentley/ecsdemo-nodejs.git
```

Checkout the contents of the sample application

```
cd ~/ecsdemo-nodejs
cat kubernetes/deployment.yaml 
cat Dockerfile
```

Deploy the application 

```
cd ~/ecsdemo-nodejs
kubectl apply -f kubernetes/deployment.yaml
```

Verify the deployment with these commands

```
kubectl get all
kubectl get pod <pod-name>
kubectl describe pod  <pod-name>
kubectl logs pod/<pod-name> | less
```

Expose this deployment as a service

```
kubectl expose deployment ecsdemo-nodejs --type=NodePort 
kubectl get service ecsdemo-nodejs
```

Get the port number where it's exposed in the localhost. Thest the service

`curl http://localhost:<port-number>/`


You may open the Security Group of this instance and open the port-number
to access the app via your local browser


