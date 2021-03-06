# Lab 02 - Pod Introduction

## Show Namespace

```
kubectl get namespaces
```

Output

```
NAME              STATUS   AGE
default           Active   3m
kube-node-lease   Active   3m
kube-public       Active   3m
kube-system       Active   3m
```

__In short__

```
kubectl get ns
```

## Show Pods

```
kubectl get pods
```

__In short__

```
kubectl get po
```

__Show all pods__

```
kubectl get pods --all-namespaces
```

Output

```
NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
kube-system   coredns-6955765f44-7vq44           1/1     Running   1          43h
kube-system   coredns-6955765f44-crx2q           1/1     Running   1          43h
kube-system   etcd-minikube                      1/1     Running   0          3m51s
kube-system   kube-apiserver-minikube            1/1     Running   0          3m51s
kube-system   kube-controller-manager-minikube   1/1     Running   1          43h
kube-system   kube-proxy-x49rj                   1/1     Running   1          43h
kube-system   kube-scheduler-minikube            1/1     Running   1          43h
kube-system   storage-provisioner                1/1     Running   2          43h
```

## Create a Pod with command

```
kubectl run nginx --image=nginx --restart=Never
```

Output

```
pod/nginx created
```

## Generate YAML template with command

```
kubectl run nginx --image=nginx --restart=Never --dry-run -o yaml
```

Output

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx
  name: nginx
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx
    resources: {}
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```


__Deploy with YAML__

```
kubectl run nginx2 --image=nginx --restart=Never --dry-run -o yaml > pod.yaml

kubectl create -f pod.yaml
```

Output

```
pod/nginx2 created
```

## Show Pod Detail

```
kubectl get pods -o wide
```

```
kubectl describe pods
```

```
kubectl describe pod nginx
```

## kubectl exec command

```
kubectl exec nginx -- hostname
```

```
kubectl exec -it nginx -- bash
```

## Proxy to test port

```
kubectl port-forward nginx 8080:80
```

Output

```
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
```

Open browser: http://127.0.0.1:8080/

## Set the resource require/limit

```
kubectl describe pods nginx2
```

__Delete Pod__

```
kubectl delete pods nginx2
```

Edit `pod.yaml`

```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx2
  name: nginx2
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx2
    resources:
      requests:
        cpu: 200m
  dnsPolicy: ClusterFirst
  restartPolicy: Never
status: {}
```

__Deploy with YAML__

```
kubectl create -f pod.yaml
```

```
kubectl describe pod nginx2
```

```
kubectl delete pod nginx2
```

## Set the liveness/readiness

Edit `pod.yaml`
```
apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    run: nginx2
  name: nginx2
spec:
  containers:
  - image: nginx
    imagePullPolicy: IfNotPresent
    name: nginx2
    resources: {}
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 3
      periodSeconds: 3
  dnsPolicy: ClusterFirst
  restartPolicy: OnFailure
status: {}
```

__Apply Change__

```
kubectl apply -f pod.yaml
```

```
kubectl get pods -w
```

__In another terminal__

```
kubectl exec nginx2 -- rm /usr/share/nginx/html/index.html
```

Output

```
NAME      READY     STATUS    RESTARTS   AGE
nginx     1/1       Running   0          7m
nginx2    1/1       Running   0          11s
nginx2    1/1       Running   1          1m
```

## Clear

```
kubectl delete pod nginx
kubectl delete pod nginx2
```
