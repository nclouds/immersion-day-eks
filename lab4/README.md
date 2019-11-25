
# EKS Immersion Day - Lab 3
## Deploying to EKS via Helm

In the session, we will continue to use the Workstation used in the preceeding sessions.

> *Please make sure you have always selected "us-west-2"* as your AWS Region

> *Substitute userX in doumentation or user1 in screenshot with your user id*


----
Step 1 - Install Helm
----

Install Helm
```
curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
chmod +x get_helm.sh
./get_helm.sh
```

<br/>
Create a new service account manifest

```
cat <<EoF > ~/service-acct.yaml
---
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
EoF

```

Add the account and init Helm

```
kubectl apply -f ~/service-acct.yaml
helm init --service-account tiller
```

----
Step 2 - Install a chart through help
----

<br/>

```
helm repo update
helm repo add bitnami https://charts.bitnami.com/bitnami
helm search nginx
helm install --name mywebserver bitnami/nginx
 
```

Describe the service and get the Load Balancer URL
<br/>

```
kubectl get service  mywebserver-nginx
```


