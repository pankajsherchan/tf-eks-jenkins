# Agent Context

## Project Intent

This repo is a learning-first custom EKS setup built with Terraform. The user wants to understand EKS in detail, not hide the infrastructure behind a large black-box module too early.

Primary goals:

- Build a reusable EKS baseline that can be adapted quickly for future projects.
- Learn the networking, IAM, Kubernetes, and deployment details step by step.
- First deploy a custom NGINX Docker image.
- Then create and deploy a FastAPI Python app.

## Current Infrastructure

The Terraform files are intentionally numbered to show the build order:

- `0-locals.tf`: environment, project, region, AZs, EKS name, EKS version
- `1-providers.tf`: Terraform and AWS provider setup
- `2-vpc.tf`: VPC
- `3-igw.tf`: Internet Gateway
- `4-subnets.tf`: two private and two public subnets
- `5-nat.tf`: EIP and NAT Gateway
- `6-routes.tf`: public/private route tables and associations
- `7-eks.tf`: EKS cluster IAM role and control plane
- `8-nodes.tf`: node IAM role and managed node group

The cluster currently uses private subnets for the control plane VPC config and node group. Public subnets are tagged for external Kubernetes load balancers, and private subnets are tagged for internal load balancers.

## Working Style

- Keep changes small, explicit, and learning-oriented.
- Prefer plain Terraform resources while the user is learning the fundamentals.
- Avoid introducing a full community EKS module until the manual pieces are understood.
- Add comments only when they clarify EKS, IAM, or networking behavior.
- Preserve the numbered Terraform file layout unless the user asks to reorganize.
- Do not run `terraform apply` or `terraform destroy` unless the user explicitly asks.
- Always call out AWS cost implications when adding resources such as NAT Gateways, load balancers, node groups, or log ingestion.

## Verification

For Terraform changes, run:

```bash
terraform fmt
terraform validate
```

If providers are not initialized, run `terraform init` first when appropriate.

For Kubernetes workload changes, prefer a clear path:

```bash
aws eks update-kubeconfig --region us-east-1 --name dev-my-demo-eks
kubectl get nodes
kubectl get pods -A
```

## Near-Term Roadmap

1. Stabilize the Terraform baseline and add useful outputs.
2. Add ECR repository support for the first NGINX image.
3. Add Kubernetes manifests for NGINX deployment, service, and optional ingress.
4. Add a minimal FastAPI app with Dockerfile.
5. Deploy FastAPI using the same ECR and Kubernetes flow.
6. Add observability, autoscaling, pod IAM, ingress, and upgrade exercises after the first app works.
