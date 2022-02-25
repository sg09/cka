# Security

Questions:
- What type of secrets available

<details><summary>Solution</summary>
<p>

```bash
 kubectl create secret --help
Create a secret using specified subcommand.

Available Commands:
  docker-registry Create a secret for use with a Docker registry
  generic         Create a secret from a local file, directory or literal value
  tls             Create a TLS secret
```
</p>
</details>

Questions:
- Create a secret to access private registry

<details><summary>Solution</summary>
<p>

```bash
kubectl create secret docker-registry reg-cred --docker-server=privateregistry.com:5000 --docker-username=dock_user --docker-password=dock_password --docker-email=dock_user@privateregistry.com
```
</p>
</details>

Questions:
- How to use the above secret to use in pod/deploy

<details><summary>Solution</summary>
<p>

```bash
Under pod spec:-

spec:
      imagePullSecrets:
      - name: reg-cred
      containers:
      - image: privateregistry.com:5000/nginx:alpine
        imagePullPolicy: IfNotPresent
        name: nginx

```
</p>
</details>




Questions:
- How to run the pod sleeper with user ID 1010

<details><summary>Solution</summary>
<p>

```bash
Under pod spec:-
apiVersion: v1
kind: Pod
metadata:
  name: sleeper
spec:
  securityContext:
    runAsUser: 1010
  containers:
    image: ubuntu

```
</p>
</details>



Questions:
- How to add SYS_TIME capability to the pod sleeper with user ID 1010

<details><summary>Solution</summary>
<p>

```bash
apiVersion: v1
kind: Pod
metadata:
  name: ubuntu-sleeper
spec:
  securityContext:
    runAsUser: 1010
  containers:
  - image: ubuntu
    name: ubuntu
    securityContext:
      capabilities:
         add: ["SYS_TIME"]

```
</p>
</details>


Questions:
- Create a network policy from internal pod to payroll and mysql pods

<details><summary>Solution</summary>
<p>

```bash
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: internal-policy
  namespace: default
spec:
  podSelector:
    matchLabels:
      role: abc
  policyTypes:
  - Egress
  egress:
  - to:
    - podSelector:
        matchLabels:
           name: mysql
    ports:
    - protocol: TCP
      port: 3306
  - to:
    - podSelector:
        matchLabels:
           name: payroll
    ports:
    - protocol: TCP
      port: 8080

```
</p>
</details>
