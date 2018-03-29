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
8e9e05c52164694d: name=default peerURLs=http://localhost:2380 clientURLs=http://127.0.0.1:2379 isLeader=true
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
2018-01-03 05:26:54.674521 I | etcdserver/membership: added member 8e9e05c52164694d [http://localhost:2380] to cluster cdf818194e3a8c32
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
k8s

v1ServiceAccountq
L
defaultdefault"*$87b53627-f2a8-11e7-9ac3-ce4b43efb2bc2����z!
default-token-jfchn"*2:"
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


v1Secret�
�
default-token-jfchndefault"*$87bfaf80-f2a8-11e7-9ac3-ce4b43efb2bc2����b-
"kubernetes.io/service-account.namedefaultbI
!kubernetes.io/service-account.uid$87b53627-f2a8-11e7-9ac3-ce4b43efb2bcz�
ca.crt-----BEGIN CERTIFICATE-----
MIICyDCCAbCgAwIBAgIBADANBgkqhkiG9w0BAQsFADAVMRMwEQYDVQQDEwprdWJl
cm5ldGVzMB4XDTE4MDEwNjA2MTA1NloXDTI4MDEwNDA2MTA1NlowFTETMBEGA1UE
AxMKa3ViZXJuZXRlczCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAMzX
nVCS+pzAIhxS7NaZnFJeuoqAe0avdQh3ZYqlUS4UcFDLaO6xr+LV7GjFypdWCbfj
1t2kDi4WJFCFJd8ScygsXmAhEumHXH9fNJ0AwWlW/zWKRrxB1Vd3ijTCpQMbd84r
Bu7HS2AI7bC+TC8EJfPXuNrbpt24l95kZhW9T1sJ0NmBhjLP9My5Nt4Hx0yKvXzm
Hfm/Gkoghn/FjFf6RNtNYq8YkfGoJWqCsUlAavWVHc/VsNgrcf/D/F29woz+/Kp0
zDbteVh5vQg6VVYLQqLFrWDDpnc9osQPFtf89tluy3E5ZdbZmp6RfCP7cVdW+UM5
Zc+oTJ59YH3EjTIyddcCAwEAAaMjMCEwDgYDVR0PAQH/BAQDAgKkMA8GA1UdEwEB
/wQFMAMBAf8wDQYJKoZIhvcNAQELBQADggEBACUaX3V+XiAgH11u///R5Orded/W
+Z1urye5p4TVg00G9+n++tNmiSmu33Dkt7lESMdEr2UdhYkaZy4BpAsmlKIrlYve
QMILbzdlRPxsCTN5sssd678uW3Y0a9ONdB/csMhs65vFzaRlHTp/9kB6qZYlE6ee
i3JJn6IhAhgKnHxaqvfJDxScE0DuZ2QZy+KmGrusD52xvoeA45vGs8sfhHphoQKy
5d3Fwjf2CM0SaqNeu6dO+5u08nhzqlPDoEN91cdzcrvmwKCU6YR7PWqntSB1Fuxk
qGX3mG4ppr4TTcGInDusJpk6xQj2YMS2dmtQxzZiuPqGME5qiB+HeHt4WP8=
-----END CERTIFICATE-----

	namespacedefault�
token�eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJkZWZhdWx0Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZWNyZXQubmFtZSI6ImRlZmF1bHQtdG9rZW4tamZjaG4iLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGVmYXVsdCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6Ijg3YjUzNjI3LWYyYTgtMTFlNy05YWMzLWNlNGI0M2VmYjJiYyIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDpkZWZhdWx0OmRlZmF1bHQifQ.VhdSkDywrwv8_eIrLru-rgGbG9x99ggFyzB-jptP4LbVVOotVK4KB7YtZLF7NfKZiTMxbtdJPSatjK7Q_Wmfwu08Ysul8zO-xoqpXIbScgTsCBBQpRZB6y1DB4HA6hDpQcQ1MsLWtxna-G1gJFuDtYa0O6Jbe4pWqwrkeA_jG6IYi3Bc1olmlHR9Q4GENpzubJhKwjRqizFNF1GA8ADCx-PHurxea1t7a1jCeyurH3sErYf04qehowwe9CWVx05OWhznAU5q3ZTmmxbuiZQvgg30XCR9TPx_bzRG93w7wr5XYw09mZD-DhaW8v8cdPhEH2SZpSO2jfeD928xjb2_Tw#kubernetes.io/service-account-token"

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
