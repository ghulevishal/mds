## Monitoring `E-cart` with Prometheus.
### Prerequisites 
  - Helm must be installed and tiller pod is running in cluster.
  - Ingress controller must be installed.

### Prometheus
- Install `Prometheus` with `Helm` chart. We are also going to create the ingress for `Prometheus`.
```
$ helm install  --name prometheus --set rbac.create='true' --set server.service.type='NodePort' --set server.ingress.enabled='true',server.ingress.hosts={prometheus.cloudyuga.io} yamls/prometheus-ecart/ 
```

### E-Cart Microservices Application architecture.
![Microservices](./Catalogue.jpeg?raw=true)

- Lets deploy the E-Cart with Microservices Application architecture.
```
$ git clone https://github.com/cloudyuga/e-cart.git
$ cd e-cart
$ git checkout ninth-prometheus
$ rm k8s/prometheus.yaml
$ rm k8s/all-in-one-ecart.yaml
$ kubectl apply -f k8s/.
$ kubectl apply -f k8s/ingress/.
```
We will Deploy the application and create the Ingress for E-Cart.

- Access the Prometheus application at http://prometheus.cloudyuga.io .

- Access the E-cart application at http://ecart.cloudyuga.io .
