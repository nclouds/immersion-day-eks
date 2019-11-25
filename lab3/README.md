
# EKS Immersion Day - Lab 3
### Deploying to EKS via Jenkins

In the session, we will continue to use the Workstation used in the preceeding sessions.

> *Please make sure you have always selected "us-west-2"* as your AWS Region

> *Substitute userX in doumentation or user1 in screenshot with your user id*


----
Step 1 - Add a sample project to your Github repo
----

<br/>
Log into Github amd clone the following repo

> https://github.com/rnzsgh/eks-workshop-sample-api-service-go

     
     
----
Step 2 - Installing and Configuring Jenkins
----

```
sudo yum install -y java-1.8.0-openjdk-devel
java -jar jenkins.war --httpPort=8081 > /tmp/jenkins.out 2>&1 &
```
<br/>
Load the URL using the Public IP

```
http://<Public-IP>:8081
```

<br/>
Follow directions to paste the initial password


<br/>
Click Selected Plugins


<br/>
Set an admin username and password


<br/>
Go to "Manage Jenkins" >> "Manage Plugins"

Go to Available Tab and Search for "Kubernetes"
Pick the "Kubernetes Continuous Deploy". 


Verify the maintainer to make sure you have picked the right one. Maintainer should be "Azure DevOps Team"




----
Step 3 - Setting up a Job in Jenkins
----







<br/><br/><br/>
