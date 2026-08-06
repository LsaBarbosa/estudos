# What is an Orchestrator?

An orchestrator is responsible for automatically managing containers.

---

# Amazon ECS

ECS stands for **Elastic Container Service**.

---

## How it works

You specify:

* which Docker image to run
* how many instances you want
* memory
* CPU
* ports
* networking

ECS does the rest.

```
Docker Image → Task Definition → ECS Service → Running containers
```

---

# Task Definition

In ECS, an application is described by a **Task Definition**.

It defines:

* Docker image
* CPU
* memory
* environment variables
* ports
* volumes
* logs

It is like a container "blueprint" or template.

---

# Service

The Service ensures that the desired number of containers is always running.

For example:

```
Desired: 5 containers

If one fails: `ECS creates another`

```
---

# ECS + Fargate

One of the biggest advantages of ECS.

With **AWS Fargate**, you don't need to manage servers.

```
Application → ECS → Fargate → AWS manages infrastructure
```

- You simply deploy the container.

---

# ECS on EC2

Another option.

```
Application → ECS → EC2
```

##### In this case, you manage:

* operating system | patches | capacity | machine updates

AWS simply runs ECS.

---

# Amazon EKS

EKS stands for **Elastic Kubernetes Service**.

- It provides a ready-to-use Kubernetes environment.

---

# Kubernetes

## Why use Kubernetes?

It offers many features.

| Concept                     | Definition                                                                                                                                  | Related to                               |
| --------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| **Auto Scaling**            | Automatically adjusts the number of replicas (Pods) or cluster nodes based on load, CPU, memory, or custom metrics. | Scalability and availability             |
| **Service Discovery**       | Allows applications to find and communicate with each other using service names, without knowing Pod IPs. | Service-to-service communication         |
| | **Rolling Update**          | Gradually updates Pods to a new application version, reducing or eliminating downtime. | Continuous deployment                    |
| **Rollback**                | Reverts an application to a previous version if the deployment encounters issues. | Failure recovery                         |
| **Secrets**                 | Stores sensitive information—such as passwords, tokens, and certificates—separately from the application. | Security and credential management       |
| **ConfigMaps**              | Stores non-sensitive configuration data, allowing parameters to be changed without modifying the application image. | Configuration management                 |
| **Ingress**                 | Exposes HTTP/HTTPS services externally, enabling domain- and path-based routing and TLS support. | Traffic ingress                          |
| **Persistent Volumes (PV)** | Provide persistent storage, retaining data even after Pods are recreated. | Data persistence                         |
| **Operators**               | Automate complex application operations—such as installation, updates, backups, and recovery—using custom controllers. | Operational automation                   |
| **Security Policies**       | Define rules to restrict privileges, network access, container execution, and cluster resource usage. | Cluster security                         |
| **Namespaces**              | Logically divide the cluster into isolated environments, facilitating organization, access control, and resource management. | Organization and isolation               |


---

# How does EKS work?

- AWS manages the **Control Plane**. 
* where to run a Pod
* when to recreate Pods
* desired state
* communication between components

`AWS` → API Server | Scheduler | Controller Manager | etcd

- You manage the **Worker Nodes** (or use Fargate). 

- Worker nodes are the machines that run the containers. 

`Worker Node` → `Pod` → `Java Container`


- These nodes can be:

EC2 or Fargate


`Your team` → Applications | Pods | Deployments | Services


---


# ECS vs. Kubernetes

The biggest difference lies in complexity.

### ECS

Simpler.

```
Docker Image → Task → Service → Running
```



---

### Kubernetes

Features a wide range of capabilities.

```
Deployment → ReplicaSet → Pod → Container
```

Also env
...involves:

| Concept          | Definition                                                                                                              | Related to                    |
| ---------------- | ----------------------------------------------------------------------------------------------------------------------- | ----------------------------- |
| **Ingress**      | Exposes HTTP/HTTPS services externally, performing routing by domain or path and potentially providing TLS termination. | Traffic ingress               |
| **Services**     | Provide a stable access point for a set of Pods, performing load balancing and service discovery. | Inter-service communication   |
| **ConfigMaps**   | Store non-sensitive application configurations, decoupling parameters from the container image. | Configuration management      |
| **Secrets**      | Store sensitive information, such as passwords, tokens, and certificates, with appropriate access control. | Security and credentials      |
| **Volumes**      | Make storage available to Pods; can be temporary or persistent, depending on the volume type used. | Data storage                  |
| **Namespaces**   | Create logical partitions within the cluster to organize resources, control access, and apply quotas. | Organization and isolation    |
| **DaemonSets**   | Ensure that a Pod runs on all nodes (or a subset of them) in the cluster. | Infrastructure services       |
| **StatefulSets** | Manage stateful applications, providing stable identity, persistent storage, and ordered startup. | Stateful applications         |
| **Jobs**         | Execute a task until successful completion and terminate the Pods after finishing. | Batch processing              |
| **CronJobs**     | Execute Jobs on a schedule, following a cron expression. | Scheduled tasks               |


It is much more flexible.

---

# AWS Integration

ECS features extremely strong integration.

Example:

| Concept                              | Definition                                                                                                                        | Related to                               |
| ------------------------------------ | --------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------- |
| **IAM**                              | Manages identities, users, groups, roles, and permissions to control access to AWS resources. | Access control and security              |
| **CloudWatch**                       | Collects metrics, logs, and events, enabling monitoring, dashboards, and alerts for resources and applications. | Observability and monitoring             |
| **Secrets Manager**                  | Automatically stores, manages, and rotates credentials, passwords, API keys, and other secrets. | Security and credential management       |
| **ECR (Elastic Container Registry)** | Managed service for storing, versioning, and distributing Docker and OCI images. | Container image registry                 |
| **ALB (Application Load Balancer)**  | Layer 7 (HTTP/HTTPS) load balancer that distributes requests across applications and supports host- and path-based routing. | Load balancing and routing               |
| **Auto Scaling**                     | Automatically adjusts the number of running instances or tasks based on metrics or demand. | Scalability and high availability        |
| **VPC (Virtual Private Cloud)**      | Isolated virtual network within AWS where subnets, routes, gateways, and security rules for resources are defined. | Networking and isolation                 |


Everything works virtually natively.

---


# When to choose ECS?

ECS is often a good choice when:

* the company uses only AWS
* the team lacks Kubernetes experience
* lower operational effort is desired
* rapid delivery is required
* native integration with AWS services is desired
* simplicity is sought

---

# When to choose EKS?

EKS tends to be more suitable when:

* the company already uses Kubernetes
* applications are distributed across multiple clouds
* portability is required
* the team has mastered Kubernetes
* advanced platform features are needed
* an ecosystem based on Helm, Operators, and Kubernetes tools already exists

---

# Summary comparison

| Feature              | ECS                   | EKS                                |
| -------------------- | --------------------- | ---------------------------------- |
| Orchestrator         | AWS proprietary       | Managed Kubernetes                 |
| Complexity           | Low                   | High                               |
| Learning curve       | Shallow               | Steep                              |
| Portability          | Low                   | High                               |
| AWS integration      | Excellent             | Excellent                          |
| Industry standard    | No                    | Yes (Kubernetes)                   |
| Advanced features    | Fewer                 | Many more                          |
| Ideal for            | Simple AWS environments | Complex or multi-cloud environments |

---

# Trade-offs

Neither solution is objectively better.

* **ECS** reduces operational complexity and accelerates adoption when infrastructure is concentrated on AWS.
* **EKS** offers greater flexibility and standardization but requires more operational knowledge and maintenance of the Kubernetes ecosystem.

The choice should consider factors such as team expertise, portability requirements, operational maturity, and the organization's infrastructure strategy.

---

# Interview Summary

> Amazon ECS and Amazon EKS are AWS container orchestration services. ECS is a proprietary solution that is simpler to operate and deeply integrated into the AWS ecosystem, making it ideal for applications running exclusively on the platform. EKS, on the other hand, provides managed Kubernetes, where AWS administers the control plane while the team manages the applications and, typically, the cluster nodes. ECS prioritizes simplicity and reduced operational effort; EKS prioritizes flexibility, Kubernetes standardization, and portability across different environments. The decision should be based on architectural requirements, team expertise, and the company's infrastructure strategy.
