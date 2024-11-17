# GitOps-ArgoCD-ContinuousDelivery-K8s

=======
#### Commands
Create a AWS EKS Cluster,here we are using awscli to create the cluster.

First create an IAM Role:
=======
Create a AWS EKS Cluster,here we are using eksctl to create the cluster.
Install eksctl on macOS (using Homebrew)
If you're using macOS and have Homebrew installed:
>>>>>>> e50ad2a (Updated README)
```
brew tap weaveworks/tap 
brew install weaveworks/tap/eksctl
```
<<<<<<< HEAD
If the VPC and Subnets already exists fetch those values or create the new ones.

To Create the cluster:
```
aws eks create-cluster --name my-eks-cluster --role-arn arn:aws:iam::<YOUR_ACCOUNT_ID>:role/EKS-Cluster-Role --resources-vpc-config subnetIds=<SUBNET_ID_1>,<SUBNET_ID_2>,securityGroupIds=<SECURITY_GROUP_ID>  --region <YOUR_REGION>
=======
Install eksctl on Linux (using curl)
For Linux distributions, you can download the binary directly using curl:
```
curl -L "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" -o eksctl.tar.gz
tar -xzf eksctl.tar.gz -C /usr/local/bin
rm eksctl.tar.gz
```

Create the cluster:
```
eksctl create cluster --name=eksdemo \
                      --region=eu-west-2 \
                      --zones=eu-west-2a,eu-west-2b \
                      --without-nodegroup
```
To check the Cluster Status:
```
aws eks describe-cluster --name eksdemo --query "cluster.status"
```
Wait until the status changes to ACTIVE.

Create & Associate IAM OIDC Provider for our EKS Cluster
To enable and use AWS IAM roles for Kubernetes service accounts on our EKS cluster, we must create & associate OIDC identity provider.
To do so using eksctl we can use the below command.
```
eksctl utils associate-iam-oidc-provider \
    --region eu-west-2 \
    --cluster eksdemo\
    --approve
```
Create EC2 Keypair
Create a new EC2 Keypair with name as "ekskey"
This keypair we will use it when creating the EKS NodeGroup.
This will help us to login to the EKS Worker Nodes using Terminal.

Create Node Group with additional Add-Ons in Public Subnets
These add-ons will create the respective IAM policies for us automatically within our Node Group role.
It will take 15 to 20 minutes to create the Node Group with public subnet

Create a Node Group:

```
# Create Public Node Group   
eksctl create nodegroup --cluster=eksdemo \
                       --region=eu-west-2 \
                       --name=eks-nodegroup \
                       --node-type=t3.medium \
                       --nodes=2 \
                       --nodes-min=2 \
                       --nodes-max=4 \
                       --node-volume-size=20 \
                       --ssh-access \
                       --ssh-public-key=ekskey \
                       --managed \
                       --asg-access \
                       --external-dns-access \
                       --full-ecr-access \
                       --appmesh-access \
                       --alb-ingress-access
```
List Worker Nodes:
```
# List EKS clusters
eksctl get cluster

# List NodeGroups in a cluster
eksctl get nodegroup --cluster=<clusterName>

# List Nodes in current kubernetes cluster
kubectl get nodes -o wide

# Our kubectl context should be automatically changed to new cluster
kubectl config view --minify
```

To delete the worker nodes and EKS Cluster:
```
eksctl delete cluster --name eksdemo --region eu-west-2

```

Verify Deletion
You can verify that the cluster has been deleted by running:
```
eksctl get cluster --region eu-west-2
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
