# Scheduling 

Questions:
- Taint a node

<details><summary>Solution</summary>
<p>

```bash
kubectl taint node node01 key1=value1:NoSchedule
```
</p>
</details>

Questions:
- Make a node node01 unschedulable

<details><summary>Solution</summary>
<p>

```bash
kubectl cordon  node01 
```
</p>
</details>

Questions:
- Create a pod which tolerate the above taint

<details><summary>Solution</summary>
<p>

```bash
cat pod.yaml
apiVersion: v1
kind: Pod
metadata:
  name: bee
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  tolerations:
  - key: "key1"
    value: "value1"
    effect: "NoSchedule"
kubectl apply -f pod.yaml
```
</p>
</details>

Questions:
- Label node nod01 with key1=label1.

<details><summary>Solution</summary>
<p>

```bash
kubectl label node node01 key1=label1
```
</p>
</details>

Questions:
- Create a deployment to ensure it runs with nodes with label key1=label1

<details><summary>Solution</summary>
<p>

```yaml
apiVersion: v1
items:
- apiVersion: apps/v1
  kind: Deployment
  metadata:
    labels:
      app: key1
    name: blue
  spec:
    replicas: 3
    selector:
      matchLabels:
        app: key1
    strategy:
      rollingUpdate:
        maxSurge: 25%
        maxUnavailable: 25%
      type: RollingUpdate
    template:
      metadata:
        labels:
          app: key1
      spec:
        affinity:
          nodeAffinity:
            requiredDuringSchedulingIgnoredDuringExecution:
              nodeSelectorTerms:
              - matchExpressions:
                - key: key1
                  opeartor: In
                  values:
                  - label1
        containers:
        - image: nginx
          name: nginx
    
kubectl apply -f deploy.yaml
```

</p>
</details>

Questions:
- Create a custom scheduler
<details><summary>Solution</summary>
<p>
```bash
Remember: All control plane components (api-server, etcd, kube-scheduler and controller-manager) available as static pod. Copy the kube-scheduler yaml from /etc/kubernetes/manifests/ and change it
Make sure to change leader-elect, port, scheduler-name and secure-port in command line args are changed

apiVersion: v1
kind: Pod
metadata:
  creationTimestamp: null
  labels:
    component: kube-scheduler1
    tier: control-plane
  name: my-scheduler
  namespace: kube-system
spec:
  containers:
  - command:
    - kube-scheduler
    - --authentication-kubeconfig=/etc/kubernetes/scheduler.conf
    - --authorization-kubeconfig=/etc/kubernetes/scheduler.conf
    - --bind-address=127.0.0.1
    - --kubeconfig=/etc/kubernetes/scheduler.conf
    
    - --leader-elect=false
    - --port=10200
    - --scheduler-name=my-scheduler
    - --sercure-port=0
    
    image: k8s.gcr.io/kube-scheduler:v1.20.0
    imagePullPolicy: IfNotPresent
    livenessProbe:
      failureThreshold: 8
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: 11259
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    name: kube-scheduler
    resources:
      requests:
        cpu: 100m
    startupProbe:
      failureThreshold: 24
      httpGet:
        host: 127.0.0.1
        path: /healthz
        port: 11259
        scheme: HTTPS
      initialDelaySeconds: 10
      periodSeconds: 10
      timeoutSeconds: 15
    volumeMounts:
    - mountPath: /etc/kubernetes/scheduler.conf
      name: kubeconfig
      readOnly: true
  hostNetwork: true
  priorityClassName: system-node-critical
  volumes:
  - hostPath:
      path: /etc/kubernetes/scheduler.conf
      type: FileOrCreate
    name: kubeconfig
status: {}
kubectl apply -f my-scheduler.yaml
```
</p>
</details>

Questions:
- Create a pod and ensure its scheduled by my-scheduler as above

<details><summary>Solution</summary>
<p>

```bash
cat deploy.yaml
---
apiVersion: v1 
kind: Pod 
metadata:
  name: nginx 
spec:
  schedulerName: my-scheduler
  containers:
  - image: nginx
    name: nginx
        
kubectl apply -f deploy.yaml        
```


</p>
</details>


Questions:
- Create a a Docker file with command. Override the values in the pod.

<details><summary>Solution</summary>
<p>

```bash
cat Dockerfile
FROM python:3.6-alpine
RUN pip install flask
COPY . /opt/
EXPOSE 8080
WORKDIR /opt
ENTRYPOINT ["python", "app.py"]
CMD ["--color", "blue"]

cat pod.yaml
--
apiVersion: v1 
kind: Pod 
metadata:
  name: webapp
  labels:
      name: webapp 
spec:
  containers:
  - name: simple-webapp
    image: siva/webapp-color
    command: ["python", "app.py"] --this is equivalent to ENTRYPOINT in Dockerfile
    args: ["--color", "red"]  -- this is equivalent to CMD in Dockerfile
        
kubectl apply -f pod.yaml        
```


</p>
</details>

