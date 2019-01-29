# Install etcd cluster
### Deploy etcd operator

See [instructions on how to install/uninstall etcd operator](install_guide.md) .

### Create and destroy an etcd cluster

```bash
$ kubectl create -f svcat-etcd-cluster.yaml -n openshift-operators
```

A 3 member etcd cluster will be created.

```bash
$ kubectl get pods -n openshift-operators
NAME                            READY     STATUS    RESTARTS   AGE
svcat-etcd-cluster-gxkmr9ql7z   1/1       Running   0          1m
svcat-etcd-cluster-m6g62x6mwc   1/1       Running   0          1m
svcat-etcd-cluster-rqk62l46kw   1/1       Running   0          1m
```

See [client service](doc/user/client_service.md) for how to access etcd clusters created by the operator.

Destroy the etcd cluster:

```bash
$ kubectl delete -f svcat-etcd-cluster.yaml -n openshift-operators
```
