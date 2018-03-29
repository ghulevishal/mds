## Prerequisites
- Create the `Kubernetes` cluster using `Kubeadm`. For DigitalOcean installation please refer the [Guide](/3-KubernetesInstallation/2-DigitalOcean/lab.md)

## Install the ETCD [On Master Node].
- Download the binary and put this binary in the Path.
```
$ wget https://github.com/coreos/etcd/releases/download/v3.3.0-rc.1/etcd-v3.3.0-rc.1-linux-amd64.tar.gz
$ tar -xvzf etcd-v3.3.0-rc.1-linux-amd64.tar.gz
$ mv etcd-v3.3.0-rc.1-linux-amd64/etcd /usr/bin/.
$ mv etcd-v3.3.0-rc.1-linux-amd64/etcdctl /usr/bin/.
```

- Get the list of ETCD member
```
$ export ETCDCTL_API=3
$ etcdctl --endpoints=http://localhost:2379 member list
8e9e05c52164694d: name=default peerURLs=http://localhost:2380 
clientURLs=http://127.0.0.1:2379 isLeader=true
```

- Get the status of ETCD cluster.
```
$ etcdctl --endpoints=http://localhost:2379 cluster-health
member 8e9e05c52164694d is healthy: got healthy result from http://127.0.0.1:2379
cluster is healthy
```

- We can take snapshot of ETCD.
```
$ etcdctl --endpoints=http://localhost:2379 snapshot save snapshotdb
Snapshot saved at snapshotdb
```

- Check the snapshot taken at `/var/lib/etcd/member/snap`.
```
$ ls /var/lib/etcd/member/snap
snapshotdb
```

- We can restore a cluster using snapshot.
```
$ etcdctl --endpoints=http://localhost:2379 snapshot restore snapshotdb
2018-01-03 05:26:54.658969 I | mvcc: restore compact to 1427
2018-01-03 05:26:54.674521 I | etcdserver/membership: added member 8e9e05c52164694d 
[http://localhost:2380] to cluster cdf818194e3a8c32
```

## ETCD Keys.

- List down all the keys.
```
$ ETCDCTL_API=3 etcdctl --endpoints=http://localhost:2379 get / --prefix --keys-only
```

- Lets check for the keys entry for `default` service-account's token name in `default` namespace.
```
$ ETCDCTL_API=3 etcdctl --endpoints=http://localhost:2379 get /registry/serviceaccounts/default/default

/registry/serviceaccounts/default/default

```

- Lets get the `default token`.
```
$ ETCDCTL_API=3 etcdctl --endpoints=http://localhost:2379 get / --prefix --keys-only | grep  /registry/secrets/default/default-

/registry/secrets/default/default-token-jfchn
```
Here `/registry/secrets/default/default-token-jfchn` is my `default-token` lets get the value for this key.

- Get the value for `default-token` in `default` namespace.
```
$ ETCDCTL_API=3 etcdctl --endpoints=http://localhost:2379 get /registry/secrets/default/default-token-jfchn

/registry/secrets/default/default-token-jfchn
k8s
```

## Watching the Keys entry in the ETCD.

- In one terminal of `Master Node`. Enter following command.
```
$ etcdctl --endpoints=http://localhost:2379 watch /registry/events/rsvp --prefix
```

Now Open new terminal of `Master Node` and execute following tasks.

- Create `rsvp` namespace.
```
$ kubectl create ns rsvp
namespace "rsvp" created
```

- Create the RSVP application configuration file as below.
```
$ vi rsvp.yaml

---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: rsvp
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: rsvp
    spec:
      containers:
      - name: rsvp-app
        image: teamcloudyuga/rsvpapp
        livenessProbe:
          httpGet:
            path: /
            port: 5000
          periodSeconds: 30
          timeoutSeconds: 1
          initialDelaySeconds: 50
        env:
        - name: MONGODB_HOST
          value: mongodb
        ports:
        - containerPort: 5000
          name: web-port
---

apiVersion: v1
kind: Service
metadata:
  name: rsvp
  labels:
    app: rsvp
spec:
  type: NodePort
  ports:
  - port: 80
    targetPort: web-port
    protocol: TCP
  selector:
    app: rsvp

---
apiVersion: v1
kind: Service
metadata:
  name: mongodb
  labels:
    app: rsvpdb
spec:
  ports:
  - port: 27017
    protocol: TCP
  selector:
    appdb: rsvpdb
---
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: rsvp-db
spec:
  replicas: 1
  template:
    metadata:
      labels:
        appdb: rsvpdb
    spec:
      containers:
      - name: rsvpd-db
        image: mongo:3.3
        env:
        - name: MONGODB_DATABASE
          value: rsvpdata
        ports:
        - containerPort: 27017
```

- Deploy the RSVP application in `rsvp` namespace.
```
$ kubectl create -f rsvp.yaml -n rsvp
```

Now go the First Terminal where ETCD watch is runnin and you will notice the some entries are aded to the ETCD.
