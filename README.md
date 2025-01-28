# HELM-Charts

It’s Package Manager for K8s. Which Help’s us to Build and Manage Applications

- Easy Roll Back
- Parameter Format
    - Like  add Liveness Probe, resources Information
- Mutli Environment Deployment Very Easy

deployment.yaml

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
        resources:
          requests:
            memory: "64Mi"
            cpu: "250m"
          limits:
            memory: "128mi"
            cpu: "500m"

```

service.yaml

```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: nginx
  name: nginx
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: nginx
  type: LoadBalancer

```

kubectl create ns yaml-nginx

kubectl apply -f deployment.yaml -n yaml-nginx

kubectl get all -n yaml-nginx

**Helm Chart:**

- Easy to amintain
- Version Control
- Easy to Deploy

helm create my-nginx

- Chart.yaml —> Metadata file information about project
- charts→folder→dependencies information MySqL
- templates→folder→
    - release —> helm install release-name(dev-nginx)
        - helm install my-nginx path
    - Chart
        - 
    - Values

**HelmChart From Scratch:**

- rm -rf charts/
- rm -rf templates/NOTES.txt
- rm -rf templates/_helpers.tpl
- rm deployment.yaml service.yaml serviceaccount.yaml  hpa.yaml  ingress.yaml
- rm -rf tests/

replicaCount : 3

image:

repository:  nginx

tag: latest

pullPolicy: IfNotPresent

Values.yaml

```yaml
replicaCount: 3

image:
  repository: nginx
  tag: latest
  pullPolicy: IfNotPresent

resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"

service:
  type: LoadBalancer
  port: 80
  targetPort: 80
```

kubectl create ns helm-nginx

helm install dev-nginx . -n helm-nginx

kubectl logs dev-nginx-1-deployment-6fc69c57bd-fdr85 -n helm-nginx

***-Roll Back:***

Value.yaml

replica count is 4

```yaml
replicaCount: 4

image:
  repository: nginx
  tag: latest
  pullPolicy: IfNotPresent

resources:
  requests:
    memory: "64Mi"
    cpu: "250m"
  limits:
    memory: "128Mi"
    cpu: "500m"

service:
  type: LoadBalancer
  port: 80
  targetPort: 80
```

helm upgrade dev-nginx-1 . -n helm-nginx

Release "dev-nginx-1" has been upgraded. Happy Helming!
NAME: dev-nginx-1
LAST DEPLOYED: Tue Jan 28 13:46:55 2025
NAMESPACE: helm-nginx
STATUS: deployed
REVISION: 2
TEST SUITE: None

**rollback Command:**

helm rollback dev-nginx-1 1 -n helm-nginx

**Instead of Yaml file we can do in cli:**

helm upgrade dev-nginx-1 .  —set replicaCount=4 -n helm-nginx

**Delete the Helm Deployment:**

helm uninstall dev-nginx-1 -n helm-nginx

**Different Environnments:**

- Create Values.yaml like
    - dev-values.yaml
    
    ```jsx
    replicaCount: 2
    
    image:
      repository: nginx
      tag: latest
      pullPolicy: IfNotPresent
    
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    
    service:
      type: LoadBalancer
      port: 80
      targetPort: 80
    ```
    
    - qa-values.yaml
    
    ```jsx
    replicaCount: 3
    
    image:
      repository: nginx
      tag: latest
      pullPolicy: IfNotPresent
    
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    
    service:
      type: LoadBalancer
      port: 80
      targetPort: 80
    ```
    
    - prod-values.yaml
    
    ```jsx
    replicaCount: 4
    
    image:
      repository: nginx
      tag: latest
      pullPolicy: IfNotPresent
    
    resources:
      requests:
        memory: "64Mi"
        cpu: "250m"
      limits:
        memory: "128Mi"
        cpu: "500m"
    
    service:
      type: LoadBalancer
      port: 80
      targetPort: 80
    ```
    

kubectl create ns dev

→ helm install dev-nginx . -f dev-values.yaml -n dev

→ kubectl get all -n dev

→ helm uninstall dev-nginx-1 -n dev

kubectl create ns prod

→ helm install prod-nginx . -f prod-values.yaml -n prod

→ kubectl get all -n prod

→ helm uninstall prod-nginx-1 -n prod

kubectl create ns qa

→ helm install qa-nginx . -f qa-values.yaml -n qa

→ kubectl get all -n qa

→ helm uninstall qa-nginx-1 -n qa

helm uninstall dev-nginx-1 -n helm-nginx