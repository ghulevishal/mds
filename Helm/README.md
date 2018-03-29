# Helm
Helm is a package installer for Kubernetes-based resources. Helm uses `charts`, which are preconfigured with packages of Kubernetes resources. Helm enables you to easily install packages, make revisions, and even roll back complex change.



- Validate your cluster and cluster components using following commands on `Master` node.
```
$ kubectl cluster-info
$ kubectl get nodes
$ kubectl get componentstatuses
```
- Install `Helm` using following commands.
```
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```

- Apply RBAC rule.
```
$ kubectl create -f yamls/helm/rbac-config.yaml
```

- Confirm `Tiller` pod is deployed in kubernetes.
```
$ kubectl --namespace kube-system get pods | grep tiller
```
