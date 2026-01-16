# GitOps Pipeline with ArgoCD and Full-Stack Observability

A production-style Kubernetes deployment pipeline where infrastructure changes flow from Git commits to running workloads automatically, with integrated monitoring to observe the entire system in real time.

## Overview

Organisations adopting Kubernetes face two interconnected challenges: deploying applications reliably and understanding what's happening inside the cluster. This project addresses both by implementing GitOps-driven continuous delivery with ArgoCD alongside a complete observability stack.

The system provisions an EKS cluster using Terraform, then deploys ArgoCD to manage all subsequent workloads declaratively. Two ArgoCD Applications handle the deployment pipeline: one synchronises application manifests from this repository, while the other deploys the kube-prometheus-stack via Helm for metrics collection and visualisation. Both applications use automated sync policies with self-healing enabled, meaning any manual drift is automatically corrected to match the Git source of truth.

This approach demonstrates production-ready thinking: infrastructure as code for repeatability, GitOps for auditability, and observability to validate that deployments behave as expected.

## Architecture

![Cloud Architecture](screenshots/cloud-architecture.png)

Terraform provisions the foundational AWS infrastructure: a VPC spanning two availability zones with public and private subnets, NAT gateway for outbound connectivity, and an EKS cluster with managed node groups running in private subnets.

ArgoCD runs inside the cluster and watches this Git repository. When commits land, ArgoCD detects the change and reconciles the cluster state. The monitoring Application pulls the kube-prometheus-stack chart directly from the Prometheus community Helm repository, deploying Prometheus for metrics scraping and Grafana for dashboards—all exposed via LoadBalancer services for immediate access.

The demo application (nginx) runs with defined resource requests and limits, deployed across three replicas to demonstrate horizontal scaling. Grafana provides pre-configured dashboards for cluster-wide and namespace-level metrics visibility.

## Tech Stack

**Infrastructure**: AWS EKS, VPC (multi-AZ), Terraform  
**GitOps**: ArgoCD with automated sync and self-healing  
**Monitoring**: Prometheus, Grafana (kube-prometheus-stack)  
**Workloads**: Kubernetes Deployments, Services, ConfigMaps  
**Tooling**: Helm, kubectl

## Key Decisions

- **ArgoCD over Flux**: ArgoCD provides a visual dashboard showing sync status and application health, which aids debugging and demonstrates the GitOps workflow clearly to stakeholders unfamiliar with the tooling.

- **Helm chart via ArgoCD Application**: Rather than installing kube-prometheus-stack imperatively, the monitoring stack is defined as an ArgoCD Application. This keeps all cluster state in Git and allows the monitoring configuration to evolve through pull requests.

- **Automated sync with self-healing**: Enabling both `automated` sync and `selfHeal` ensures the cluster continuously converges to the declared state. Manual changes via kubectl are overwritten, enforcing Git as the single source of truth.

- **Single NAT gateway**: For a development environment, a single NAT gateway reduces cost while still enabling private subnet egress. Production would use one per AZ for resilience.

## Screenshots

![ArgoCD dashboard](screenshots/1.png)

![ArgoCD monitoring application](screenshots/2.png)

![ArgoCD webapp application](screenshots/3.png)

![Grafana cluster dashboard](screenshots/4.png)

![Grafana namespace view](screenshots/5.png)

![Deployed application](screenshots/6.png)

![Monitoring pods](screenshots/7.png)

![AWS EKS cluster](screenshots/8.png)

## Author

**Noah Frost**

- Website: [noahfrost.co.uk](https://noahfrost.co.uk)
- GitHub: [github.com/nfroze](https://github.com/nfroze)
- LinkedIn: [linkedin.com/in/nfroze](https://linkedin.com/in/nfroze)