
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
Step 2 - Create a ECR Rep0
----

In AWS Console, navigate to ECR


You will see your repo listed with a URL. You will be using this later


----
Step 3 - Prep the workstation to run Docker commands
----

```
sudo service docker start
sudo usermod -a -G docker ec2-user
```

<br/>

Logout and Login again and make sure you can run Docker commands

```
docker info
```


     
----
Step 4 - Installing and Configuring Jenkins
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
Step 5 - Setting up a Job in Jenkins
----



Add a Build Step "Execute Shell"


Copy paste the following contents. 
> ##### Make sure you subtitute the Repo URL from Step 2 

```
export REPOSITORY_URI="<insert-repo-url>"
export TAG="$(date +%Y-%m-%d.%H.%M.%S).$(echo $GIT_COMMIT | tail -c 5)"
$(aws ecr get-login --no-include-email)
docker build --tag $REPOSITORY_URI:$TAG .
docker push $REPOSITORY_URI:$TAG
sed -i 's@CONTAINER_IMAGE@'"$REPOSITORY_URI:$TAG"'@' hello-k8s.yml

```

Add one more Build Step "Deploy to Kubernetes"

In KubeConfig, click "Add" >> "Jenkins"


Once you finish setting up, a new entry will show up in Kubeconfig. Select that entry.

<br/>
Click "Save"


----
Step 6 - Trigger the Job and check the Deployment
----

Click "Build Now"


<br/><br/><br/>


----
Step 7 - Check the URL using the loadbalancer URL
----

<br/>

----
Step 8 - Make a change in your source code and trigger the build again
----

