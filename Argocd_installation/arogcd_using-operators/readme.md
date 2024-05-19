# ArgoCD installion using operators

OperatorHub is a centralized repository of Kubernetes Operators. Operators are software extensions to Kubernetes that use custom resources to manage applications and their components. They follow Kubernetes principles, such as the control loop, to manage a Kubernetes application's lifecycle, including deployment, scaling, backups, and updates.

you can check your operator in https://operatorhub.io/


## Pre-requisites

- Ubuntu OS 
- sudo privileges
- Internet access
- t2.medium instance type or higher
- configured kubeadm or AKS or EKS cluster

## lets start configuring ArgoCD using operators

This command installs OLM version 0.27.0 on your Kubernetes cluster. OLM helps manage the lifecycle of operators, including installation, updates, and dependencies.

```bash
curl -sL https://github.com/operator-framework/operator-lifecycle-manager/releases/download/v0.27.0/install.sh | bash -s v0.27.0
```

This command downloads the Argo CD Operator YAML file from OperatorHub and applies it to your Kubernetes cluster, creating the necessary resources to deploy and manage Argo CD.

```bash
kubectl create -f https://operatorhub.io/install/argocd-operator.yaml
```

To check whether everything is alright then it show some output 

```bash
kubectl get csv -n operators
kubectl get  pods -n operators
```

After the pods run then apply a configuration file to your Kubernetes cluster

```bash
kubectl apply -f argocd-basic.yml
```

now to expose it into browser change `ClusterIP to NodePort` in case of kubeadm and `ClusterIP to LoadBalancer` in case of AKS or EKS

```bash
kubectl edit svc example-argocd-server
```

To check it in browser  `instanceip:port_of_example-argocd-server` in kubeadm cluster and `load balance ip` in aks or eks


Now to get the secret use these command and configure ArgoCD

```bash
kubectl get secret
kubectl edit secret example-argocd-cluster ## username is admin and password is given in the file in encoded form with user admin
echo -n password | base64 -d
```
