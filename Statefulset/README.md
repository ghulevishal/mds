## Statefulset 
### Prerequisites
- Rook must be installed and default storage class must be set 

### Create Statefulset.

- Check the Storage Class.
```
$ kubectl get sc
```

- Deploy the `MYSQL` StatefuSets. Create Configmap, service and statefulset.
```
$ kubectl apply -f yamls/stateful/configmap.yaml
$ kubectl apply -f yamls/stateful/service.yaml
$ kubectl apply -f yamls/stateful/mysql-statefulset.yaml
```
