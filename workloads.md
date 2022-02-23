# Workloads  

Doc: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/

Questions:
- Create a pod with nginx image

<details><summary>Solution</summary>
<p>

```bash
# Create the namespace first
kubectl run nginx --image=nginx
```
</p>
</details>

Questions:
- Get the images used in a pod

<details><summary>Solution</summary>
<p>

```bash
kubectl describe po pod_name|grep Image
```
</p>
</details>

Questions:
- Create a replicaset with nginx with 2 replicas.

<details><summary>Solution</summary>
<p>

```bash
cat rs.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replicaset-1
spec:
  replicas: 2
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx
        image: nginx

kubectl apply -f rs.yaml
```
</p>
</details>

Questions:
- Scale replicaset to 5 replicas

<details><summary>Solution</summary>
<p>

```yaml

kubectl exec envvar -- env | grep PLANET
PLANET=blue
```

</p>
</details>

Questions:
- Create a configmap named `space` with two values `planet=blue` and `moon=white`.
- Create a pod similar to the previous where you have two environment variables taken from the above configmap and show them in the container.

<details><summary>Solution</summary>
<p>
```bash
kubectl scale rs new-replica-set --replicas=2 --record
```
</p>
</details>

Questions:
- Create a deployment with busybox image having 2 replicas

<details><summary>Solution</summary>
<p>

```bash
cat deploy.yaml
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deployment-1
spec:
  replicas: 2
  selector:
    matchLabels:
      name: busybox
  template:
    metadata:
      labels:
        name: busybox
    spec:
      containers:
      - name: busybox-container
        image: busybox
        command:
        - sh
        - "-c"
        - echo Hello K8S! && sleep 3600
        
kubectl apply -f deploy.yaml        
```


</p>
</details>

Questions:
- Create a  service using imperative way

<details><summary>Solution</summary>
<p>


```bash
kubectl create service nodeport nginx --tcp=80:80 --node-port=30080
or
kubectl expose pod nginx --type=NodePort --port=80 --name=nginx-service

For clusterIP:
kubectl create service clusterip redis --tcp=6379:6379
or
kubectl expose pod redis --port=6379 --name redis-service
```

</p>
</details>
