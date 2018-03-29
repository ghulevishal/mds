## E-cart Logging with EFK.

### Prequisites
- Helm must be installed and Tiller pod is running in your cluster.

### Elasticsearch, Kibana and FluentD cluster for Logging.
- Deploy the EFK cluster.
```
$ helm install --name efk yamls/efk/.
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

- Access the `Kibana` application at http://logging.cloudyuga.io .

- Access the `E-cart` application at http://ecart.cloudyuga.io .
