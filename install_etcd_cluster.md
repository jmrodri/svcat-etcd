# Install etcd cluster
### Deploy etcd operator

See [instructions on how to install/uninstall etcd operator](install_guide.md) .

### Create and destroy an etcd cluster

```bash
$ kubectl create -f svcat-etcd-cluster.yaml
```

A 3 member etcd cluster will be created.

```bash
$ kubectl get pods
NAME                            READY     STATUS    RESTARTS   AGE
svcat-etcd-cluster-gxkmr9ql7z   1/1       Running   0          1m
svcat-etcd-cluster-m6g62x6mwc   1/1       Running   0          1m
svcat-etcd-cluster-rqk62l46kw   1/1       Running   0          1m
```

See [client service](doc/user/client_service.md) for how to access etcd clusters created by the operator.

If you are working with [minikube locally](https://github.com/kubernetes/minikube#minikube), create a nodePort service and test that etcd is responding:

```bash
$ kubectl create -f svcat-etcd-cluster-nodeport-service.json
$ export ETCDCTL_API=3
$ export ETCDCTL_ENDPOINTS=$(minikube service svcat-etcd-cluster-client-service --url)
$ etcdctl put foo bar
```

Destroy the etcd cluster:

```bash
$ kubectl delete -f example/example-etcd-cluster.yaml
```
