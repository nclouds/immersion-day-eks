
* Please make sure you have always selected "us-west-2"
* Substitute userX in doumentation or user2 in screenshot with your user


Lab1: Working with Containers and Docker locally

Creating a Workstation on AWS (to act as local environment)


Navigate to EC2

Launch Instance

Amazon Linux 2 AMI (HVM), SSD Volume Type - ami-0a85857bfc5345c38 (64-bit x86) / ami-0c155d582e592bec0 (64-bit Arm)

Instance Type: t2.micro

See below for other options to pick. Make sure you pick IAM Role as "ecslabworkstationprofile"

image

Leave everything else as is

Create a new Security Group by Opening the Port 22 to "My IP"

Create a new Key Pair "userX_kp", click download

For Mac Users

Prepare the private key

chmod 600 <PATH-TO-KEY>/user2_kp.pem 

After the instance is ready

ssh -i <PATH-TO-KEY>/user2_kp.pem ec2-user@35.166.37.102

For Windows Users

Connecting via SSH for Windows Users
https://linuxacademy.com/guide/17385-use-putty-to-access-ec2-linux-instances-via-ssh-from-windows/

Update to the latest AWS CLI:
$ sudo yum update -y
Install docker:
$ sudo yum install -y docker git
$ sudo service docker start

Add ec2-user to the docker group so you can execute Docker commands without using sudo:
$ sudo usermod -a -G docker ec2-user

Exit and SSH in again to pick up the new permissions.

$ docker info


5. Prepping the Docker images


git clone https://github.com/nclouds/ecs-bootcamp-sample

cd web
docker build -t userX_web .

docker images

docker run -d -p 3000:3000 userX_web

docker ps

curl localhost:3000/web

Open up the Security Group of Workstation, so that you can see the page in your local browser


Open the URL <public-ip>:3000/web


Repeat the same steps for userX_api


Creating a Registry

Navigate to ECR

Create two registries - userX_web and userX_api


Configure AWS CLI

aws configure


Confirm you can login

aws ecr get-login

Log into repo

`aws ecr get-login --region us-west-2 --no-include-email `

Push the docker images to Repo

docker tag userX_web:latest 224820028258.dkr.ecr.us-west-2.amazonaws.com/userX_web:latest
docker push 224820028258.dkr.ecr.us-west-2.amazonaws.com/userX_web:latest
docker tag userX_api:latest 224820028258.dkr.ecr.us-west-2.amazonaws.com/userX_api:latest
docker push 224820028258.dkr.ecr.us-west-2.amazonaws.com/userX_api:latest




