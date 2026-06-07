# EKS Learning Progress

Source roadmap: [docs/eks-learning-roadmap.md](docs/eks-learning-roadmap.md)

Last updated: 2026-06-07

Use this file as the working checklist for the custom EKS lab. Checked items mean the repo contains the relevant baseline work or the checkpoint has been verified manually. Unchecked items are still to do or still need confirmation against AWS/Kubernetes.

## Working Agreement For Each Component

For every infrastructure or workload component we work on, keep the learning trail synchronized before calling the item done:

- Update `progress.md` with the status, next check, and any AWS cost note.
- Compare the component to ECS in the component log when there is a useful ECS equivalent.
- Keep the implementation small and explicit while the manual EKS pieces are still being learned.

## Component Study Log

| Component | Source | EKS lab meaning | ECS comparison | Status |
| --- | --- | --- | --- | --- |
| VPC | `2-vpc.tf` | Private network boundary for the cluster, nodes, pods, and load balancers. | ECS services also run inside a VPC; this foundation is shared. | Defined. |
| Public subnets | `4-subnets.tf` | Internet-facing load balancer placement through Kubernetes subnet tags. | ECS services using an internet-facing ALB/NLB also need public subnets. | Defined. |
| Private subnets | `4-subnets.tf` | EKS control plane VPC config and managed node group placement. | ECS tasks or EC2 container instances can also run in private subnets. | Defined. |
| Internet Gateway | `3-igw.tf` | Public route-table target for internet-facing resources. | Same VPC concept for ECS. | Defined. |
| NAT Gateway | `5-nat.tf` | Outbound internet path for private nodes and workloads. | Private ECS tasks often use the same NAT pattern unless VPC endpoints or public networking are used. | Defined. |
| Route tables | `6-routes.tf` | Subnet traffic directions for public and private paths. | Same VPC concept for ECS. | Defined. |
| EKS control plane | `7-eks.tf` | AWS-managed Kubernetes API and cluster control plane. | ECS has an AWS-managed ECS cluster control plane, but no Kubernetes API server. | Defined. |
| Managed node group | `8-nodes.tf` | EC2 worker capacity where Kubernetes pods run. | ECS equivalent is EC2 capacity providers/container instances, or Fargate if you do not manage nodes. | Defined. |
| ECR repository | Planned | Image registry for NGINX and FastAPI images. | Shared with ECS; both EKS and ECS commonly pull images from ECR. | Not started. |
| Kubernetes Deployment and Service | Planned | Runs and exposes the first NGINX/FastAPI workloads. | ECS equivalent is an ECS service plus task definition, often wired to an ALB/NLB. | Not started. |

## Current Snapshot

- [x] Numbered Terraform baseline exists through managed node groups.
- [x] VPC, public subnets, private subnets, Internet Gateway, NAT Gateway, and route tables are defined.
- [x] EKS cluster IAM role and control plane are defined.
- [x] Managed node group IAM role and worker node policies are defined.
- [x] Component tracking now includes a progress entry and ECS comparison when useful.
- [ ] Terraform has been initialized, formatted, validated, and planned in the current environment.
- [ ] Cluster has been applied and verified with `kubectl`.
- [ ] First NGINX workload has been built, pushed, deployed, and exposed.
- [ ] FastAPI workload has been created, containerized, deployed, and health-checked.

Cost note: this lab includes paid AWS resources when applied, including EKS cluster hours, EC2 worker nodes, NAT Gateway hours/data processing, load balancers, EBS volumes, and logs.

## Phase 0: Terraform And AWS Grounding

Objective: understand what Terraform will create and how AWS charges for it.

- [x] Create Terraform provider configuration.
- [x] Define local project, environment, region, Availability Zone, cluster name, and EKS version settings.
- [ ] Run `terraform init`.
- [ ] Run `terraform fmt`.
- [ ] Run `terraform validate`.
- [ ] Run `terraform plan`.
- [ ] Review the plan until every resource has a clear purpose.
- [ ] Identify the cost sources before applying.
- [ ] Practice the lifecycle concepts: init, plan, apply, destroy, state, dependencies, and provider versions.

Checkpoint:

- [ ] `terraform plan` is readable and every resource has a purpose.

## Phase 1: Network Foundation

Objective: understand why EKS needs a real VPC design.

- [x] Define the VPC CIDR range.
- [x] Enable VPC DNS support and DNS hostnames.
- [x] Define two private subnets across two Availability Zones.
- [x] Define two public subnets across two Availability Zones.
- [x] Add Kubernetes tags for public load balancer subnets.
- [x] Add Kubernetes tags for internal load balancer subnets.
- [x] Define an Internet Gateway for public subnet traffic.
- [x] Define an Elastic IP and NAT Gateway for private subnet outbound access.
- [x] Define public and private route tables.
- [x] Associate public and private subnets with the correct route tables.
- [ ] Explain public versus private subnet behavior in this VPC.
- [ ] Explain Internet Gateway versus NAT Gateway behavior.
- [ ] Confirm why worker nodes are placed in private subnets.

Checkpoint:

- [ ] You can explain the route taken by outbound traffic from a private worker node to the internet.

## Phase 2: EKS Control Plane

Objective: understand what AWS manages and what you still manage.

- [x] Define the EKS cluster IAM role.
- [x] Attach the `AmazonEKSClusterPolicy` managed policy.
- [x] Define the EKS control plane.
- [x] Configure the control plane to use private subnets.
- [x] Configure Kubernetes API endpoint access settings.
- [x] Configure EKS authentication mode.
- [x] Pin the Kubernetes version through `local.eks_version`.
- [ ] Confirm the selected EKS version is currently supported by AWS.
- [ ] Run `terraform apply` only when ready for paid resources.
- [ ] Run `aws eks update-kubeconfig --region us-east-1 --name dev-my-demo-eks`.
- [ ] Run `kubectl get namespaces`.

Checkpoint:

- [ ] `aws eks update-kubeconfig` works and `kubectl get namespaces` returns data.

## Phase 3: Managed Node Groups

Objective: understand how pods get compute capacity.

- [x] Define the worker node IAM role.
- [x] Attach `AmazonEKSWorkerNodePolicy`.
- [x] Attach `AmazonEKS_CNI_Policy`.
- [x] Attach `AmazonEC2ContainerRegistryReadOnly`.
- [x] Define an EKS managed node group.
- [x] Place worker nodes in private subnets.
- [x] Configure instance type, capacity type, and scaling bounds.
- [x] Configure rolling update behavior.
- [x] Add a node label for scheduling basics.
- [ ] Run `kubectl get nodes`.
- [ ] Run `kubectl get nodes -o wide`.
- [ ] Confirm nodes are ready and backed by EC2 instances in the expected subnets.
- [ ] Understand desired/min/max capacity and why desired size may be ignored after creation.

Checkpoint:

- [ ] `kubectl get nodes -o wide` shows ready EC2-backed nodes in the expected subnets.

## Phase 4: First Workload With NGINX

Objective: prove that image build, registry, scheduling, and networking all work.

- [ ] Add an Amazon ECR repository for the NGINX image.
- [ ] Create a simple custom NGINX app or static page.
- [ ] Add a Dockerfile for the NGINX image.
- [ ] Build the Docker image locally.
- [ ] Authenticate Docker to Amazon ECR.
- [ ] Push the image to ECR.
- [ ] Add Kubernetes manifests for the NGINX Deployment.
- [ ] Add a Kubernetes Service for NGINX.
- [ ] Decide whether to expose NGINX with Service type `LoadBalancer` or Ingress.
- [ ] Deploy NGINX to the cluster.
- [ ] Verify rollout status.
- [ ] Inspect pod logs.
- [ ] Confirm the endpoint returns the custom NGINX response.

Checkpoint:

- [ ] A custom NGINX image is reachable through a Kubernetes Service or Ingress.

Cost note: exposing NGINX with a Service type `LoadBalancer` or an ALB-backed Ingress can create paid AWS load balancer resources.

## Phase 5: FastAPI Workload

Objective: move from static web serving to a real API container.

- [ ] Create a minimal FastAPI app.
- [ ] Add Python dependency management.
- [ ] Add a FastAPI Dockerfile.
- [ ] Configure the container startup command.
- [ ] Add a `/health` endpoint.
- [ ] Build the FastAPI image locally.
- [ ] Push the FastAPI image to ECR.
- [ ] Add Kubernetes manifests for the FastAPI Deployment.
- [ ] Add readiness and liveness probes.
- [ ] Add resource requests and limits.
- [ ] Add environment variables or ConfigMaps where useful.
- [ ] Deploy FastAPI to the cluster.
- [ ] Verify `/health` returns a successful response.

Checkpoint:

- [ ] `/health` returns a successful response from FastAPI running on EKS.

## Phase 6: Ingress And DNS

Objective: expose applications in a production-like way.

- [ ] Learn Ingress versus Service type `LoadBalancer`.
- [ ] Learn ALB versus NLB tradeoffs.
- [ ] Install or configure the AWS Load Balancer Controller.
- [ ] Add Ingress manifests for the app.
- [ ] Create or choose an ACM TLS certificate.
- [ ] Create Route 53 DNS records.
- [ ] Verify HTTPS traffic reaches the app through a stable DNS name.

Checkpoint:

- [ ] A stable DNS name routes HTTPS traffic to the app.

Cost note: ALBs, NLBs, Route 53 hosted zones/queries, and ACM-adjacent DNS usage can add AWS cost.

## Phase 7: Identity For Pods

Objective: let workloads use AWS APIs without static credentials.

- [ ] Learn IAM Roles for Service Accounts.
- [ ] Learn EKS Pod Identity.
- [ ] Create a Kubernetes service account for a workload.
- [ ] Create a least-privilege IAM policy.
- [ ] Bind the workload to an AWS role.
- [ ] Deploy a test pod that calls an AWS API.
- [ ] Confirm no static AWS credentials are embedded in the pod.

Checkpoint:

- [ ] A pod can call an AWS API using its assigned role, without embedded credentials.

## Phase 8: Observability And Debugging

Objective: make the cluster understandable when something fails.

- [ ] Practice `kubectl describe`.
- [ ] Practice reading pod logs.
- [ ] Practice reading Kubernetes events.
- [ ] Practice `kubectl exec`.
- [ ] Practice `kubectl port-forward`.
- [ ] Enable or review EKS control plane logs.
- [ ] Evaluate CloudWatch Container Insights or another metrics stack.
- [ ] Debug a Pending pod.
- [ ] Debug a CrashLoopBackOff pod.

Checkpoint:

- [ ] You can debug a failing pod from Pending or CrashLoopBackOff to root cause.

Cost note: EKS control plane logs, Container Insights, and retained CloudWatch logs can add ongoing ingestion and storage cost.

## Phase 9: Scaling And Operations

Objective: understand how EKS changes safely over time.

- [ ] Add Horizontal Pod Autoscaler.
- [ ] Add Cluster Autoscaler or Karpenter.
- [ ] Add pod disruption budgets.
- [ ] Practice rolling updates.
- [ ] Practice rollbacks.
- [ ] Practice Kubernetes version upgrade planning.
- [ ] Practice node group upgrades.
- [ ] Learn backup and disaster recovery basics.
- [ ] Test a controlled node replacement.

Checkpoint:

- [ ] The app can scale horizontally and survive a controlled node replacement.

Cost note: autoscaling can increase EC2 capacity, and Karpenter or Cluster Autoscaler may create additional nodes depending on pending workloads.

## Phase 10: Reusable Project Template

Objective: turn the learning lab into a reusable baseline.

- [ ] Add useful Terraform outputs.
- [ ] Move project settings from locals into variables where reuse requires it.
- [ ] Add `*.tfvars` examples for project/environment settings.
- [ ] Decide module boundaries after the manual resources are understood.
- [ ] Add remote state.
- [ ] Add CI checks for Terraform formatting and validation.
- [ ] Add CI checks for Kubernetes manifests.
- [ ] Document how to reuse the baseline for a new project.

Checkpoint:

- [ ] A new project can reuse the baseline by changing environment-specific inputs instead of editing core resource definitions.
