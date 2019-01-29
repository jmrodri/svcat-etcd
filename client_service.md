# etcd client service

For every etcd cluster created, the etcd operator will create an etcd client service in the same namespace with the name `<cluster-name>-client`.

```
$ kubectl create -f svcat-etcd-cluster.yaml
$ kubectl get services -n openshift-operators
NAME                        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
svcat-etcd-cluster          None           <none>        2380/TCP   1m
svcat-etcd-cluster-client   10.0.222.115   <none>        2379/TCP   1m
```

The client service is of type `ClusterIP` and accessible only from within the Kubernetes overlay network.

For example, access the service from a pod in the cluster:

```
$ kubectl run --rm -i --tty fun --image quay.io/coreos/etcd --restart=Never -- /bin/sh
/ # ETCDCTL_API=3 etcdctl --endpoints http://svcat-etcd-cluster-client:2379 put foo bar
OK
(ctrl-D to exit)
```

If accessing this service from a different namespace than that of the etcd cluster, use the fully qualified domain name (FQDN) `http://<cluster-name>-client.<cluster-namespace>.svc.cluster.local:2379`.

## Accessing the service from outside the cluster

To access the client API of the etcd cluster from outside the Kubernetes cluster, expose a new client service of type `LoadBalancer`. If using a cloud provider like GKE/GCE or AWS, setting the type to `LoadBalancer` will automatically create the load balancer with a publicly accessible IP.

The spec for this service will use the label selector `etcd_cluster: <cluster-name>` to load balance the client requests over the etcd pods in the cluster.

For example, create a service for the cluster described above:

```
$ kubectl create -f svcat-etcd-client-service-lb.yaml -n openshift-operators
```

Wait until the load balancer is created and the service is assigned an `EXTERNAL-IP`:

```
$ kubectl get services -n openshift-operators
NAME                           CLUSTER-IP     EXTERNAL-IP     PORT(S)          AGE
svcat-etcd-cluster             None           <none>          2380/TCP         5m
svcat-etcd-cluster-client      10.0.222.115   <none>          2379/TCP         5m
svcat-etcd-cluster-client-lb   10.0.176.134   35.184.74.127   2379:32478/TCP   1m
```

The etcd client API should now be accessible from outside the Kubernetes cluster:

```
$ ETCDCTL_API=3 etcdctl --endpoints http://35.184.74.127:2379 get foo
foo
bar
```
