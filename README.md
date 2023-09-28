1.kubectl get namespaces

```
NAME          STATUS    AGE
default       Active    5m
kube-system   Active    5m
kube-public   Active    5m
```

2.kube-logging.yaml 

3.kubectl create -f kube-logging.yaml
```
namespace/kube-logging created
```
4.kubectl get namespaces
```
NAME           STATUS    AGE
default        Active    23m
kube-logging   Active    1m
kube-public    Active    23m
kube-system    Active    23m
```
5.elasticsearch_svc.yaml

6.kubectl create -f elasticsearch_svc.yaml
```
service/elasticsearch created
```
7.kubectl get services --namespace=kube-logging
```
NAME            TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)             AGE
elasticsearch   ClusterIP   None         <none>        9200/TCP,9300/TCP   26s
```
8.elasticsearch_statefulset.yaml

9.kubectl create -f elasticsearch_statefulset.yaml
```
statefulset.apps/es-cluster created
```
10.kubectl rollout status sts/es-cluster --namespace=kube-logging
```
Waiting for 3 pods to be ready...
Waiting for 3 pods to be ready...
Waiting for 3 pods to be ready...
Waiting for 3 pods to be ready...
Waiting for 3 pods to be ready...
Waiting for 3 pods to be ready...
Waiting for 3 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 2 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...
Waiting for 1 pods to be ready...

partitioned roll out complete: 3 new pods have been updated...
```
11.kubectl port-forward es-cluster-0 9200:9200 --namespace=kube-logging
```
 {
  "cluster_name" : "k8s-logs",
  "compressed_size_in_bytes" : 348,
  "cluster_uuid" : "QD06dK7CQgids-GQZooNVw",
  "version" : 3,
  "state_uuid" : "mjNIWXAzQVuxNNOQ7xR-qg",
  "master_node" : "IdM5B7cUQWqFgIHXBp0JDg",
  "blocks" : { },
  "nodes" : {
    "u7DoTpMmSCixOoictzHItA" : {
      "name" : "es-cluster-1",
      "ephemeral_id" : "ZlBflnXKRMC4RvEACHIVdg",
      "transport_address" : "10.244.8.2:9300",
      "attributes" : { }
    },
    "IdM5B7cUQWqFgIHXBp0JDg" : {
      "name" : "es-cluster-0",
      "ephemeral_id" : "JTk1FDdFQuWbSFAtBxdxAQ",
      "transport_address" : "10.244.44.3:9300",
      "attributes" : { }
    },
```
12.kibana.yaml

13.kubectl create -f kibana.yaml
```
service/kibana created
deployment.apps/kibana created
```
14. kubectl rollout status deployment/kibana --namespace=kube-logging
```
deployment "kibana" successfully rolled out
```
15.kubectl get pods --namespace=kube-logging
```
NAME                      READY     STATUS    RESTARTS   AGE
es-cluster-0              1/1       Running   0          60m
es-cluster-1              1/1       Running   0          59m
es-cluster-2              1/1       Running   0          58m
kibana-6c9fb4b5b7-plbg2   1/1       Running   0          5m54s
```
16.kubectl port-forward kibana-6c9fb4b5b7-plbg2 5601:5601 --namespace=kube-logging
```
Forwarding from 127.0.0.1:5601 -> 5601
Forwarding from [::1]:5601 -> 5601
```
17.http://localhost:5601

18.fluentd.yaml

19.kubectl create -f fluentd.yaml
```
serviceaccount/fluentd created
clusterrole.rbac.authorization.k8s.io/fluentd created
clusterrolebinding.rbac.authorization.k8s.io/fluentd created
daemonset.extensions/fluentd created
```
20.kubectl get ds --namespace=kube-logging
```
NAME      DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE SELECTOR   AGE
fluentd   3         3         3         3            3           <none>          59s
```
21.counter.yaml

22.kubectl create -f counter.yaml
