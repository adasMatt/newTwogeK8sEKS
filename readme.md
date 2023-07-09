# twoge with k8s

## prerequisites
Chandra's amazing [Twoge application repo](https://github.com/chandradeoarya/twoge) 

## Small things to do first
- Create Dockerfile, build and then push image to Dockerhub
```
FROM python:alpine

RUN apk update && \
    apk add --no-cache build-base libffi-dev openssl-dev

COPY . /app
WORKDIR /app

RUN pip install -r requirements.txt

EXPOSE 8080
CMD python app.py
```

```
docker build -t "matthawkiit/twoge-520pm" . 
docker push matthawkiit/twoge-520pm 
```

- grab a server
```
minikube start
```
- make twoge-ns & set context to use it (namespace there are images that go with this as well):
``` 
kubemini create namespace twoge-ns
kubemini config set-context --current --namespace=twoge-ns
```
*note that my alias for "`minikube kubectl --`" is different than yours, sry :p

- make [twoge-quota.yml](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/resourcequota.yml)
```
kubemini create -f resourcequota.yml
```

- make [postgresql-secrets.yml](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/postgresql-fake-secrets.yml) & [postgresql-configmap.yml](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/postgresql-configmap.yml)
- create these and add to cluster:
```
kubemini create -n twoge-ns -f postgresql-secrets.yaml      # optional to specify -n twoge-ns here since you are already working within the namespace if you ran the command containing "set-context" above
kubemini create -f postgresql-configmap.yml                 # namespace field is included inside this file
```
*Kind of went back and forth while I was doing this, had a small amount of trouble with postgresql-configmap.yml since inside the file was namespace='default' by default.

## Next: Starting with database setup 
Now that there are the necessary secrets and configmap objects(?) running(?) on the cluster & namespace(?), the deployments and services that depend on them to function properly can be created.

- make [postgres-deploy.yml](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/postgres-deploy.yml) & [postgres-service.yml](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/postgres-service.yml)  
- create them and add to cluster:
```
kubemini apply -f postgres-deploy.yml 
kubemini apply -f postgres-service.yml
```

## An Application: Finally :) 
  
- make [twoge-deploy.yml](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/twoge-dep.yml) & [twoge-service.yml](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/twoge-service.yml)
- create and add to cluster:
```
kubemini create -f twoge-deploy.yaml
kubemini create -f twoge-service.yml
```
-- this should be the point where minikube dashboard should show a deployment running and Twoge application should be accessible somehow (localhost? minikube IP somewhere?).  I keep having some kind of problem, my db deployment is not even working 2nd time around with all new files and in the new namespace

![architecture image](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/images/k8sClusterArchitecture.png "k8s architecture")

## EKS part (untested)

- create cluster
```
eksctl create cluster --region eu-central-1 --node-type t2.small --nodes 1 --nodes-min 1 --nodes-max 1 --name matt-k8s-eks
```
- eks volume, pv, pvc
```
eksctl utils associate-iam-oidc-provider --region=eu-central-1 --cluster=YourClusterNameHere --approve
```
```
eksctl create iamserviceaccount \
  --name ebs-csi-controller-sa \
  --namespace kube-system \
  --cluster matt-k8s-eks \
  --attach-policy-arn arn:aws:iam::aws:policy/service-role/AmazonEBSCSIDriverPolicy \
  --approve \
  --role-only \
  --role-name AmazonEKS_EBS_CSI_DriverRole
```
```
eksctl create addon --name aws-ebs-csi-driver --cluster matt-k8s-eks \
--service-account-role-arn arn:aws:iam::$(aws sts get-caller-identity --query Account \
--output text):role/AmazonEKS_EBS_CSI_DriverRole --force
```

### See also
- [variousStruggles.md](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/.theseAreNotWorkingNoIdeaWhyHowAnnoying/variousStruggles.md) if you want to see a cluster truck and me complaining and displaying some confusion about k8s, minikube, and docker desktop, or my early lack of understanding of those subjects.
- also see a short explanation for the cause of the terminal output of some errors in [kubectlLogsForPod.txt](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/.theseAreNotWorkingNoIdeaWhyHowAnnoying/kubectlLogsForPod.txt) and [sometingDiffAtLeastIGuess.txt](https://github.com/adasMatt/newTwogeK8sEKS/blob/master/.theseAreNotWorkingNoIdeaWhyHowAnnoying/somethingDiffAtLeastIGuess.txt)
