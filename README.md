# container-orchestration-comparisons
This repo contains tutorials on how to perform common task (like deploying a cluster) with various Container Orchestration tools.

PRs welcome!

If your tool of choice isn't listed below -- add it.  

# Supported Frameworks 

Kubernetes v.1.3.0 aka k8s
Swarm v1.12.0-rc2

# Supported Providers
Google Cloud Platform aka GCP

More to come (ie, marthon+mesos, nomad, etc).

# Tutorials

## Bringing up clusters

How to set up the different frameworks on various cloud providers.

If there's a hosted version it's represented via a '/' like so: hosted / non-hosted

|  | GCP |
| --- | --- |
| Kubernetes | hosted / non-hosted |
| Swarm | TBD | 

## Common Tasks

|  | k8s |  Swarm   |
| --- | --- | --- |
| Blue/Green Deployments | [link](blue-green-deployment/kubernetes/README.md) | TBD |
| Route traffic to multiple backends with nginx | TBD | TBD |
