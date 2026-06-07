# Custom EKS Terraform Lab

This project is a hands-on EKS learning lab and reusable Terraform baseline. The goal is to understand EKS deeply by building the cluster from first principles, then using the same setup to deploy real workloads on demand for future projects.

The first delivery milestone is to publish and run an NGINX Docker image on EKS. The second milestone is to replace that workload with a FastAPI Python application.

## Current Shape

The stack currently creates the core network and EKS compute layer:

- VPC with DNS support enabled
- Two private subnets across two Availability Zones
- Two public subnets across two Availability Zones
- Internet Gateway for public egress/ingress
- NAT Gateway in a public subnet for private subnet outbound access
- Public and private route tables
- EKS cluster IAM role
- EKS control plane
- Managed node group using EC2 worker nodes in private subnets

The EKS version is controlled in `0-locals.tf` through `local.eks_version`. Before applying changes, confirm the selected version is still supported by Amazon EKS:

- https://docs.aws.amazon.com/eks/latest/userguide/kubernetes-versions.html

## Repository Map

```text
0-locals.tf                 Project, environment, region, AZ, and EKS version settings
1-providers.tf              Terraform and AWS provider configuration
2-vpc.tf                    VPC definition
3-igw.tf                    Internet Gateway
4-subnets.tf                Public and private subnets with Kubernetes load balancer tags
5-nat.tf                    Elastic IP and NAT Gateway
6-routes.tf                 Public/private route tables and subnet associations
7-eks.tf                    EKS cluster IAM role and control plane
8-nodes.tf                  Worker node IAM role and managed node group
docs/                       Learning roadmap and project notes
```

## Learning Goals

This repo should teach the full path from "empty AWS account" to "application running on Kubernetes":

- Terraform state, providers, dependency graph, naming, and module boundaries
- VPC fundamentals: CIDR planning, subnets, route tables, Internet Gateway, NAT Gateway, and DNS
- EKS control plane responsibilities versus worker node responsibilities
- IAM roles and policies for the cluster, nodes, pods, and deployments
- Kubernetes primitives: namespaces, deployments, services, ingress, config, secrets, probes, and scaling
- Container build and registry flow with Docker and Amazon ECR
- Load balancing and traffic routing into the cluster
- Observability, debugging, upgrades, cost controls, and cleanup

## Milestones

### 1. Baseline Cluster

Build a working EKS cluster with managed nodes.

Success means:

- `terraform validate` passes
- `terraform apply` creates the cluster and node group
- `aws eks update-kubeconfig` configures local kubectl access
- `kubectl get nodes` shows ready nodes

### 2. Publish And Deploy NGINX

Use a simple NGINX image as the first workload because it keeps the application layer intentionally boring while the EKS pieces become familiar.

Success means:

- Build an NGINX Docker image locally
- Push it to Amazon ECR
- Deploy it with Kubernetes manifests or Helm
- Expose it through a Service or Ingress
- Verify the endpoint returns the expected NGINX response

### 3. Replace NGINX With FastAPI

Create a minimal FastAPI app and deploy it using the same platform path.

Success means:

- Build a FastAPI Docker image
- Push it to Amazon ECR
- Deploy it to EKS
- Configure health checks
- Expose the API
- Verify an endpoint such as `/health` or `/docs`

### 4. Production-Oriented Iteration

Once the first app works, improve the baseline step by step:

- Move project settings from locals into variables and `*.tfvars`
- Add outputs for cluster name, region, VPC ID, subnet IDs, and kubeconfig command
- Add EKS managed add-ons explicitly
- Add IAM Roles for Service Accounts or EKS Pod Identity
- Add autoscaling with Cluster Autoscaler or Karpenter
- Add logging and metrics
- Add ingress with the AWS Load Balancer Controller
- Split reusable infrastructure into modules only after the concepts are understood

## Common Commands

```bash
terraform fmt
terraform init
terraform validate
terraform plan
terraform apply
aws eks update-kubeconfig --region us-east-1 --name dev-my-demo-eks
kubectl get nodes
```

## Safety Notes

This project creates paid AWS resources, including EKS control plane hours, EC2 instances, NAT Gateway hours, and data processing. Destroy resources when the lab is not in use:

```bash
terraform destroy
```

Do not commit AWS credentials, kubeconfig files, Terraform state files, private keys, or generated secrets.
