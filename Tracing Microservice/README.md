## Tracing Microservice

### Deploy the `Jaeger` for Tracing microservice. It will create the ingress for `Jaeger.
```
$ kubectl apply -f yamls/jaeger/jaeger-all-in-one-template.yml
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

- Access the `Jaeger` application at http://tracing.cloudyuga.io .

- Access the `E-cart` application at http://ecart.cloudyuga.io .
