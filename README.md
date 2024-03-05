
> Pre-requisites: EKS cluster running with Karperneter installed


## Install ebs-csi-driver

```bash 
kubectl create secret generic aws-secret --namespace kube-system --from-literal "key_id=${AWS_ACCESS_KEY_ID}" --from-literal "access_key=${AWS_SECRET_ACCESS_KEY}"

helm repo add aws-ebs-csi-driver https://kubernetes-sigs.github.io/aws-ebs-csi-driver
helm repo update

helm upgrade --install aws-ebs-csi-driver --namespace kube-system aws-ebs-csi-driver/aws-ebs-csi-driver
```


## Install KubeDB
```bash
helm upgrade -i kubedb oci://ghcr.io/appscode-charts/kubedb \
    --version v2024.2.14 \
    --namespace kubedb --create-namespace \
    --set-file global.license=/home/arnob/yamls/license/aws.txt \
    --set kubedb-provisioner.imagePullPolicy=Always \
    --set kubedb-webhook-server.imagePullPolicy=Always \
    --set kubedb-provisioner.operator.tag=v0.43.0-petset-0 \
    --set kubedb-ops-manager.operator.tag=v0.30.0-petset.0 \
    --set kubedb-webhook-server.server.tag=v0.19.0-petset.0 \
    --set kubedb-crd-manager.image.tag=v0.0.7-petset.0 \
    --set petset.enabled=true
```

## Create nodePools
Create Ec2NodeClass & NodePools. See `nodepool.sh` for example.

## Apply PlacementPolicies

`kubectl apply -f ./placement.yaml`

## Edit pg-coordinator image
```bash
kubectl patch pgversion <pg-version-name> --type=merge -p '{"spec":{"coordinator":{"image":"ghcr.io/kubedb/pg-coordinator:v0.27.0-petset.0"}}}'
```

## Apply Database

`kubectl apply -f ./postgres.yaml`