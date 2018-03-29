- Clone the Repository.
```
$ git clone https://github.com/stefanprodan/k8s-prom-hpa /tmp/hpa
```

- Apply the metrics.
```
$ kubectl apply  -f /tmp/hpa/metrics-server
```

- Install the `jq`.
```
$ sudo apt install jq -y
```

- After couple of minutes the metric-server starts reporting CPU and memory usage for nodes and pods. 
```
$ kubectl get --raw "/apis/metrics.k8s.io/v1beta1/nodes" | jq .
```

- Auto Scaling based on CPU and memory usage. Deploy the the `E-cart` Application.
```
$ kubectl apply -f yamls/hpa/ecart.yaml
```

- Deploy the HPA
```
$ kubectl apply -f yamls/hpa/ecart-HPA.yaml
```

- Get HPA.
```
$ kubectl get hpa
$ kubectl describe hpa
```
