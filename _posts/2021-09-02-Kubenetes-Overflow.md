---
layout: post
title: Kubenetes & Argo Workflow Overflow
date: 2021-09-02
categories: [technology]
tags: [life]
comments: false
---



# Kubernetes



### What

Kubernetes is a **portable**, **extensible**, **open-source** platform for managing **containerized** **workloads** and **services**, that facilitates both **declarative configuration** and **automation**. It has a large, rapidly growing **ecosystem**. 



Kubernetes comprises a set of **independent**, **composable** control processes that **continuously** **drive** the current state towards the provided desired state.



**Usage:**

- Provide a runtime environment for Docker containers
- Scale and load balance docker containers
- Abstract away the infrastructure containers run on
- Monitor/health check containers
- Declarative definition for running containers
- Storage mounting
- Service discovery and exposure
- Automatic bin packing
- Labelling and selection of resources
- Self-healing
- Secret and configuration management



**Resource:**

- **Volume**
- Pod
- Deployment
- Namespace
- **Replica SetJobs/Cronjob**
- Config Map
- Secrets
- ClusterRole (RBAC)
- **TFJob(CRD)**
- **Operator**





# Argo Workflows



### What

An open source **container-native workflow** engine for orchestrating parallel jobs on Kubernetes. Argo Workflows is implemented as a Kubernetes CRD. Each task in the workflow is a container. DAG; run on Kubernetes



Combining with CLI, sever & workflow-controller, UI.



### How

Build Docker images & C++ code; write Argo yaml template file; Argo job submit.



Using SimPipe is easier.