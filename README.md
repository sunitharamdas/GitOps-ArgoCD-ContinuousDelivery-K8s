# GitOps-ArgoCD-ContinuousDelivery-K8s

=======
#### Commands
Create a AWS EKS Cluster,here we are using awscli to create the cluster.

First create an IAM Role:
```
aws iam create-role --role-name EKS-Cluster-Role --assume-role-policy-document file://trust-policy.json
```
If the VPC and Subnets already exists fetch those values or create the new ones.

To Create the cluster:
```
aws eks create-cluster --name my-eks-cluster --role-arn arn:aws:iam::<YOUR_ACCOUNT_ID>:role/EKS-Cluster-Role --resources-vpc-config subnetIds=<SUBNET_ID_1>,<SUBNET_ID_2>,securityGroupIds=<SECURITY_GROUP_ID>  --region <YOUR_REGION>
```
To check the Cluster Status:
```
aws eks describe-cluster --name my-eks-cluster --query "cluster.status"
```

```bash
# install ArgoCD in k8s
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# access ArgoCD UI
kubectl get svc -n argocd
kubectl port-forward svc/argocd-server 8080:443 -n argocd

# login with admin user and below token (as in documentation):
kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 --decode && echo

# you can change and delete init password

```
</br>

#### Links

* Config repo: [https://gitlab.com/nanuchi/argocd-app-config](https://gitlab.com/nanuchi/argocd-app-config)

* Docker repo: [https://hub.docker.com/repository/docker/nanajanashia/argocd-app](https://hub.docker.com/repository/docker/nanajanashia/argocd-app)

* Install ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd](https://argo-cd.readthedocs.io/en/stable/getting_started/#1-install-argo-cd)

* Login to ArgoCD: [https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli](https://argo-cd.readthedocs.io/en/stable/getting_started/#4-login-using-the-cli)

* ArgoCD Configuration: [https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/](https://argo-cd.readthedocs.io/en/stable/operator-manual/declarative-setup/)
