# Installation guide

## Setu up RBAC
Setup basic [RBAC rules]([https://github.com/coreos/etcd-operator/blob/master/doc/user/rbac.md) for etcd operator:

```
$ rbac/create_role.sh --namespace=openshift-operators
```

## Install etcd operator
Create a deployment for etcd operator:
```
$ kubectl create -f deployment.yaml -n openshift-operators
```

etcd operator will automatically create a Kubernetes Custom Resource Definition (CRD):

```bash
$ kubectl get customresourcedefinitions -n openshift-operators | grep etcd
NAME                                    KIND
etcdclusters.etcd.database.coreos.com   CustomResourceDefinition.v1beta1.apiextensions.k8s.io
```

## Uninstall etcd operator

Note that the etcd clusters managed by etcd operator will **NOT** be deleted even if the operator is uninstalled.

This is an intentional design to prevent accidental operator failure from killing all the etcd clusters.

To delete all clusters, delete all cluster CR objects before uninstalling the operator.

Clean up etcd operator:

```bash
kubectl delete -f deployment.yaml -n openshift-operators
kubectl delete endpoints etcd-operator -n openshift-operators
kubectl delete crd etcdclusters.etcd.database.coreos.com
kubectl delete clusterrole etcd-operator
kubectl delete clusterrolebinding etcd-operator
```
