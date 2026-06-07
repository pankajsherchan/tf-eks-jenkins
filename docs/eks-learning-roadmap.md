# EKS Learning Roadmap

This roadmap keeps the project practical: each phase should produce something visible while teaching one layer of EKS.

## Phase 0: Terraform And AWS Grounding

Objective: understand what Terraform will create and how AWS charges for it.

Learn:

- Terraform init, plan, apply, destroy, state, dependencies, and provider versions
- AWS regions, Availability Zones, tags, IAM identity, and credentials
- Cost sources: EKS cluster hours, EC2 nodes, NAT Gateway, load balancers, EBS volumes, logs

Checkpoint:

- `terraform plan` is readable and every resource has a purpose.

## Phase 1: Network Foundation

Objective: understand why EKS needs a real VPC design.

Learn:

- VPC CIDR ranges
- Public versus private subnets
- Route tables and route table associations
- Internet Gateway versus NAT Gateway
- Why worker nodes often live in private subnets
- Subnet tags used by Kubernetes load balancers

Checkpoint:

- You can explain the route taken by outbound traffic from a private worker node to the internet.

## Phase 2: EKS Control Plane

Objective: understand what AWS manages and what you still manage.

Learn:

- EKS cluster IAM role
- Kubernetes API endpoint public/private access
- Cluster authentication mode
- Kubernetes version lifecycle
- kubeconfig and AWS IAM authentication

Checkpoint:

- `aws eks update-kubeconfig` works and `kubectl get namespaces` returns data.

## Phase 3: Managed Node Groups

Objective: understand how pods get compute capacity.

Learn:

- Node IAM role
- Required worker node policies
- Managed node group lifecycle
- Instance types, desired/min/max capacity, and rolling updates
- Node labels and scheduling basics

Checkpoint:

- `kubectl get nodes -o wide` shows ready EC2-backed nodes in the expected subnets.

## Phase 4: First Workload With NGINX

Objective: prove that image build, registry, scheduling, and networking all work.

Learn:

- Dockerfile basics
- Amazon ECR repository and authentication
- Kubernetes Deployment
- Kubernetes Service
- LoadBalancer behavior on AWS
- Pod logs and rollout debugging

Checkpoint:

- A custom NGINX image is reachable through a Kubernetes Service or Ingress.

## Phase 5: FastAPI Workload

Objective: move from static web serving to a real API container.

Learn:

- Python dependency management
- FastAPI app structure
- Container startup command
- Readiness and liveness probes
- Environment variables and ConfigMaps
- Resource requests and limits

Checkpoint:

- `/health` returns a successful response from FastAPI running on EKS.

## Phase 6: Ingress And DNS

Objective: expose applications in a production-like way.

Learn:

- AWS Load Balancer Controller
- Ingress versus Service type LoadBalancer
- ALB versus NLB
- TLS certificates with ACM
- Route 53 DNS records

Checkpoint:

- A stable DNS name routes HTTPS traffic to the app.

## Phase 7: Identity For Pods

Objective: let workloads use AWS APIs without static credentials.

Learn:

- IAM Roles for Service Accounts
- EKS Pod Identity
- Service accounts
- Least privilege policies

Checkpoint:

- A pod can call an AWS API using its assigned role, without embedded credentials.

## Phase 8: Observability And Debugging

Objective: make the cluster understandable when something fails.

Learn:

- `kubectl describe`, logs, events, exec, port-forward
- EKS control plane logs
- CloudWatch Container Insights or another metrics stack
- Kubernetes events and common scheduling failures

Checkpoint:

- You can debug a failing pod from Pending or CrashLoopBackOff to root cause.

## Phase 9: Scaling And Operations

Objective: understand how EKS changes safely over time.

Learn:

- Horizontal Pod Autoscaler
- Cluster Autoscaler or Karpenter
- Pod disruption budgets
- Rolling updates and rollbacks
- Kubernetes and node group upgrades
- Backup and disaster recovery basics

Checkpoint:

- The app can scale horizontally and survive a controlled node replacement.

## Phase 10: Reusable Project Template

Objective: turn the learning lab into a reusable baseline.

Learn:

- Variables and tfvars per project/environment
- Terraform outputs
- Module boundaries
- Remote state
- CI checks for Terraform and Kubernetes manifests

Checkpoint:

- A new project can reuse the baseline by changing environment-specific inputs instead of editing core resource definitions.
