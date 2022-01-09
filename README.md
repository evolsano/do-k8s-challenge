# do-k8s-challenge
This is my first official repo in Github and I am accepting the [Digital Ocean Kubernetes Challenge](https://www.digitalocean.com/community/pages/kubernetes-challenge)

## Challenge: Deploy an internal container registry
Kubernetes does not provide an internal container registry but it is often useful to add one. There are many projects which enable you to deploy an internal container registry, such as Harbour or Trow.

## Steps

### Install kubectl and doctl

Install Kubernetes command-line tool [kubectl](https://kubernetes.io/docs/tasks/tools/) and DigitalOcean CLI tool [doctl](https://docs.digitalocean.com/reference/doctl/how-to/install/) in your local machine. 

### Create kubernetes cluster in Digital Ocean (DO) dashboard. 

DO offer clean UI and simplify the process to bring up a kubernetes cluster. Login to DO dashboard, and start your kubernetes journey.

<img width="1032" alt="Screenshot 2022-01-09 at 10 45 15 AM" src="https://user-images.githubusercontent.com/8122147/148667302-2872373a-5460-4d91-ac69-65725ceb7adf.png">

<img width="1119" alt="Screenshot 2022-01-09 at 10 45 35 AM" src="https://user-images.githubusercontent.com/8122147/148667305-926813b6-465f-4212-8f48-e78e67d24b09.png">

<img width="691" alt="Screenshot 2022-01-09 at 10 45 53 AM" src="https://user-images.githubusercontent.com/8122147/148667310-2cc61cfc-fa39-46cd-a299-3a8fd396d45d.png">

Copy and paste the command from into your terminal to connect to your kubernetes cluster.

<img width="1117" alt="Screenshot 2022-01-09 at 10 47 58 AM" src="https://user-images.githubusercontent.com/8122147/148667800-a772cb91-2b1d-4448-9b71-85ddc61d9a5e.png">

Once the k8s cluster created, you can verify it from DO dashboard, or from terminal.

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

Create and edit harbor yaml file.
```
helm show values bitnami/harbor > harbor-values.yaml
```

If you own a domain name, edit the externalURL and commonName in harbor-values.yaml to your designated subdomain for harbor.
```
externalURL: https://core.harbor.domain
commonName: 'core.harbor.domain'
```

Install harbor with helm
```
helm install harbor -f harbor-values.yaml bitnami/harbor -n harbor --create-namespace
```
>Output
```
NAME: harbor
LAST DEPLOYED: Sun Dec 26 11:05:15 2021
NAMESPACE: harbor
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
CHART NAME: harbor
CHART VERSION: 11.1.6
APP VERSION: 2.4.1

** Please be patient while the chart is being deployed **

1. Get the Harbor URL:

  NOTE: It may take a few minutes for the LoadBalancer IP to be available.
        Watch the status with: 'kubectl get svc --namespace harbor -w harbor'
  export SERVICE_IP=$(kubectl get svc --namespace harbor harbor --template "{{ range (index .status.loadBalancer.ingress 0) }}{{.}}{{ end }}")
  echo "Harbor URL: http://$SERVICE_IP/"

2. Login with the following credentials to see your Harbor application

  echo Username: "admin"
  echo Password: $(kubectl get secret --namespace harbor harbor-core-envvars -o jsonpath="{.data.HARBOR_ADMIN_PASSWORD}" | base64 --decode)
```

Get harbor pods deployment and ensure all pods running.
```
kubectl get pods -n harbor
```
>Output
```
NAME                                    READY   STATUS    RESTARTS   AGE
harbor-chartmuseum-6d565c4965-v57xt     1/1     Running   0          2m29s
harbor-core-75589849fc-xt6pr            1/1     Running   0          2m29s
harbor-jobservice-6644498d77-gphxg      1/1     Running   0          2m29s
harbor-nginx-685c86566c-8926k           1/1     Running   0          2m30s
harbor-notary-server-58cd7bcbd7-997v4   1/1     Running   0          2m30s
harbor-notary-signer-68f895db7c-xmvtb   1/1     Running   0          2m29s
harbor-portal-86bb64ff84-fhjcd          1/1     Running   0          2m29s
harbor-postgresql-0                     1/1     Running   0          2m29s
harbor-redis-master-0                   1/1     Running   0          2m29s
harbor-registry-d9bb7984-7n2m7          2/2     Running   0          2m29s
harbor-trivy-0                          1/1     Running   0          2m29s
```

### harbor external IP
Get the external IP with this command,
```
kubectl get svc -n harbor -w harbor
```
>Output
```
NAME    TYPE           CLUSTER-IP       EXTERNAL-IP       PORT(S)                                     AGE
harbor   LoadBalancer   10.245.100.223   144.126.241.233   80:31786/TCP,443:30983/TCP,4443:30188/TCP   41m
```

Create A record in DNS with the EXTERNAL-IP.

### SSL/TLS Certificate
I am using Cloudflare to provision a public signed SSL certificate and proxied to harbor.
Since I am using LoadBalancer mode to deploy my harbor, it will redirect http to https. Remember to set the SL/TLS encryption mode to Full.

### Access to harbor.

<img width="1440" alt="Screenshot 2021-12-27 at 4 22 36 PM" src="https://user-images.githubusercontent.com/8122147/147624925-5f5f6f31-e36c-428f-a507-c840500f4d42.png">

<img width="1440" alt="Screenshot 2021-12-27 at 4 23 23 PM" src="https://user-images.githubusercontent.com/8122147/147625008-15a9878d-98a4-4a9f-8693-a004990a5cde.png">

## Final thought
I would like to thank you Digital Ocean again by offering such a great challenge and I can finally get my hands into Kubernetes. Digital Ocean offer a very convenient way for those who want to setup any Kubernetes cluster environment in minutes! 
