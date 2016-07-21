# Blue-Green Deployment
Steps to perform a manual Blue-Green Deployment on Kubernetes

### Prerequisites
- Provisioned Kubernetes Cluster
- kubectl

### Step 1: Create Stable Service

A *service* is also called a *router* or *load balancer*.

This service will target containers tagged with the color "blue" and the type "webserver".

**service.yaml:**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webserver
spec:
  type: LoadBalancer
  ports:
    - port: 80
      targetPort: 80
      protocol: TCP
  selector:
    type: webserver
    color: blue
```

Create service:
- `kubectl apply -f service.yaml`

Get IP address for the service:

- `kubectl get svc webserver`
```
NAME                CLUSTER-IP       EXTERNAL-IP       PORT(S)     AGE
webserver           10.XXX.XXX.XXX   AAA.BBB.CCC.DDD   80/TCP      5m
```

Assume `AAA.BBB.CCC.DDD` is the IP address.

### Step 2: Launch First Version

Launch three containers running the v1 software. This version will be tagged "blue."

**blue.yaml:**

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: webserver-blue
spec:
  replicas: 3
  template:
    metadata:
      labels:
        type: webserver
        color: blue
    spec:
      containers:
      - image: CONTAINER:1.0
        name: webserver-container
        ports:
        - containerPort: 80
          name: http-server
```

Create deployment:
- `kubectl apply -f blue.yaml`

Going to `AAA.BBB.CCC.DDD` we will see the first version serving traffic.

### Step 3: Launch Second Version

Launch three containers running the v2 software. This version will be tagged "green."

**green.yaml:**

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: webserver-green
spec:
  replicas: 3
  template:
    metadata:
      labels:
        type: webserver
        color: green
    spec:
      containers:
      - image: CONTAINER:2.0
        name: webserver-container
        ports:
        - containerPort: 80
          name: http-server
```

Create deployment:
- `kubectl apply -f green.yaml`

### Step 4: Switch from Blue to Green

Edit the Service so it sends traffic to the Green deployment.
   - `kubectl edit svc webserver`

This will open the YAML in a text editor. Change "blue" to "green" and save.

Going to `AAA.BBB.CCC.DDD` we will see the second version serving traffic.

### Step 5: Launch Third Version

Update the "blue" deployment to version 3.

- `kubectl set image deployment/webserver-blue webserver-container=CONTAINER:3.0`

### Step 6: Switch from Green to Blue

Edit the Service so it sends traffic to the Green deployment.
   - `kubectl edit svc webserver`

This will open the YAML in a text editor. Change "green" to "blue" and save.

Going to `AAA.BBB.CCC.DDD` we will see the third version serving traffic.