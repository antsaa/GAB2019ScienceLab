
Create service principal for cluster to use:
az ad sp create-for-rbac --skip-assignment

Deploy ARM template for AKS cluster using either the button in the repository or CLI

Fetch credentials for your newly created cluster:
az aks get-credentials -g turkulab -n gabakscluster

Install the NGINX ingress controller:
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/master/deploy/provider/cloud-generic.yaml

Apply deployment:
kubectl apply -f lab/manifests/deployment.yaml

Apply service:
kubectl apply -f lab/manifests/service.yaml

Apply ingress controller:
kubectl apply -f lab/manifests/ingress.yaml