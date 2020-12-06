
Cilium documentation: https://docs.cilium.io/en/v1.9/gettingstarted/k8s-install-eks/


### Install eksctl
```
curl -sL "https://github.com/weaveworks/eksctl/releases/download/latest_release/eksctl_$(uname -s)_amd64.tar.gz" | tar -xz -C ~/.local/bin \
    && chmod +x ~/.local/bin/eksctl
```

### Install aws-iam-authenticator
```
curl -sL "https://amazon-eks.s3.us-west-2.amazonaws.com/1.18.9/2020-11-02/bin/linux/amd64/aws-iam-authenticator" -o ~/.local/bin/aws-iam-auhenticator
```

## Deploy EKS cluster control plane
### Execute eksctl command
```
eksctl create cluster -name test-cluster \
    --without-nodegroup \
    --vpc-cidr 192.168.0.0/16
```
### Delete aws-node daemonset
```
kubectl -n kube-system delete daemonset aws-node
```

## Deploy Cilium
### Add Helm repository
```
helm repo add cilium https://helm.cilium.io/
```
### Deploy Cilium chart
```
helm install cilium cilium/cilium --version 1.9.0 \
  --namespace kube-system \
  --set eni=true \
  --set ipam.mode=eni \
  --set egressMasqueradeInterfaces=eth0 \
  --set tunnel=disabled \
  --set nodeinit.enabled=true
```

## Deploy EKS cluster node-group
### Execute eksctl command
```
eksctl create nodegroup --cluster test-cluster --nodes 2
```
