# Cluster Setup on Compute Engine
Steps to set up a Docker Swarm cluster with Google Compute Engine

Note: This is not the best way to create a Swarm cluster on GCP. When the [setup script](https://github.com/thesandlord/google-cloud-swarm.git) supports Managed Instances Groups, clusters will be more robust and easier to scale.

### Prerequisites
- [Google Cloud Platform account](cloud.google.com/freetrial)
- [gcloud sdk](cloud.google.com/sdk)
- [Docker 1.12](https://www.docker.com/products/overview)
- Docker Machine 0.8.0

### Step 1: Configure Setup Script

`git clone https://github.com/thesandlord/google-cloud-swarm.git`

Specify 3 workers in `options.sh`

`sed -i bak -e s/NUM_WORKERS=2/NUM_WORKERS=3/g options.sh`

### Step 2: Create Cluster

`./swarm-up`

### Step 3: Add Extra Node to the Cluster

Start a new shell to prevent environmnet variable plution:

`sh`

Load environment variables

`source options.sh`

Create Node:
```
docker-machine create $PREFIX-worker-4 \
        --engine-install-url $ENGINE_INSTALL_URL \
        -d google \
        --google-machine-type $WORKER_MACHINE_TYPE \
        --google-zone $WORKER_ZONE \
        --google-disk-size $WORKER_DISK \
        --google-tags $TAGS \
        --google-project $PROJECT_ID
```

Join the Swarm:

`SWARM_MANAGER_INTERNAL_IP=$(gcloud compute instances describe $PREFIX-manager-1 --zone $MANAGER_ZONE --format=text | grep '^networkInterfaces\[[0-9]\+\]\.networkIP:' | sed 's/^.* //g')
`
```
eval $(docker-machine env $PREFIX-worker-4) && \
    docker swarm join $SWARM_MANAGER_INTERNAL_IP:2377 --secret $SWARM_SECRET
```
Exit the shell:

`exit`

### Step 4: Remove Extra Node from the Cluster

`$(source options.sh && docker-machine rm -f $PREFIX-worker-4)`

`$(source options.sh && docker node rm $PREFIX-worker-4)`

##Optional - Drain node before deleting:
This shouldn't be strictly necessary, as swarm will detect a node is deleted and automatically reschedule work.

### Step 4: Get list of Nodes

`docker nodes ls`

| ID                          | NAME                | MEMBERSHIP  | STATUS  | AVAILABILITY  | MANAGER STATUS |
| ---                         | ---                 | ---         | ---     | ---           | ---            |
| 7s21m2bcz5i1ks8dv9hcmfwvk   | my-swarm-worker-1   | Accepted    | Ready   | Active        |                |
| 9xd6pzp2fv5is3a69ng0ms7s5   | my-swarm-worker-2   | Accepted    | Ready   | Active        |                |
| by2uchvhbzx0ybgj11s5y7cto * | my-swarm-manager-1  | Accepted    | Ready   | Active        | Leader         |
| czaeu4mdifnvkv3ld1mh90xek   | my-swarm-worker-4   | Accepted    | Ready   | Active        |                |

### Step 5: Drain a node
I just picked a node at random

`docker node update --availability drain my-swarm-worker-4`

`kubectl get nodes`

| ID                          | NAME                | MEMBERSHIP  | STATUS  | AVAILABILITY  | MANAGER STATUS |
| ---                         | ---                 | ---         | ---     | ---           | ---            |
| 7s21m2bcz5i1ks8dv9hcmfwvk   | my-swarm-worker-1   | Accepted    | Ready   | Active        |                |
| 9xd6pzp2fv5is3a69ng0ms7s5   | my-swarm-worker-2   | Accepted    | Ready   | Active        |                |
| by2uchvhbzx0ybgj11s5y7cto * | my-swarm-manager-1  | Accepted    | Ready   | Active        | Leader         |
| czaeu4mdifnvkv3ld1mh90xek   | my-swarm-worker-4   | Accepted    | Ready   | Drain         |                |

### Step 6: Delete Node and Resize Cluster

`docker-machine rm -f my-swarm-worker-4`

`docker node rm my-swarm-worker-4`