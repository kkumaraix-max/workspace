🌍 Terraform Workspace + Modules Architecture

This project demonstrates how to use Terraform Workspaces together with modular Infrastructure-as-Code to manage multiple environments (dev, stage, prod) using a single codebase. Workspaces allow environment isolation, while modules provide reusable infrastructure components.

📌 About

This repository implements a scalable Terraform structure using workspaces for environment separation and modules for reusable infrastructure. Instead of duplicating code across different environment folders, Terraform Workspaces let you maintain one root configuration and deploy it into multiple environments simply by switching the workspace.

Modules are used to structure the architecture into clean components such as networking, compute, security, or databases. This makes the code easier to maintain, more readable, and highly reusable across environments.

This approach is ideal for teams that want:

A single Terraform codebase for multiple environments

Strict separation between dev, stage, and prod

Clean organization using Terraform modules

Easy updates and shared logic across all environments

Reduced duplication and safer infrastructure changes

📁 Repository Structure
terraform-workspace-modules/
│
├── modules/
│   ├── vpc/
│   ├── compute/
│   ├── database/
│   ├── security/
│   └── storage/
│
├── main.tf
├── variables.tf
├── outputs.tf
├── versions.tf
└── README.md

🧩 How Workspaces Are Used

Terraform Workspaces allow you to deploy the same infrastructure code to different environments:

dev

stage

prod

Each workspace maintains its own state file, ensuring complete isolation.

Create a new workspace
terraform workspace new dev

Switch to a workspace
terraform workspace select dev

List workspaces
terraform workspace list

Show current workspace
terraform workspace show


Your workspace name is then used inside the config like:

variable "environment" {
  description = "Environment name from Terraform workspace"
  default     = terraform.workspace
}

🔗 How Modules Are Used

Modules are sourced from the local modules/ directory:

Example: Calling a VPC module
module "vpc" {
  source = "./modules/vpc"

  cidr_block      = var.vpc_cidr
  public_subnets  = var.public_subnets
  private_subnets = var.private_subnets

  environment = var.environment
}

Example: Calling a Compute module
module "compute" {
  source = "./modules/compute"

  instance_type = var.instance_type
  ami_id        = var.ami_id
  subnet_id     = module.vpc.private_subnets[0]

  environment = var.environment
}

🚀 Deploying with Workspaces
1. Initialize Terraform
terraform init

2. Select environment (workspace)
terraform workspace select dev


OR create if it doesn’t exist:

terraform workspace new dev

3. Plan infrastructure
terraform plan

4. Apply infrastructure
terraform apply

5. Destroy infrastructure
terraform destroy


Each workspace keeps its own state:

dev → terraform.tfstate (dev)
stage → terraform.tfstate (stage)
prod → terraform.tfstate (prod)

🛡 Best Practices

Use workspace name as part of resource names

Keep modules small and reusable

Never hardcode environment-specific values — use workspaces

Use remote state backend for team environments

Tag everything using ${terraform.workspace}

Keep provider and backend config in the root module only

🧪 Example Variables
environment = terraform.workspace

vpc_cidr = {
  dev   = "10.0.0.0/16"
  stage = "10.1.0.0/16"
  prod  = "10.2.0.0/16"
}[terraform.workspace]


This allows fully dynamic values based on workspace.