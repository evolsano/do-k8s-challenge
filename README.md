# do-k8s-challenge
This is my first official repo in Github and I am accepting the [Digital Ocean Kubernetes Challenge](https://www.digitalocean.com/community/pages/kubernetes-challenge)

## Challenge: Deploy an internal container registry
Kubernetes does not provide an internal container registry but it is often useful to add one. There are many projects which enable you to deploy an internal container registry, such as Harbour or Trow.

## Steps

### Install kubectl and doctl

Install Kubernetes command-line tool [kubectl](https://kubernetes.io/docs/tasks/tools/) and DigitalOcean CLI tool [doctl](https://docs.digitalocean.com/reference/doctl/how-to/install/) in your local machine. 

### Create kubernetes cluster in Digital Ocean (DO) dashboard. 

DO offer clean UI and simplify the process to bring up a kubernetes cluster.

Once the k8s cluster created, you can verify it from DO dashboard, or from CLI.

```
kubectl get nodes
```

>Output
```
NAME                STATUS   ROLES    AGE     VERSION
pool-harbor-u6n58   Ready    <none>   6m52s   v1.21.5
pool-harbor-u6n5c   Ready    <none>   6m55s   v1.21.5
pool-harbor-u6n5u   Ready    <none>   6m48s   v1.21.5
```

### Install Helm

Install Helm in my local machine.
```
brew install helm
```

### Harbor installation
The real challenge start here. I am using Bitnami [Helm chart](https://bitnami.com/stack/harbor/helm) to deploy harbor.

Add Bitnami repo to Helm.
```
helm repo add bitnami https://charts.bitnami.com/bitnami
```
>Output
```
% helm repo add bitnami https://charts.bitnami.com/bitnami
"bitnami" has been added to your repositories
```