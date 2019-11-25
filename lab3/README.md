
# EKS Immersion Day - Lab 3
## Deploying to EKS via Jenkins

In the session, we will continue to use the Workstation used in the preceeding sessions.

> *Please make sure you have always selected "us-west-2"* as your AWS Region

> *Substitute userX in doumentation or user1 in screenshot with your user id*


----
Step 1 - Add a sample project to your Github repo
----

<br/>
Log into Github amd fork the following repo

> https://github.com/rnzsgh/eks-workshop-sample-api-service-go


<br/>

----
Step 2 - Create a ECR Repo
----

In AWS Console, navigate to ECR.

Create a new repsitory. Name it userX_samplego

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/repo_create.png)

You will see your repo listed with a URL. You will be using this later

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/ecr_repo.png)

<br/>

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

<br/>   

----
Step 4 - Installing and Configuring Jenkins
----

```
sudo yum install -y java-1.8.0-openjdk-devel
wget http://mirrors.jenkins.io/war-stable/latest/jenkins.war
java -jar jenkins.war --httpPort=8081 > /tmp/jenkins.out 2>&1 &
```
<br/>
Load the URL using the Public IP

```
http://<Public-IP>:8081
```

<br/>
Follow directions to paste the initial password
The initial password will be in the following file

```
cat /home/ec2-user/.jenkins/secrets/initialAdminPassword
```

<br/><br/>
Click Selected Plugins

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/install_selected.png)

<br/><br/>

Set an admin username and password

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/admin_user.png)


<br/><br/>
Go to "Manage Jenkins" >> "Manage Plugins"

Go to Available Tab and Search for "Kubernetes"
Pick the "Kubernetes Continuous Deploy". 

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/search.png)

<br/><br/>

Verify the maintainer to make sure you have picked the right one. Maintainer should be "Azure DevOps Team"

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/kub_plugin.png)

Click "Install without restart"

<br/>   

----
Step 5 - Setting up a Job in Jenkins
----

Click on "Add Job"

Follow through with these settings

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/config.png)

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/git.png)

We won't be adding a trigger for this build.

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

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/build_stage.png)

Add one more Build Step "Deploy to Kubernetes"


In KubeConfig, click "Add" >> "Jenkins"

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/credentials.png)


Once you finish setting up, a new entry will show up in Kubeconfig. Select that entry.

![inst](https://github.com/nclouds/immersion-day-eks/blob/master/lab3/deploy_stage.png)


<br/>
Click "Save"

<br/>   

----
Step 6 - Trigger the Job and check the Deployment
----

Click "Build Now"


<br/><br/>

----
Step 7 - Check the URL using the loadbalancer URL
----

Find the service URL by the following commands

```
kubectl get service/hello-k8s
```

<br/>

----
Step 8 - Make a change in your source code and trigger the build again
----

