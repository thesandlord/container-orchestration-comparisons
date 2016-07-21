# Container Orchestration Framework Comparisons
This repo contains tutorials on how to perform common task (like deploying a cluster) with various Container Orchestration tools.

PRs welcome!

If your tool of choice isn't listed below -- add it.  

# Supported Frameworks 
| framework  | version |
| --- | --- |
| Kubernetes (k8s) | v.1.3.0 |
| Swarm | v1.12.0-rc2 |

More to come (ie, marthon+mesos, nomad, etc).

# Supported Providers
Google Cloud Platform (GCP)

More to come (ie, Amazon Web Services, Azure, etc).

# Tutorials

## Bringing up clusters

How to set up the different frameworks on various cloud providers.

If there's a hosted version it's represented like so: hosted / non-hosted

|  | Google Cloud Platform |
| --- | --- |
| Kubernetes | [hosted](cluster-setup/google-cloud-platform/kubernetes/README.md) / non-hosted |
| Swarm | [non-hosted](cluster-setup/google-cloud-platform/swarm/README.md) | 

## Common Tasks

|  | Kubernetes |  Swarm   |
| --- | --- | --- |
| [Blue/Green Deployments](blue-green-deployment/) | [link](blue-green-deployment/kubernetes/README.md) | TBD |
| Route traffic to multiple backends with nginx | TBD | TBD |
