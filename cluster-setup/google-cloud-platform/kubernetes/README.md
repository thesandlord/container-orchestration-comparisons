# Cluster Setup on Container Engine
Steps to set up a Kubernetes cluster with Google Container Engine

### Prerequisites
- [Google Cloud Platform account](cloud.google.com/freetrial)
- [gcloud sdk](cloud.google.com/sdk)
  - optionally, you can use [Cloud Shell](https://cloud.google.com/shell/docs/)
- kubectl

### Step 1: Create Cluster

We will create a cluster called 'mycluster' with three nodes

`gcloud container clusters create mycluster --num-nodes 3`

### Step 2: Authenticate into the Cluster

`gcloud container clusters get-credentials mycluster`

### Step 3: Add Extra Node to the Cluster

`gcloud container clusters resize mycluster --size 4`

### Step 4: Remove Extra Node from the Cluster

`gcloud container clusters resize mycluster --size 3`

##Optional - Drain node before deleting:
This shouldn't be strictly necessary, as kubernetes will detect a node is deleted and automatically reschedule work.

### Step 4: Get list of Nodes

`kubectl get nodes`

| NAME                                      | STATUS  |  AGE |
| ---                                       | ---     | ---  |
| gke-mycluster-default-pool-9b43461b-1yn9  | Ready   |  4m  |
| gke-mycluster-default-pool-9b43461b-c1vd  | Ready   |  4m  |
| gke-mycluster-default-pool-9b43461b-j2c3  | Ready   |  4m  |
| gke-mycluster-default-pool-9b43461b-r5xb  | Ready   |  3m  |

### Step 5: Drain a node
I just picked a node at random

`kubectl drain gke-mycluster-default-pool-9b43461b-c1vd`

`kubectl get nodes`

| NAME                                      | STATUS                     |  AGE |
| ---                                       | ---                        | ---  |
| gke-mycluster-default-pool-9b43461b-1yn9  | Ready                      |  4m  |
| gke-mycluster-default-pool-9b43461b-c1vd  | Ready,SchedulingDisabled   |  4m  |
| gke-mycluster-default-pool-9b43461b-j2c3  | Ready                      |  4m  |
| gke-mycluster-default-pool-9b43461b-r5xb  | Ready                      |  3m  |

### Step 6: Delete Node and Resize Cluster

```
gcloud compute instances delete gke-mycluster-default-pool-9b43461b-c1vd && \
  gcloud container clusters resize mycluster --size 3
```

Be sure to answer 'Y' to the questions. Or you can use this command if you want a fully automated solution:

```
yes y | gcloud compute instances delete gke-mycluster-default-pool-9b43461b-c1vd && \
  yes y | gcloud container clusters resize mycluster --size 3
```