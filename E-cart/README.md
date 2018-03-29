
## Monolith Application Architecture:
![Monolith Application](./Monolith.jpeg?raw=true)

### Monolith E-Cart Application.

- Deploy the E-Cart Monolith application.
```
$ git clone https://github.com/cloudyuga/e-cart.git
$ cd e-cart
$ git checkout monolith
$ kubectl apply -f k8s/.
```

###  Clean the application.

- Delete the E-Cart Monolith application.
```
$ cd e-cart
$ git checkout monolith
$ kubectl delete -f k8s/.
```

## Microservices Application architecture.
![Microservices](./Catalogue.jpeg?raw=true)

### Microservice E-Cart Application. [Ingress must be enabled]
- Deploy the Microservice E-Cart Application.
```
$ git clone https://github.com/cloudyuga/e-cart.git
$ cd e-cart
$ git checkout ninth-prometheus
$ kubectl apply -f k8s/.
$ kubectl apply -f k8s/ingress/.
```

###  Clean the application.

- Delete the Microservice E-Cart Application.
```
$ cd e-cart
$ git checkout ninth-prometheus
$ kubectl delete -f k8s/.
```
