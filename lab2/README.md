
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
curl "https://s3.amazonaws.com/aws-cli/awscli-bundle.zip" -o "awscli-bundle.zip"
unzip awscli-bundle.zip
sudo ./awscli-bundle/install -i /usr/local/aws -b /usr/bin/aws
sudo curl --silent --location -o /usr/local/bin/kubectl https://storage.googleapis.com/kubernetes-release/release/v1.13.7/bin/linux/amd64/kubectl
sudo chmod +x /usr/local/bin/kubectl
sudo yum -y install jq gettext bash-completion
curl --silent --location "https://github.com/weaveworks/eksctl/releases/download/0.9.0/eksctl_Linux_amd64.tar.gz" | tar xz -C /tmp
sudo mv -v /tmp/eksctl /usr/local/bin

kubectl completion bash >>  ~/.bash_completion
eksctl completion bash >> ~/.bash_completion
source /etc/profile.d/bash_completion.sh

```
<br/>
Configure AWS CLI

```
aws configure
```
Press Enter to select all default options

<br/>
Make sure AWS CLI is working with proper role
```
 aws sts get-caller-identity
```
You should see a role with eksworkshop-admin in it


Verify that aws, kubectl and eksctl are installed
```
aws --version
kubectl version
eksctl version 
```

Verify all kubectl packags are installed
```
for command in kubectl jq envsubst;
   do
      which $command &>/dev/null && echo "$command in path" || echo "$command NOT FOUND";
   done
```
<br/><br/>
----
Step 4 - Create a EKS Cluster using EKS
----
<br/>
```
aws eks get-token --cluster-name user1 --region us-west-2
```
*This step will take close to 10 mins*



<br/><br/><br/>
