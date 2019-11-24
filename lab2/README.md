
# EKS Immersion Day - Lab 2
### (Working with EKS)

In the session, we will be provisiong a Amazon Linux machine on AWS as a stand-in for your local machine. This will act as a local workstation to manage and deploy to EKS. All the AWS CLI commands used here can be executed in your Local machine, after you have 
[configured](https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html) your AWS CLI with proper credentails assoicated with a IAM User.
<br/><br/>
We will also be using this machine as a Jenkins Server in the following lab.


> *Please make sure you have always selected "us-west-2"* as your AWS Region

> *Substitute userX in doumentation or user1 in screenshot with your user id*

   
     
     
----
Step 1 - Provisioning an EC2 Instance on AWS (to act as local Workstation)
----

Log into the AWS Console, Navigate to EC2. Launch Instance

Pick the following AMI (The first AMI you see on the list)

<br/>
<br/>


`Amazon Linux 2 AMI (HVM), SSD Volume Type - ami-0c5204531f799e0c6 (64-bit x86) / ami-03b4aa59bf41f9ed4 (64-bit Arm)`

<br/>
<br/>

Pick the following Instance Type

<br/>
<br/>


`t2.micro`

<br/>
<br/>


In the next page, use the following screenshot to pick your settings.
> ### Make sure you pick "eksworkshop-admin" as the IAM Role
You may leave everything else in it's default choice

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab1/instance_settings.png)

<br/><br/><br/>

Create a name tag with Key "Name" and Value "userX-workstation"

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab1/name.png)


<br/><br/><br/>

Create a new Security Group by Opening the Ports 22, 80, 443 and 8080 to "My IP"

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
   ssh -i <PATH-TO-KEY>/userX_kp.pem ec2-user@<PUBLIC-IP>
```

*For Windows Users*

   You will need to create a private key to use in PuTTY. Please follow the instructions [here](https://linuxacademy.com/guide/17385-use-putty-to-access-ec2-linux-instances-via-ssh-from-windows/)

<br/><br/><br/>

----
Step 3 - Install necessary tools
----

```
sudo yum update -y
sudo yum install -y docker git
sudo curl --silent --location -o /usr/local/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/v1.13.7/bin/linux/amd64/kubectl
sudo chmod +x /usr/local/bin/kubectl
sudo yum -y install jq gettext bash-completion
```
<br/>
Verify all packags are installed
```
for command in kubectl jq envsubst;
   do
      which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND";
   done
```


    7  kubectl completion bash >>  ~/.bash_completion
    8  source /etc/profile.d/bash_completion.sh
    9  cd ~
   10  git clone https://github.com/brentley/ecsdemo-frontend.git
   11  git clone https://github.com/brentley/ecsdemo-nodejs.git
   12  git clone https://github.com/brentley/ecsdemo-crystal.git
  

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

----
Step 5 - Shutdown the EC2 Instance
----

For ease of use and clarity, we will be creating a separate workstation for the next lab. Please shutdown this instance to avoid confusion in the next Lab.


