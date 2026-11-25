# Phase 43 – Infrastructure as Code (IaC), Terraform and Cloud Infrastructure Automation

**Level:** Advanced  
**Domain:** DevOps, Cloud Engineering and Infrastructure Automation  
**Primary Tools:** Terraform, AWS, Ansible, GitHub Actions, Linux, Docker  
**Practical Cloud Provider:** Amazon Web Services (AWS)  
**Configuration Language:** HCL (HashiCorp Configuration Language)

---

# 1. Learning Objectives

Infrastructure as Code (IaC) is an essential skill for modern DevOps and cloud engineers.

By completing this phase, you will understand how to:

1. Understand Infrastructure as Code.
2. Differentiate declarative and imperative infrastructure management.
3. Understand Terraform architecture.
4. Install and configure Terraform.
5. Write Terraform configuration using HCL.
6. Work with providers and resources.
7. Use variables, locals and outputs.
8. Understand Terraform state management.
9. Create reusable Terraform modules.
10. Understand Terraform workspaces.
11. Use data sources and provisioners.
12. Manage resource dependencies and lifecycle.
13. Provision AWS infrastructure using Terraform.
14. Create EC2 instances, VPCs and subnets.
15. Configure security groups and networking.
16. Create S3 buckets and RDS databases.
17. Configure remote state and state locking.
18. Automate Terraform using GitHub Actions.
19. Understand Ansible and configuration management.
20. Compare Terraform and Ansible.
21. Design production infrastructure.
22. Apply IaC security and operational best practices.

---

# 2. What Is Infrastructure as Code (IaC)?

## 2.1 Understanding Infrastructure

Before learning IaC, understand what infrastructure means.

Infrastructure refers to the resources required to run an application.

For example, suppose you want to deploy a full-stack application.

You may need:

- Virtual machines.
- Networking.
- Storage.
- Databases.
- Load balancers.
- Firewalls.
- DNS configuration.
- IAM permissions.
- Monitoring systems.

Traditionally, engineers configured these resources manually through cloud dashboards.

For example, in AWS:

1. Open the AWS Console.
2. Create an EC2 instance.
3. Configure the networking.
4. Create security groups.
5. Attach storage.
6. Configure IAM permissions.
7. Create an RDS database.
8. Configure database access.

This process can become complicated when managing many environments or applications.

## 2.2 What Is Infrastructure as Code?

**Infrastructure as Code (IaC)** is the practice of managing and provisioning infrastructure through machine-readable configuration files instead of manually configuring every resource.

Instead of manually creating an EC2 instance, you describe the desired infrastructure in code.

Example:

```hcl
resource "aws_instance" "app_server" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  tags = {
    Name = "Application Server"
  }
}
```

Terraform reads this configuration and communicates with AWS to create the specified resource.

The configuration becomes the infrastructure's reproducible specification.

## 2.3 Manual Infrastructure vs IaC

| Manual Infrastructure | Infrastructure as Code |
|---|---|
| Created through dashboards | Created through configuration |
| Difficult to reproduce consistently | Reproducible |
| Changes may be difficult to track | Changes can be tracked in Git |
| More manual work | More automation |
| Configuration drift is harder to detect | Drift can be detected through plans and refresh |
| Difficult to standardize | Reusable modules and templates |

## 2.4 Real-World Example

Suppose you have three environments:

```text
Development
Staging
Production
```

Each environment requires:

- One application server.
- One database.
- One storage bucket.
- Networking.
- Security groups.

Without IaC, you might manually configure each environment.

With Terraform, you can define reusable infrastructure configurations and supply different environment values.

```text
                 Terraform Code
                       |
              -------------------
              |        |        |
              v        v        v
          Development Staging Production
              |        |        |
              v        v        v
             AWS      AWS      AWS
```

This improves consistency and reduces manual configuration errors.

---

# 3. Declarative vs Imperative Infrastructure

Two important approaches are used to describe infrastructure.

## 3.1 Imperative Approach

In an imperative approach, you specify the steps required to reach a desired result.

Example:

```text
1. Create a virtual machine.
2. Install Node.js.
3. Install Nginx.
4. Create an application directory.
5. Download application files.
6. Start the application.
```

You describe how to perform the task.

Shell scripting is a common imperative approach.

Example:

```bash
#!/bin/bash

sudo apt update

sudo apt install -y nginx

sudo systemctl enable nginx

sudo systemctl start nginx
```

## 3.2 Declarative Approach

In a declarative approach, you describe the desired final state.

Example:

```hcl
resource "aws_instance" "app" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"
}
```

You describe what infrastructure should exist.

Terraform calculates the actions needed to reach that state.

## 3.3 Comparison

| Feature | Imperative | Declarative |
|---|---|---|
| Focus | How to do something | What should exist |
| Instructions | Step-by-step | Desired state |
| Example | Shell scripts | Terraform |
| State management | Often handled manually | Terraform maintains state |
| Change detection | Depends on script logic | Terraform calculates differences |

**Important:** Terraform is primarily declarative, but it also supports imperative operations in limited cases, such as provisioners and external automation. Those mechanisms should not replace normal resource management.

---

# 4. Introduction to Terraform

## 4.1 What Is Terraform?

Terraform is an Infrastructure as Code tool developed by HashiCorp.

It allows you to define, provision and manage infrastructure using configuration files.

Terraform supports many infrastructure providers, including:

- AWS.
- Microsoft Azure.
- Google Cloud.
- Cloudflare.
- Kubernetes.
- GitHub.
- DigitalOcean.

## 4.2 Why Use Terraform?

Terraform provides:

1. Infrastructure automation.
2. Declarative configuration.
3. Infrastructure version control.
4. Reusable modules.
5. Change planning.
6. State management.
7. Multi-cloud support.
8. Infrastructure collaboration.

## 4.3 Terraform vs AWS CloudFormation

| Terraform | AWS CloudFormation |
|---|---|
| Supports multiple providers | Primarily AWS infrastructure |
| Uses HCL | Uses JSON or YAML |
| Maintains Terraform state | AWS manages stack state |
| Supports reusable modules | Supports nested stacks and modules |
| Developed by HashiCorp | Developed by AWS |

Both tools are useful. The choice depends on your infrastructure, team expertise and cloud strategy.

## 4.4 Terraform Architecture

Terraform has several important components:

1. Terraform CLI.
2. Configuration files.
3. Providers.
4. Resources.
5. Data sources.
6. State.
7. Backend.
8. Terraform Registry.

Architecture:

```text
             Terraform Configuration
                      |
                      v
                 Terraform CLI
                      |
                      v
                 Terraform Core
                      |
             -------------------
             |                 |
             v                 v
          Providers          State
             |
       ----------------
       |       |      |
       v       v      v
      AWS   Azure   GCP
```

### Terraform Core

Terraform Core:

- Reads configuration.
- Evaluates expressions.
- Reads state.
- Builds a dependency graph.
- Creates an execution plan.
- Coordinates infrastructure changes.

### Providers

Providers are plugins that allow Terraform to communicate with external platforms.

For example, the AWS provider communicates with AWS APIs.

### Resources

Resources represent infrastructure objects that Terraform manages.

Examples:

```text
aws_instance
aws_s3_bucket
aws_vpc
aws_db_instance
```

### State

State tracks the relationship between Terraform configuration and real infrastructure.

We will study it in detail later.

Official documentation: [Terraform Language Documentation](https://developer.hashicorp.com/terraform/language?utm_source=chatgpt.com).

---

# 5. Installing Terraform

Terraform can be installed on Windows, Linux and macOS.

## 5.1 Windows Installation

You can install Terraform using:

- Chocolatey.
- Winget.
- Manual binary installation.

Using Winget:

```powershell
winget install Hashicorp.Terraform
```

Verify:

```powershell
terraform version
```

## 5.2 Linux Installation

On Ubuntu, follow HashiCorp's official installation instructions.

The general process is:

1. Add HashiCorp's package repository.
2. Install the required signing key.
3. Update package indexes.
4. Install Terraform.

Verify:

```bash
terraform version
```

## 5.3 macOS Installation

Using Homebrew:

```bash
brew tap hashicorp/tap
```

```bash
brew install hashicorp/tap/terraform
```

Verify:

```bash
terraform version
```

Official installation instructions: [Install Terraform](https://developer.hashicorp.com/terraform/install?utm_source=chatgpt.com).

---

# 6. Terraform Project Structure

Terraform configuration files commonly use the `.tf` extension.

Example:

```text
terraform-project/
│
├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
├── terraform.tfvars
├── versions.tf
└── .gitignore
```

Each file has a specific purpose.

| File | Purpose |
|---|---|
| `main.tf` | Main resource definitions |
| `providers.tf` | Provider configuration |
| `variables.tf` | Input variables |
| `outputs.tf` | Output values |
| `terraform.tfvars` | Variable values |
| `versions.tf` | Terraform and provider version constraints |
| `.gitignore` | Excludes generated or sensitive files |

Terraform loads all applicable `.tf` files in the working directory as one configuration.

The filenames are organizational conventions, not mandatory filenames.

## 6.1 Terraform Configuration Language (HCL)

Terraform uses HashiCorp Configuration Language.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  tags = {
    Name = "Web Server"
  }
}
```

Basic syntax:

```hcl
block_type "label" "label" {
  argument = value
}
```

For example:

```hcl
resource "aws_instance" "web" {
  instance_type = "t3.micro"
}
```

Here:

- `resource` is the block type.
- `aws_instance` is the resource type.
- `web` is the local resource name.
- `instance_type` is an argument.

---

# 7. Terraform Providers

## 7.1 What Is a Provider?

A provider is a plugin that allows Terraform to interact with an external platform.

Examples:

```text
AWS Provider
Azure Provider
Google Cloud Provider
Cloudflare Provider
Kubernetes Provider
GitHub Provider
```

Suppose you want to create an EC2 instance.

Terraform needs the AWS provider to communicate with AWS.

## 7.2 Configure the AWS Provider

Create:

`providers.tf`

```hcl
provider "aws" {
  region = "ap-south-1"
}
```

This configures the AWS provider to use the Mumbai region.

## 7.3 Configure Provider Requirements

Create:

`versions.tf`

```hcl
terraform {
  required_version = ">= 1.10.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

This declares:

- A minimum Terraform CLI version.
- The AWS provider source.
- An acceptable provider version range.

**Important:** `~> 6.0` allows compatible 6.x releases under Terraform's version constraint rules. The actual selected version is recorded in `.terraform.lock.hcl`.

The provider version is updated deliberately rather than automatically on every execution.

## 7.4 Initialize Providers

```bash
terraform init
```

Terraform:

1. Initializes the working directory.
2. Downloads required providers.
3. Initializes the backend if configured.
4. Creates `.terraform/`.
5. Creates or updates `.terraform.lock.hcl`.

## 7.5 Provider Authentication

Terraform needs credentials to access AWS.

A common approach is AWS CLI configuration.

```bash
aws configure
```

This configures AWS credentials for local development.

The AWS provider can use the standard AWS credential chain, including environment variables, shared credentials files, IAM roles and other supported mechanisms.

**Never hardcode AWS access keys inside Terraform files.**

For production CI/CD, prefer temporary credentials through OIDC rather than long-lived static access keys.

---

# 8. Terraform Resources

## 8.1 What Is a Resource?

A resource represents an infrastructure object that Terraform creates, updates or destroys.

Examples:

| Terraform Resource | AWS Infrastructure |
|---|---|
| `aws_instance` | EC2 instance |
| `aws_s3_bucket` | S3 bucket |
| `aws_vpc` | Virtual Private Cloud |
| `aws_subnet` | Subnet |
| `aws_security_group` | Security group |
| `aws_db_instance` | RDS database |

## 8.2 Create an EC2 Resource

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  tags = {
    Name = "My Web Server"
  }
}
```

The resource address is:

```text
aws_instance.web
```

The resource type is:

```text
aws_instance
```

The local name is:

```text
web
```

**Note:** The AMI identifier is illustrative. You must use an AMI that exists in your selected AWS region and is compatible with the desired operating system and architecture.

## 8.3 Resource Attributes

Resources have arguments and attributes.

Arguments are configuration values you provide.

Attributes are values Terraform receives from the provider.

For example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"
}
```

An attribute can be referenced using:

```hcl
aws_instance.web.id
```

Other examples include:

```hcl
aws_instance.web.public_ip
```

```hcl
aws_instance.web.private_ip
```

These values may not be known until Terraform creates the instance.

---

# 9. Terraform Workflow and Important Commands

Terraform has a standard workflow.

```text
Write Configuration
        |
        v
terraform init
        |
        v
terraform fmt
        |
        v
terraform validate
        |
        v
terraform plan
        |
        v
Review Plan
        |
        v
terraform apply
        |
        v
Infrastructure Created
```

## 9.1 `terraform init`

Initializes the working directory.

```bash
terraform init
```

Use it when:

- Starting a new Terraform project.
- Adding a provider.
- Adding a module.
- Changing backend configuration.

## 9.2 `terraform fmt`

Formats Terraform configuration files.

```bash
terraform fmt
```

Check formatting without changing files:

```bash
terraform fmt -check -recursive
```

## 9.3 `terraform validate`

Validates configuration syntax and internal consistency.

```bash
terraform validate
```

This does not prove that the AWS resources can actually be created.

## 9.4 `terraform plan`

Shows the proposed infrastructure changes.

```bash
terraform plan
```

Example:

```text
Plan: 1 to add, 0 to change, 0 to destroy.
```

This means Terraform plans to create one resource.

## 9.5 `terraform apply`

Applies the proposed infrastructure changes.

```bash
terraform apply
```

Terraform displays a plan and requests confirmation by default.

To apply a previously saved plan:

```bash
terraform apply tfplan
```

## 9.6 `terraform destroy`

Destroys resources managed by the current Terraform configuration and state.

```bash
terraform destroy
```

**Warning:** This can delete real infrastructure and cause data loss or service outages. Always inspect the plan and verify the target workspace and environment before approving destruction.

## 9.7 Useful Commands

| Command | Purpose |
|---|---|
| `terraform init` | Initialize project |
| `terraform fmt` | Format files |
| `terraform validate` | Validate configuration |
| `terraform plan` | Preview changes |
| `terraform apply` | Apply changes |
| `terraform destroy` | Destroy managed resources |
| `terraform show` | Inspect current state or saved plan |
| `terraform output` | Display outputs |
| `terraform providers` | Show provider requirements |
| `terraform version` | Show installed version |
| `terraform graph` | Generate dependency graph |
| `terraform console` | Evaluate Terraform expressions |

Official CLI documentation: [Terraform CLI Documentation](https://developer.hashicorp.com/terraform/cli?utm_source=chatgpt.com).

---

# 10. Terraform Variables

Variables allow you to make Terraform configurations reusable.

Without variables:

```hcl
resource "aws_instance" "web" {
  instance_type = "t3.micro"
}
```

If you want to change the instance type, you must modify the configuration.

With variables:

```hcl
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
}
```

Use the variable:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = var.instance_type
}
```

## 10.1 Variable Types

Terraform supports types such as:

- string.
- number.
- bool.
- list.
- set.
- map.
- object.
- tuple.
- any.

Examples:

```hcl
variable "region" {
  type    = string
  default = "ap-south-1"
}
```

```hcl
variable "instance_count" {
  type    = number
  default = 2
}
```

```hcl
variable "enable_monitoring" {
  type    = bool
  default = true
}
```

```hcl
variable "availability_zones" {
  type = list(string)

  default = [
    "ap-south-1a",
    "ap-south-1b"
  ]
}
```

```hcl
variable "tags" {
  type = map(string)

  default = {
    Environment = "development"
    Project     = "ecommerce"
  }
}
```

## 10.2 Required Variables

```hcl
variable "db_username" {
  type = string
}
```

Because there is no default, Terraform requires a value from an appropriate input source.

## 10.3 Assign Values Using `.tfvars`

Create:

`terraform.tfvars`

```hcl
region        = "ap-south-1"
instance_type = "t3.micro"
```

Terraform automatically loads `terraform.tfvars` and `terraform.tfvars.json`, as well as appropriately named `*.auto.tfvars` files.

Other variable files can be passed explicitly:

```bash
terraform plan -var-file="staging.tfvars"
```

## 10.4 Pass Variables Through CLI

```bash
terraform plan -var="instance_type=t3.small"
```

## 10.5 Environment Variables

Terraform supports:

```bash
export TF_VAR_instance_type="t3.small"
```

PowerShell:

```powershell
$env:TF_VAR_instance_type = "t3.small"
```

## 10.6 Sensitive Variables

For sensitive values:

```hcl
variable "db_password" {
  type      = string
  sensitive = true
}
```

**Important:** `sensitive = true` primarily redacts values in normal CLI output. It does not encrypt the value in Terraform state or guarantee that it cannot appear in saved plans.

Protect state and plan files accordingly.

---

# 11. Terraform Locals

Locals allow you to define reusable expressions inside a configuration.

Example:

```hcl
locals {
  project_name = "ecommerce"

  common_tags = {
    Project     = local.project_name
    Environment = "development"
    ManagedBy   = "Terraform"
  }
}
```

Use locals:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  tags = local.common_tags
}
```

## Variables vs Locals

| Variables | Locals |
|---|---|
| Receive input from users or automation | Define internal expressions |
| Can have defaults | Defined within `locals` blocks |
| Accessed using `var.name` | Accessed using `local.name` |
| Make configurations configurable | Reduce repetition and simplify expressions |

---

# 12. Terraform Outputs

Outputs expose useful values from Terraform configuration.

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"
}

output "instance_id" {
  value = aws_instance.web.id
}
```

After applying:

```bash
terraform output
```

Read a specific output:

```bash
terraform output instance_id
```

Output the public IP:

```hcl
output "public_ip" {
  value = aws_instance.web.public_ip
}
```

Mark output as sensitive:

```hcl
output "database_password" {
  value     = var.db_password
  sensitive = true
}
```

Remember: output sensitivity does not remove the value from state.

---

# 13. Terraform State Management

This is one of the most important Terraform concepts.

## 13.1 What Is Terraform State?

Terraform state is a record that maps Terraform-managed resource instances to real infrastructure objects.

By default, Terraform stores state in:

```text
terraform.tfstate
```

It may also create:

```text
terraform.tfstate.backup
```

State contains information such as:

- Resource identities.
- Resource attributes.
- Provider metadata.
- Dependency information.
- Sensitive values returned or recorded during provisioning.

## 13.2 Why Does Terraform Need State?

Suppose your configuration contains:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"
}
```

Terraform creates an EC2 instance.

It must remember which real EC2 instance belongs to:

```text
aws_instance.web
```

Without this mapping, Terraform would have difficulty reliably managing future updates and deletions.

## 13.3 Terraform State Workflow

```text
Terraform Configuration
          |
          v
      Terraform
          |
          v
     State File
          |
          v
  Actual Infrastructure
          |
          v
   Cloud Provider APIs
```

## 13.4 Inspect State

List managed resources:

```bash
terraform state list
```

Show a particular resource:

```bash
terraform state show aws_instance.web
```

Inspect state:

```bash
terraform show
```

## 13.5 Important State Commands

| Command | Purpose |
|---|---|
| `terraform state list` | List state resources |
| `terraform state show` | Inspect a resource |
| `terraform state mv` | Move a resource address |
| `terraform state rm` | Remove a state binding |
| `terraform state pull` | Download current state |
| `terraform state push` | Upload state |

Be extremely careful with state modification commands.

For example:

```bash
terraform state rm aws_instance.web
```

This removes the resource from Terraform state but does not automatically destroy the real EC2 instance.

Terraform may subsequently propose creating another instance if the resource remains in the configuration.

## 13.6 Why Should You Never Commit State Files?

Terraform state can contain sensitive information.

For example:

- Database credentials.
- Connection strings.
- Resource identifiers.
- Infrastructure details.
- Provider-returned sensitive attributes.

Add these entries to `.gitignore`:

```gitignore
.terraform/
*.tfstate
*.tfstate.*
*.tfplan
crash.log
crash.*.log
```

Do commit:

```text
.terraform.lock.hcl
```

This lock file records selected provider versions and checksums.

Official documentation: [Terraform State Documentation](https://developer.hashicorp.com/terraform/language/state?utm_source=chatgpt.com).

---

# 14. Remote State and State Locking

Local state is useful for learning, but it is not ideal for a team.

Suppose two developers work on the same infrastructure.

Developer A has one local state file.

Developer B has another local state file.

Their state can diverge, causing inconsistent infrastructure operations.

Remote state solves this collaboration problem.

## 14.1 What Is Remote State?

Remote state stores Terraform state in a shared remote backend.

Examples include:

- Amazon S3.
- HCP Terraform.
- Consul.
- PostgreSQL.

For AWS-based infrastructure, S3 is a common choice.

## 14.2 S3 Backend

Example:

```hcl
terraform {
  backend "s3" {
    bucket       = "company-terraform-state"
    key          = "ecommerce/production/terraform.tfstate"
    region       = "ap-south-1"
    encrypt      = true
    use_lockfile = true
  }
}
```

This configuration stores state in S3 and enables S3 native state locking.

**Important prerequisites:**

- The S3 bucket must already exist.
- The bucket should have versioning enabled.
- Access should be tightly controlled.
- Encryption should be enabled.
- The Terraform execution identity must have appropriate permissions.
- The backend configuration must be initialized.

The S3 backend supports native locking through `use_lockfile`. The older DynamoDB-based locking configuration is deprecated in current Terraform documentation, so new configurations should use native S3 locking where supported. [HashiCorp Developer](https://developer.hashicorp.com/terraform/language/state/backends?utm_source=chatgpt.com)

## 14.3 What Is State Locking?

State locking prevents multiple Terraform operations from modifying the same state simultaneously when the backend supports locking.

Example:

```text
Developer A
     |
     v
Acquire State Lock
     |
     v
Terraform Apply
     |
     v
Release Lock
```

Meanwhile:

```text
Developer B
     |
     v
Try to Acquire Lock
     |
     v
Wait / Fail
```

This helps prevent state corruption caused by concurrent writes.

## 14.4 Force Unlock

If a lock becomes stuck due to an interrupted operation, Terraform provides:

```bash
terraform force-unlock <LOCK_ID>
```

Use this only after confirming that the lock belongs to your failed operation and no other Terraform operation is active.

Do not force-unlock a valid lock held by another developer.

Official references:

- [Terraform State Locking](https://developer.hashicorp.com/terraform/language/state/locking?utm_source=chatgpt.com)
- [Terraform Backend Documentation](https://developer.hashicorp.com/terraform/language/state/backends?utm_source=chatgpt.com)

---

# 15. Terraform Modules

## 15.1 What Is a Module?

A Terraform module is a collection of Terraform configuration files managed together.

Modules help you organize and reuse infrastructure definitions.

For example, instead of writing VPC configuration repeatedly, create a reusable VPC module.

```text
terraform-project/
│
├── main.tf
│
└── modules/
    │
    ├── vpc/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    ├── ec2/
    │   ├── main.tf
    │   ├── variables.tf
    │   └── outputs.tf
    │
    └── rds/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

## 15.2 Root Module

The directory where you run Terraform is the root module.

Example:

```hcl
module "network" {
  source = "./modules/vpc"

  vpc_cidr = "10.0.0.0/16"
}
```

## 15.3 Child Module

Inside:

`modules/vpc/variables.tf`

```hcl
variable "vpc_cidr" {
  type = string
}
```

`modules/vpc/main.tf`

```hcl
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr

  tags = {
    Name = "Application VPC"
  }
}
```

`modules/vpc/outputs.tf`

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}
```

## 15.4 Access Module Outputs

In the root module:

```hcl
output "network_vpc_id" {
  value = module.network.vpc_id
}
```

## 15.5 Use Public Modules

Terraform Registry contains community and official modules.

Example:

```hcl
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"

  name = "production-vpc"
  cidr = "10.0.0.0/16"

  azs             = ["ap-south-1a", "ap-south-1b"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24"]
}
```

For production, pin module versions and review the module source, defaults and security implications before use.

## 15.6 Benefits of Modules

- Reusability.
- Standardization.
- Maintainability.
- Reduced duplication.
- Easier testing.
- Consistent infrastructure.

Official reference: [Terraform Modules Documentation](https://developer.hashicorp.com/terraform/language/modules?utm_source=chatgpt.com).

---

# 16. Terraform Workspaces

## 16.1 What Is a Workspace?

Terraform workspaces allow multiple state instances to be associated with one configuration.

For example:

```text
default
development
staging
production
```

Each workspace has its own state.

## 16.2 List Workspaces

```bash
terraform workspace list
```

## 16.3 Create Workspace

```bash
terraform workspace new development
```

## 16.4 Switch Workspace

```bash
terraform workspace select production
```

## 16.5 Show Current Workspace

```bash
terraform workspace show
```

## 16.6 Delete Workspace

```bash
terraform workspace delete development
```

A workspace containing managed resources normally cannot be deleted without first removing those resources or using the appropriate state-management process.

## 16.7 Workspace-Specific Values

Example:

```hcl
locals {
  environment = terraform.workspace
}
```

Then:

```hcl
resource "aws_s3_bucket" "app" {
  bucket = "my-ecommerce-${local.environment}"
}
```

This creates differently named buckets for different workspaces.

## 16.8 Workspace Limitations

Workspaces are useful for separating state instances, but they are not automatically a complete environment-isolation or security strategy.

For important production environments, consider:

- Separate root modules.
- Separate state backends or keys.
- Separate AWS accounts.
- Separate IAM roles.
- Separate credentials.
- Separate approval policies.

Do not assume that selecting a workspace automatically protects production infrastructure.

---

# 17. Terraform Data Sources

## 17.1 What Is a Data Source?

A data source allows Terraform to read information about existing infrastructure.

A resource creates or manages infrastructure.

A data source queries infrastructure information without creating or modifying the queried object.

Example:

You already have an AWS VPC.

You want to retrieve its ID using Terraform.

```hcl
data "aws_vpc" "existing" {
  default = true
}
```

Access its ID:

```hcl
data.aws_vpc.existing.id
```

## 17.2 Query an Existing AMI

Example:

```hcl
data "aws_ami" "ubuntu" {
  most_recent = true

  owners = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-noble-24.04-amd64-server-*"]
  }

  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
}
```

Use the AMI:

```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
}
```

This is more flexible than hardcoding an AMI ID.

However, a moving `most_recent` query may select a different image in a later plan. For tightly controlled production releases, pin or otherwise constrain the AMI selection.

## 17.3 Resource vs Data Source

| Resource | Data Source |
|---|---|
| Manages infrastructure | Reads infrastructure information |
| Can create resources | Does not create the queried object |
| Can update resources | Does not update the queried object |
| Can destroy managed resources | Does not destroy the queried object |
| Uses `resource` block | Uses `data` block |

Official reference: [Terraform Data Sources](https://docs.hashicorp.com/terraform/language/data-sources?utm_source=chatgpt.com).

---

# 18. Terraform Provisioners

## 18.1 What Is a Provisioner?

Provisioners allow Terraform to execute commands or scripts during certain resource lifecycle events.

For example:

- Running a shell command on a remote machine.
- Executing a local command.
- Copying a file to a server.

Terraform supports provisioner types such as:

- `local-exec`.
- `remote-exec`.
- `file`.

## 18.2 Local-Exec Example

```hcl
resource "terraform_data" "example" {
  provisioner "local-exec" {
    command = "echo Infrastructure created"
  }
}
```

This runs a local command during the resource lifecycle.

## 18.3 Remote-Exec Example

Conceptual example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  connection {
    type        = "ssh"
    user        = "ubuntu"
    private_key = file("deployment-key.pem")
    host        = self.public_ip
  }

  provisioner "remote-exec" {
    inline = [
      "sudo apt update",
      "sudo apt install -y nginx"
    ]
  }
}
```

This example requires:

- A valid AMI.
- A reachable public IP.
- A properly configured SSH key.
- Security group access.
- A compatible SSH user.
- A suitable network path.

Do not treat this as a production-ready deployment method.

## 18.4 Why Should You Avoid Provisioners?

Provisioners are generally a last resort because:

- Terraform cannot reliably model every command's effect.
- Provisioner failures can leave partially configured infrastructure.
- Re-running operations can be difficult.
- Idempotency is not guaranteed.
- Application configuration becomes mixed with infrastructure provisioning.

Better alternatives:

- Ansible.
- Cloud-init.
- AWS Systems Manager.
- Container images.
- Configuration management tools.

**Important:** Provisioners are not a replacement for normal Terraform resources or dedicated configuration-management tools.

---

# 19. Resource Dependencies and Lifecycle

## 19.1 Resource Dependencies

Terraform builds a dependency graph.

Suppose an EC2 instance requires a security group.

```hcl
resource "aws_security_group" "web" {
  name = "web-security-group"
}
```

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  vpc_security_group_ids = [
    aws_security_group.web.id
  ]
}
```

Terraform understands that the security group must exist before the instance can use it.

This is an implicit dependency.

## 19.2 Explicit Dependencies

Sometimes dependencies cannot be inferred through resource references.

Use:

```hcl
depends_on = [
  aws_security_group.web
]
```

Example:

```hcl
resource "aws_instance" "web" {
  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  depends_on = [
    aws_security_group.web
  ]
}
```

Use explicit dependencies only when genuinely necessary.

## 19.3 Terraform Lifecycle

The `lifecycle` block controls certain resource lifecycle behaviors.

Example:

```hcl
resource "aws_s3_bucket" "app" {
  bucket = "example-unique-bucket-name"

  lifecycle {
    prevent_destroy = true
  }
}
```

### `prevent_destroy`

Prevents Terraform from planning destruction of the resource while this lifecycle rule remains in its configuration.

### `create_before_destroy`

```hcl
lifecycle {
  create_before_destroy = true
}
```

This requests creation of the replacement before destruction of the old resource when replacement is required and the provider/API allows it.

### `ignore_changes`

```hcl
lifecycle {
  ignore_changes = [
    tags
  ]
}
```

This tells Terraform to ignore changes to specified attributes during normal update planning.

Use it carefully because it can hide meaningful configuration drift.

Official reference: [Terraform Lifecycle Documentation](https://developer.hashicorp.com/terraform/language/meta-arguments/lifecycle?utm_source=chatgpt.com).

---

# 20. AWS Infrastructure Fundamentals

Before creating AWS infrastructure using Terraform, understand the basic components.

## 20.1 Amazon EC2

Amazon EC2 provides virtual servers in the AWS cloud.

Common use cases:

- Hosting Node.js applications.
- Running Docker containers.
- Hosting Nginx.
- Running background workers.
- Running self-managed databases.

## 20.2 Amazon VPC

Amazon VPC provides logically isolated networking for AWS resources.

A VPC contains:

- CIDR ranges.
- Subnets.
- Route tables.
- Internet gateways.
- NAT gateways.
- Security groups.
- Network ACLs.

## 20.3 Amazon S3

Amazon S3 is an object-storage service.

Common use cases:

- Images.
- Videos.
- Documents.
- Application uploads.
- Backups.
- Static assets.
- Terraform state storage.

## 20.4 Amazon RDS

Amazon RDS provides managed relational databases.

It supports database engines such as:

- MySQL.
- PostgreSQL.
- MariaDB.
- Oracle.
- Microsoft SQL Server.

## 20.5 Basic AWS Architecture

```text
                  Internet
                     |
                     v
                Internet Gateway
                     |
                     v
                    VPC
                     |
          -----------------------
          |                     |
          v                     v
      Public Subnet         Private Subnet
          |                     |
          v                     v
       EC2 / ALB              RDS
```

Application servers can receive traffic through an appropriate public entry point, while databases should normally remain private.

---

# 21. Create an AWS VPC Using Terraform

Now we will create an actual infrastructure configuration.

**Cost and safety warning:** Applying AWS Terraform configurations can create billable resources. NAT Gateways, RDS, EC2, public IPv4 addresses and data transfer can incur charges. Review the AWS pricing for every resource, use a test account, and destroy test infrastructure when finished.

## 21.1 Project Structure

```text
aws-terraform/
│
├── versions.tf
├── providers.tf
├── variables.tf
├── main.tf
├── outputs.tf
└── terraform.tfvars
```

## 21.2 Provider Configuration

`versions.tf`

```hcl
terraform {
  required_version = ">= 1.10.0"

  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 6.0"
    }
  }
}
```

`providers.tf`

```hcl
provider "aws" {
  region = var.aws_region
}
```

## 21.3 Variables

`variables.tf`

```hcl
variable "aws_region" {
  type    = string
  default = "ap-south-1"
}

variable "vpc_cidr" {
  type    = string
  default = "10.0.0.0/16"
}

variable "environment" {
  type    = string
  default = "development"
}
```

## 21.4 Create VPC

`main.tf`

```hcl
resource "aws_vpc" "main" {
  cidr_block           = var.vpc_cidr
  enable_dns_support   = true
  enable_dns_hostnames = true

  tags = {
    Name        = "ecommerce-vpc"
    Environment = var.environment
  }
}
```

## 21.5 Create Public Subnet

```hcl
resource "aws_subnet" "public" {
  vpc_id                  = aws_vpc.main.id
  cidr_block              = "10.0.1.0/24"
  availability_zone       = "ap-south-1a"
  map_public_ip_on_launch = true

  tags = {
    Name = "public-subnet"
  }
}
```

## 21.6 Create Private Subnet

```hcl
resource "aws_subnet" "private" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.2.0/24"
  availability_zone = "ap-south-1a"

  tags = {
    Name = "private-subnet"
  }
}
```

## 21.7 Create Internet Gateway

```hcl
resource "aws_internet_gateway" "main" {
  vpc_id = aws_vpc.main.id

  tags = {
    Name = "main-igw"
  }
}
```

## 21.8 Create Public Route Table

```hcl
resource "aws_route_table" "public" {
  vpc_id = aws_vpc.main.id

  route {
    cidr_block = "0.0.0.0/0"
    gateway_id = aws_internet_gateway.main.id
  }

  tags = {
    Name = "public-route-table"
  }
}
```

## 21.9 Associate Public Subnet

```hcl
resource "aws_route_table_association" "public" {
  subnet_id      = aws_subnet.public.id
  route_table_id = aws_route_table.public.id
}
```

## 21.10 Outputs

`outputs.tf`

```hcl
output "vpc_id" {
  value = aws_vpc.main.id
}

output "public_subnet_id" {
  value = aws_subnet.public.id
}

output "private_subnet_id" {
  value = aws_subnet.private.id
}
```

## 21.11 Deploy

Run:

```bash
terraform init
```

```bash
terraform fmt
```

```bash
terraform validate
```

```bash
terraform plan
```

Review the plan carefully.

Then:

```bash
terraform apply
```

This creates:

- One VPC.
- One public subnet.
- One private subnet.
- One internet gateway.
- One public route table.
- One subnet association.

**Important:** The private subnet currently has no NAT Gateway or other outbound route. This is intentional in this basic example. Private resources requiring outbound internet connectivity need an appropriate network design.

---

# 22. Create an EC2 Instance Using Terraform

Now create an EC2 instance inside the VPC.

## 22.1 Create Security Group

```hcl
resource "aws_security_group" "web" {
  name        = "web-security-group"
  description = "Allow web traffic"
  vpc_id      = aws_vpc.main.id

  ingress {
    description = "HTTP"
    from_port   = 80
    to_port     = 80
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  ingress {
    description = "HTTPS"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }

  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
}
```

This permits HTTP and HTTPS ingress from the internet.

It does not configure SSH ingress, which is deliberately omitted.

For production SSH access, prefer AWS Systems Manager Session Manager or tightly restricted administrative access rather than opening port 22 to the world.

## 22.2 Create EC2 Instance

```hcl
resource "aws_instance" "web" {
  ami                    = data.aws_ami.ubuntu.id
  instance_type          = "t3.micro"
  subnet_id              = aws_subnet.public.id
  vpc_security_group_ids = [aws_security_group.web.id]

  user_data = <<-EOF
    #!/bin/bash
    apt-get update
    apt-get install -y nginx
    systemctl enable nginx
    systemctl start nginx
  EOF

  tags = {
    Name = "ecommerce-web-server"
  }
}
```

This uses cloud-init through EC2 user data to install Nginx.

## 22.3 AMI Data Source

Add:

```hcl
data "aws_ami" "ubuntu" {
  most_recent = true

  owners = ["099720109477"]

  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-noble-24.04-amd64-server-*"]
  }

  filter {
    name   = "architecture"
    values = ["x86_64"]
  }
}
```

The data source selects a matching Ubuntu AMI from the specified owner.

For reproducible production infrastructure, consider pinning the AMI ID rather than always selecting the newest matching image.

## 22.4 Public IP Output

```hcl
output "ec2_public_ip" {
  value = aws_instance.web.public_ip
}
```

After applying:

```bash
terraform output ec2_public_ip
```

You can test:

```text
http://PUBLIC_IP
```

If Nginx is installed and the instance is reachable, the default Nginx page should appear.

---

# 23. Create an S3 Bucket Using Terraform

S3 is frequently used for application uploads, assets and backups.

## 23.1 Create Bucket

```hcl
resource "aws_s3_bucket" "uploads" {
  bucket = "my-ecommerce-uploads-unique-name"

  tags = {
    Name        = "Ecommerce Uploads"
    Environment = var.environment
  }
}
```

S3 bucket names must satisfy AWS naming rules and be globally unique within the applicable namespace.

## 23.2 Enable Versioning

```hcl
resource "aws_s3_bucket_versioning" "uploads" {
  bucket = aws_s3_bucket.uploads.id

  versioning_configuration {
    status = "Enabled"
  }
}
```

Versioning preserves multiple versions of objects.

It can help recover from accidental overwrites or deletions, subject to lifecycle and deletion-marker behavior.

## 23.3 Enable Server-Side Encryption

```hcl
resource "aws_s3_bucket_server_side_encryption_configuration" "uploads" {
  bucket = aws_s3_bucket.uploads.id

  rule {
    apply_server_side_encryption_by_default {
      sse_algorithm = "AES256"
    }
  }
}
```

This configures S3-managed server-side encryption.

## 23.4 Block Public Access

```hcl
resource "aws_s3_bucket_public_access_block" "uploads" {
  bucket = aws_s3_bucket.uploads.id

  block_public_acls       = true
  block_public_policy     = true
  ignore_public_acls      = true
  restrict_public_buckets = true
}
```

This is a strong default for private application uploads.

If public delivery is required, prefer a controlled architecture such as CloudFront with Origin Access Control rather than making the bucket publicly writable.

---

# 24. Create an RDS Database Using Terraform

Suppose your application uses MySQL.

Instead of installing MySQL directly on an EC2 instance, you can use Amazon RDS.

## 24.1 RDS Architecture

```text
             Internet
                 |
                 v
            Application
                 |
                 v
             Private VPC
                 |
                 v
             RDS MySQL
```

The database should normally be private and accessible only from approved application security groups.

## 24.2 Create DB Subnet Group

RDS typically requires subnets across at least two Availability Zones for a DB subnet group.

For example, add another private subnet:

```hcl
resource "aws_subnet" "private_b" {
  vpc_id            = aws_vpc.main.id
  cidr_block        = "10.0.3.0/24"
  availability_zone = "ap-south-1b"

  tags = {
    Name = "private-subnet-b"
  }
}
```

Then create a DB subnet group:

```hcl
resource "aws_db_subnet_group" "main" {
  name = "ecommerce-db-subnet-group"

  subnet_ids = [
    aws_subnet.private.id,
    aws_subnet.private_b.id
  ]
}
```

## 24.3 Create Database Security Group

```hcl
resource "aws_security_group" "database" {
  name   = "database-security-group"
  vpc_id = aws_vpc.main.id

  ingress {
    description     = "MySQL from application security group"
    from_port       = 3306
    to_port         = 3306
    protocol        = "tcp"
    security_groups = [aws_security_group.web.id]
  }
}
```

This permits MySQL traffic from the application security group.

For a production application, use a separate application security group and avoid allowing database access from broad public CIDR ranges.

## 24.4 Create RDS Instance

```hcl
resource "aws_db_instance" "mysql" {
  identifier = "ecommerce-mysql"

  engine         = "mysql"
  instance_class = "db.t3.micro"

  allocated_storage = 20
  storage_type      = "gp3"

  db_name  = "ecommerce"
  username = var.db_username
  password = var.db_password

  db_subnet_group_name   = aws_db_subnet_group.main.name
  vpc_security_group_ids = [aws_security_group.database.id]

  publicly_accessible = false

  backup_retention_period = 7

  skip_final_snapshot = false
  final_snapshot_identifier = "ecommerce-final-snapshot"

  tags = {
    Name = "ecommerce-mysql"
  }
}
```

This is an educational starting point, not a complete production database configuration.

Production configuration should additionally address:

- Encryption at rest.
- TLS in transit.
- Multi-AZ availability requirements.
- Storage autoscaling.
- Monitoring.
- Maintenance windows.
- Password management.
- Snapshot retention.
- Deletion protection.
- Recovery testing.
- Engine version selection.
- Cost requirements.

The chosen instance class and engine version must be supported in the selected region.

**Important:** Never hardcode a real database password in `.tf` or `.tfvars` files committed to Git. Terraform state may also contain the password. Use a secure secret-management approach and tightly restrict state access.

---

# 25. Terraform Import and Existing Infrastructure

Sometimes infrastructure already exists and you want Terraform to manage it.

You do not necessarily need to destroy and recreate it.

## 25.1 What Is Terraform Import?

Import associates an existing infrastructure object with a Terraform resource address.

For example, you already have an S3 bucket.

You want Terraform to manage it.

First define the resource:

```hcl
resource "aws_s3_bucket" "existing" {
  bucket = "existing-company-bucket"
}
```

Then import:

```bash
terraform import aws_s3_bucket.existing existing-company-bucket
```

Import does not automatically generate a complete, correct configuration for every resource.

You must ensure your configuration accurately represents the imported object.

## 25.2 Import Blocks

Terraform also supports declarative import blocks.

Example:

```hcl
import {
  to = aws_s3_bucket.existing
  id = "existing-company-bucket"
}
```

Then run:

```bash
terraform plan
```

Review the proposed import and apply it.

## 25.3 Why Is Import Useful?

- Adopt manually created infrastructure.
- Standardize existing resources.
- Bring resources under version control.
- Reduce manual configuration.
- Migrate to IaC gradually.

Official reference: [Terraform Import Documentation](https://developer.hashicorp.com/terraform/language/import?utm_source=chatgpt.com).

---

# 26. Terraform Workflows With GitHub Actions

Terraform should also be part of CI/CD.

A common workflow:

```text
Developer Creates PR
        |
        v
Terraform Format Check
        |
        v
Terraform Validate
        |
        v
Terraform Plan
        |
        v
Review Plan
        |
        v
Approve
        |
        v
Terraform Apply
```

## 26.1 Repository Structure

```text
infrastructure/
│
├── main.tf
├── providers.tf
├── variables.tf
├── outputs.tf
├── versions.tf
│
└── .github/
    │
    └── workflows/
        └── terraform.yml
```

## 26.2 Example Terraform CI Workflow

```yaml
name: Terraform CI

on:
  pull_request:
    paths:
      - "infrastructure/**"

permissions:
  contents: read

jobs:
  terraform:
    runs-on: ubuntu-latest

    defaults:
      run:
        working-directory: infrastructure

    steps:
      - name: Checkout
        uses: actions/checkout@v7

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v4

      - name: Terraform Format
        run: terraform fmt -check -recursive

      - name: Terraform Init
        run: terraform init -backend=false

      - name: Terraform Validate
        run: terraform validate
```

This workflow performs basic static validation.

It does not access the production backend or apply infrastructure changes.

## 26.3 Terraform Plan in CI

A real plan workflow needs:

- Backend configuration.
- Cloud authentication.
- Correct permissions.
- A protected state backend.
- Appropriate handling of plan files.

Example AWS authentication:

```yaml
permissions:
  contents: read
  id-token: write
```

Then use GitHub OIDC to assume a restricted AWS IAM role.

## 26.4 Terraform Apply

A production apply should generally be a separate protected job.

Example:

```yaml
jobs:
  apply:
    runs-on: ubuntu-latest

    environment: production

    steps:
      - name: Terraform Apply
        run: terraform apply -auto-approve
```

This is only a conceptual fragment. A real apply job must initialize the correct backend, authenticate with the correct cloud role, use the intended Terraform version and apply the reviewed configuration or saved plan.

**Important:** Do not automatically apply arbitrary Terraform changes from untrusted Pull Requests. Terraform plans can expose sensitive infrastructure details and may involve privileged cloud operations.

---

# 27. Ansible Fundamentals

Terraform is not the only infrastructure automation tool.

Another important tool is Ansible.

## 27.1 What Is Ansible?

Ansible is an automation and configuration-management tool.

It helps automate tasks such as:

- Installing software.
- Configuring servers.
- Managing users.
- Deploying applications.
- Configuring Nginx.
- Managing services.
- Performing system updates.

Unlike Terraform's provider-based infrastructure management, Ansible commonly connects to target systems and executes tasks using modules.

## 27.2 Terraform vs Ansible Example

Suppose you need to deploy a Node.js application.

Terraform can:

- Create an EC2 instance.
- Create a VPC.
- Create a security group.
- Create an RDS database.

Ansible can:

- Install Node.js.
- Install Nginx.
- Create application directories.
- Configure PM2.
- Deploy application configuration.
- Start and restart services.

Architecture:

```text
            Terraform
                |
                v
          Create AWS EC2
                |
                v
           EC2 Instance
                |
                v
             Ansible
                |
        ------------------
        |        |       |
      Node.js  Nginx    PM2
        |        |       |
        ------------------
                |
                v
        Running Application
```

## 27.3 Install Ansible

On a suitable Linux environment, follow the official installation documentation.

Verify:

```bash
ansible --version
```

Official documentation: [Ansible Documentation](https://docs.ansible.com/projects/ansible/latest/?utm_source=chatgpt.com).

---

# 28. Ansible Architecture

Ansible has several important components.

| Component | Purpose |
|---|---|
| Control Node | Machine running Ansible |
| Managed Node | Server being configured |
| Inventory | List of managed hosts |
| Playbook | YAML automation instructions |
| Module | Performs a particular task |
| Task | Individual operation |
| Role | Reusable automation structure |
| Handler | Runs when notified by a task |

## 28.1 Control Node

The machine running Ansible commands.

Example:

```text
Developer Laptop
```

## 28.2 Managed Node

The machine being configured.

Example:

```text
AWS EC2 Ubuntu Server
```

## 28.3 Inventory

Ansible needs to know which servers it should manage.

Create:

`inventory.ini`

```ini
[web]
192.0.2.10
192.0.2.11

[database]
192.0.2.20
```

The IP addresses above are illustrative documentation addresses, not real deployment targets.

## 28.4 Test Connectivity

```bash
ansible all -i inventory.ini -m ping
```

This checks whether Ansible can communicate with the managed nodes.

---

# 29. Ansible Playbooks

## 29.1 What Is a Playbook?

A playbook is a YAML file containing automation instructions.

Example:

`install-nginx.yml`

```yaml
---
- name: Configure Web Servers
  hosts: web
  become: true

  tasks:
    - name: Update Package Cache
      ansible.builtin.apt:
        update_cache: true

    - name: Install Nginx
      ansible.builtin.apt:
        name: nginx
        state: present

    - name: Start Nginx
      ansible.builtin.service:
        name: nginx
        state: started
        enabled: true
```

Run:

```bash
ansible-playbook -i inventory.ini install-nginx.yml
```

This playbook:

1. Targets the `web` inventory group.
2. Uses privilege escalation.
3. Updates package indexes.
4. Installs Nginx.
5. Ensures the service is running and enabled.

## 29.2 Ansible Idempotency

Idempotency means repeatedly applying the same desired configuration should not continually produce unnecessary changes.

For example:

```yaml
- name: Ensure Nginx Is Installed
  ansible.builtin.apt:
    name: nginx
    state: present
```

If Nginx is already installed, Ansible normally reports no change.

This is an important configuration-management concept.

## 29.3 Ansible Handlers

Handlers run when notified by tasks.

Example:

```yaml
---
- name: Configure Nginx
  hosts: web
  become: true

  tasks:
    - name: Update Nginx Configuration
      ansible.builtin.copy:
        src: nginx.conf
        dest: /etc/nginx/nginx.conf
        mode: "0644"
      notify: Restart Nginx

  handlers:
    - name: Restart Nginx
      ansible.builtin.service:
        name: nginx
        state: restarted
```

The handler runs only if the notifying task reports a change.

## 29.4 Ansible Roles

Roles organize automation into reusable structures.

Example:

```text
ansible-project/
│
├── inventory/
│   └── production.ini
│
├── playbooks/
│   └── site.yml
│
└── roles/
    │
    ├── nginx/
    │   ├── tasks/
    │   ├── handlers/
    │   ├── templates/
    │   ├── files/
    │   ├── vars/
    │   └── defaults/
    │
    └── nodejs/
        ├── tasks/
        ├── handlers/
        └── defaults/
```

Roles help organize large automation projects.

Official reference: [Ansible Playbooks Documentation](https://docs.ansible.com/projects/ansible/latest/user_guide/playbooks.html?utm_source=chatgpt.com).

---

# 30. Terraform vs Ansible

Both tools are useful, but their primary responsibilities differ.

| Feature | Terraform | Ansible |
|---|---|---|
| Primary purpose | Infrastructure provisioning and lifecycle management | Configuration management and automation |
| Language | HCL | YAML |
| Architecture | Provider-based | Control node and managed nodes |
| State | Maintains infrastructure state | Does not use Terraform-style resource state |
| Main workflow | Plan and apply | Execute playbooks |
| Infrastructure creation | Excellent | Possible, through modules |
| Server configuration | Limited provisioner support | Excellent |
| Application deployment | Possible | Common use case |
| Cloud resources | Strong support through providers | Supported through collections |
| Idempotency | Declarative resource management | Many modules are idempotent |

## 30.1 When Should You Use Terraform?

Use Terraform when you need to:

- Create cloud infrastructure.
- Manage networking.
- Create EC2 instances.
- Provision databases.
- Manage storage.
- Configure cloud resources.
- Track infrastructure lifecycle.

## 30.2 When Should You Use Ansible?

Use Ansible when you need to:

- Install packages.
- Configure Linux servers.
- Manage users.
- Configure services.
- Deploy applications.
- Apply operating-system updates.
- Maintain server configuration.

## 30.3 Can You Use Both?

Yes.

A common production workflow is:

```text
             GitHub Actions
                   |
                   v
                Terraform
                   |
                   v
             Create AWS VPC
                   |
                   v
             Create EC2
                   |
                   v
                 Ansible
                   |
          -------------------
          |         |       |
        Node.js   Nginx    PM2
          |         |       |
          -------------------
                   |
                   v
            Running Backend
```

Terraform manages infrastructure.

Ansible configures the operating system and application environment.

---

# 31. Production Infrastructure Architecture

Let's design infrastructure for a full-stack e-commerce application.

Technology stack:

```text
Frontend: Next.js
Backend: Node.js + Express
Database: MySQL
Cache: Redis
Storage: Amazon S3
Infrastructure: AWS
Automation: Terraform
Configuration: Ansible
CI/CD: GitHub Actions
```

## 31.1 Architecture

```text
                       Users
                         |
                         v
                    CloudFront
                         |
                         v
                  Application Entry
                         |
              -----------------------
              |                     |
              v                     v
         Frontend                Backend
         Next.js               Express API
              |                     |
              |            ------------------
              |            |        |       |
              |            v        v       v
              |          MySQL    Redis    S3
              |            |
              ----------------
                         |
                         v
                    Monitoring
```

## 31.2 Infrastructure Components

| Component | AWS Service | Managed By |
|---|---|---|
| Virtual Servers | EC2 | Terraform |
| Network | VPC | Terraform |
| Subnets | VPC Subnets | Terraform |
| Firewall | Security Groups | Terraform |
| Object Storage | S3 | Terraform |
| Relational DB | RDS | Terraform |
| Application Setup | Linux Configuration | Ansible |
| Reverse Proxy | Nginx | Ansible |
| Process Management | PM2 or systemd | Ansible |
| CI/CD | GitHub Actions | GitHub |
| Logs and Metrics | CloudWatch | Terraform / AWS configuration |

## 31.3 Production Network Design

A more secure network layout:

```text
                         Internet
                            |
                            v
                     Application Entry
                            |
                            v
                           VPC
                            |
              ---------------------------
              |                         |
              v                         v
         Public Subnets            Private Subnets
              |                         |
              v                         v
       Load Balancer              Application / DB
                                        |
                               -----------------
                               |               |
                               v               v
                              RDS             Redis
```

Production architecture should consider:

- Multiple Availability Zones.
- Private database subnets.
- Restricted inbound rules.
- Encryption.
- IAM least privilege.
- Backups.
- Monitoring.
- Disaster recovery.
- Cost controls.

For applications requiring high availability, use suitable managed load balancing, auto scaling and database availability features rather than relying on one EC2 instance.

---

# 32. Infrastructure Security Best Practices

Infrastructure security must be part of IaC from the beginning.

## 32.1 Never Hardcode Secrets

Avoid:

```hcl
password = "my-secret-password"
```

Use secure secret-management approaches.

Examples:

- AWS Secrets Manager.
- AWS Systems Manager Parameter Store.
- Secure CI/CD environment secrets.
- Appropriate secret injection mechanisms.

Remember that secret values may still be recorded in Terraform state depending on the resource and provider.

## 32.2 Follow Least Privilege

IAM policies should grant only the permissions required.

Avoid:

```text
AdministratorAccess
```

for routine Terraform execution unless there is a narrowly justified administrative task.

## 32.3 Protect State Files

State files should have:

- Encryption.
- Access controls.
- Versioning or recovery capability.
- Locking.
- Auditability.
- Restricted download permissions.

## 32.4 Secure Networking

Avoid:

```hcl
cidr_blocks = ["0.0.0.0/0"]
```

for sensitive administrative services such as:

- SSH.
- RDP.
- Database ports.
- Internal management APIs.

Allow only required traffic from trusted security groups or restricted network ranges.

## 32.5 Enable S3 Security Controls

For sensitive storage buckets:

- Block public access.
- Enable encryption.
- Enable versioning where appropriate.
- Configure lifecycle policies.
- Apply least-privilege bucket policies.
- Monitor access.
- Consider Object Lock when retention requirements justify it.

## 32.6 Use Infrastructure Scanning

Useful tools include:

| Tool | Purpose |
|---|---|
| Checkov | IaC security scanning |
| tfsec | Terraform security analysis |
| Trivy | Vulnerability and IaC scanning |
| Terrascan | Infrastructure policy scanning |
| OPA | Policy enforcement |
| Sentinel | Policy as code in supported HashiCorp products |

A scanner can detect common misconfigurations, but it cannot guarantee that infrastructure is secure.

## 32.7 Protect Against Destructive Changes

Before applying:

```bash
terraform plan
```

Review:

- Resources to create.
- Resources to update.
- Resources to destroy.
- Security group changes.
- Database replacement.
- Storage deletion.
- IAM permission changes.

Do not blindly approve a production plan.

---

# 33. Advanced Terraform Concepts

These topics are important for production-level infrastructure management.

## 33.1 `for_each`

Use `for_each` to create resources from a map or set.

Example:

```hcl
variable "buckets" {
  type = set(string)

  default = [
    "frontend-assets",
    "user-uploads",
    "application-backups"
  ]
}

resource "aws_s3_bucket" "app" {
  for_each = var.buckets

  bucket = "${each.key}-unique-company-suffix"
}
```

This creates multiple bucket resources.

The final names must still satisfy S3 naming rules and be globally unique.

## 33.2 `count`

Use `count` to create multiple instances of a resource.

```hcl
resource "aws_instance" "web" {
  count = 2

  ami           = "ami-0123456789abcdef0"
  instance_type = "t3.micro"

  tags = {
    Name = "web-${count.index}"
  }
}
```

This creates two EC2 instances.

## 33.3 `for_each` vs `count`

| Feature | `count` | `for_each` |
|---|---|---|
| Input | Number or boolean | Map or set |
| Access | `count.index` | `each.key`, `each.value` |
| Best use | Similar repeated resources | Distinct named resources |
| Identity | Numeric index | Stable key |

For resources with distinct identities, `for_each` is often easier to maintain.

## 33.4 Dynamic Blocks

Dynamic blocks generate nested configuration blocks from collections.

Example:

```hcl
variable "ingress_rules" {
  type = list(object({
    port        = number
    description = string
  }))

  default = [
    {
      port        = 80
      description = "HTTP"
    },
    {
      port        = 443
      description = "HTTPS"
    }
  ]
}
```

Use:

```hcl
resource "aws_security_group" "web" {
  name = "web-security-group"

  dynamic "ingress" {
    for_each = var.ingress_rules

    content {
      from_port   = ingress.value.port
      to_port     = ingress.value.port
      protocol    = "tcp"
      cidr_blocks = ["0.0.0.0/0"]
      description = ingress.value.description
    }
  }
}
```

This is an example of dynamic configuration generation. In production, prefer explicit rule resources and tightly controlled source ranges where appropriate.

## 33.5 Terraform Lifecycle Meta-Arguments

Other useful meta-arguments include:

- `create_before_destroy`.
- `prevent_destroy`.
- `ignore_changes`.
- `replace_triggered_by`.

Example:

```hcl
lifecycle {
  replace_triggered_by = [
    terraform_data.deployment_version
  ]
}
```

These options help control resource replacement and lifecycle behavior.

---

# 34. Common Terraform and Ansible Mistakes

| Mistake | Better Practice |
|---|---|
| Committing Terraform state | Store state in a secure remote backend |
| Running apply without reviewing plan | Review planned changes |
| Hardcoding credentials | Use secure credential mechanisms |
| Giving Terraform excessive IAM permissions | Use least privilege |
| Using `terraform destroy` carelessly | Confirm environment and impact |
| Ignoring provider versions | Pin and review provider upgrades |
| Using one shared state for unrelated systems | Separate state boundaries |
| Overusing Terraform workspaces for isolation | Use separate accounts and state boundaries where appropriate |
| Using provisioners for all configuration tasks | Prefer Ansible, cloud-init or images |
| Opening database ports publicly | Use private networking and restricted security groups |
| Ignoring database backups | Configure and test recovery |
| Creating production infrastructure manually | Manage it through controlled IaC |
| Treating state as a backup | Maintain independent recovery procedures |
| Running concurrent applies | Use state locking |
| Hardcoding AMI IDs without tracking | Use controlled, documented image selection |
| Ignoring infrastructure drift | Regularly review Terraform plans |
| Using unreviewed public modules | Inspect sources and pin versions |
| Running Ansible without inventory control | Use well-defined inventories and environments |

---

# 35. Interview Questions and Answers

## Basic-Level Questions

### Q1. What is Infrastructure as Code?

IaC is the practice of provisioning and managing infrastructure through machine-readable configuration files.

### Q2. What is Terraform?

Terraform is an Infrastructure as Code tool that provisions and manages infrastructure using declarative configuration.

### Q3. What is a Terraform provider?

A provider is a plugin that allows Terraform to communicate with external platforms such as AWS, Azure and Google Cloud.

### Q4. What is a Terraform resource?

A resource represents an infrastructure object managed by Terraform.

### Q5. What is Terraform state?

Terraform state maps resource definitions in configuration to real infrastructure objects.

### Q6. What is `terraform init`?

It initializes the Terraform working directory and downloads required providers and modules.

### Q7. What is `terraform plan`?

It generates a proposed execution plan showing infrastructure changes.

### Q8. What is `terraform apply`?

It executes the approved plan and applies infrastructure changes.

### Q9. What is a Terraform variable?

A variable is an input value used to make configuration reusable and configurable.

### Q10. What is a Terraform module?

A module is a reusable collection of Terraform configuration files.

## Intermediate-Level Questions

### Q11. What is the difference between a resource and a data source?

A resource manages infrastructure. A data source reads information about existing infrastructure.

### Q12. What is a Terraform backend?

A backend defines where Terraform stores its state and, depending on the backend, how state locking is handled.

### Q13. What is remote state?

Remote state stores Terraform state in a shared remote location rather than a local file.

### Q14. What is state locking?

State locking prevents conflicting Terraform operations from writing to the same state simultaneously when the backend supports locking.

### Q15. What is a Terraform workspace?

A workspace provides a separate state instance for a particular Terraform configuration.

### Q16. What is `terraform.tfvars`?

It is a variable definition file that supplies values to Terraform variables.

### Q17. What is `depends_on`?

It explicitly declares a dependency between Terraform objects when Terraform cannot infer the dependency automatically.

### Q18. What is `prevent_destroy`?

It is a lifecycle rule that prevents Terraform from planning destruction of a resource while the rule remains in the configuration.

### Q19. What is Terraform import?

Import associates an existing infrastructure object with a Terraform resource address.

### Q20. What is Ansible?

Ansible is an automation and configuration-management tool used to configure systems and automate operational tasks.

## Advanced-Level Questions

### Q21. What is the difference between Terraform and Ansible?

Terraform primarily manages infrastructure lifecycle, while Ansible primarily manages system configuration and automation.

### Q22. Why should Terraform state not be committed to Git?

State may contain sensitive values and infrastructure metadata. It should be stored in a secure backend with appropriate access control.

### Q23. What is infrastructure drift?

Infrastructure drift occurs when real infrastructure differs from the configuration Terraform expects to manage.

### Q24. What is the purpose of Terraform modules?

Modules organize reusable infrastructure definitions and reduce duplication.

### Q25. What is the difference between `count` and `for_each`?

`count` uses numeric indexes, while `for_each` uses map keys or set elements to identify resource instances.

### Q26. Why should provisioners be used sparingly?

Provisioners are difficult to model reliably, may not be idempotent and can leave partially configured infrastructure.

### Q27. How do you secure Terraform state?

Use encrypted remote storage, access controls, state locking, versioning, restricted IAM permissions and secure handling of plan files.

### Q28. What is Terraform infrastructure drift?

It is the difference between declared configuration and actual deployed infrastructure.

### Q29. Why is OIDC useful for Terraform in GitHub Actions?

It allows workflows to obtain temporary cloud credentials without storing long-lived cloud access keys.

### Q30. How do Terraform and Ansible work together?

Terraform provisions infrastructure such as EC2 instances and networking. Ansible configures operating systems, installs packages and deploys applications.

---

# 36. Practical Assignments

Complete these assignments to develop real-world IaC skills.

## Assignment 1: Terraform Installation

1. Install Terraform.
2. Verify the version.
3. Configure the AWS CLI.
4. Verify AWS identity.

Command:

```bash
aws sts get-caller-identity
```

## Assignment 2: Create an AWS VPC

Using Terraform, create:

- One VPC.
- One public subnet.
- One private subnet.
- One Internet Gateway.
- One public route table.
- One subnet association.

## Assignment 3: Create an EC2 Instance

Create:

- One EC2 instance.
- One security group.
- HTTP and HTTPS access.
- Nginx installation through user data.
- Public IP output.

Do not expose SSH publicly.

## Assignment 4: Create an S3 Bucket

Configure:

- S3 bucket.
- Versioning.
- Encryption.
- Public access blocking.
- Outputs.

## Assignment 5: Create an RDS Database

Configure:

- RDS MySQL.
- DB subnet group.
- Private subnets.
- Database security group.
- Backup retention.
- Secure credentials.

Use a low-cost test configuration and inspect regional pricing before applying.

## Assignment 6: Build a Terraform Module

Create a reusable EC2 module.

Requirements:

- Variables.
- Outputs.
- Resource definitions.
- Input validation where appropriate.

Call the module from a root module.

## Assignment 7: Remote State

Configure an S3 backend.

Requirements:

- Versioned state bucket.
- Encryption.
- Native S3 state locking.
- IAM access restrictions.
- Secure backend initialization.

## Assignment 8: Ansible Configuration

Create an Ansible playbook that:

- Connects to an EC2 instance.
- Installs Node.js.
- Installs Nginx.
- Configures the application directory.
- Starts the required services.

## Assignment 9: Terraform + Ansible Automation

Create a workflow:

```text
Terraform
   |
   v
Create EC2
   |
   v
Ansible
   |
   v
Configure Server
   |
   v
Deploy Application
```

## Assignment 10: Complete Production IaC Project

Design infrastructure for a full-stack e-commerce application.

Requirements:

- VPC.
- Public and private subnets.
- EC2 or another application compute service.
- Security groups.
- RDS MySQL.
- S3 bucket.
- Remote Terraform state.
- Separate environments.
- GitHub Actions validation.
- Secure deployment credentials.
- Ansible server configuration.
- Monitoring and backup strategy.

---

# 37. Quick Revision Notes

| Concept | Important Point |
|---|---|
| IaC | Infrastructure managed through code |
| Terraform | Infrastructure provisioning tool |
| HCL | Terraform configuration language |
| Provider | Plugin connecting Terraform to external platforms |
| Resource | Infrastructure object managed by Terraform |
| Data Source | Reads existing infrastructure information |
| Variable | Input to Terraform configuration |
| Local | Internal reusable expression |
| Output | Exposes useful configuration values |
| State | Maps Terraform resources to real infrastructure |
| Backend | Stores Terraform state |
| State Locking | Prevents conflicting state writes |
| Module | Reusable Terraform configuration |
| Workspace | Separate state instance |
| Provisioner | Executes commands during resource lifecycle |
| `terraform init` | Initializes working directory |
| `terraform fmt` | Formats Terraform code |
| `terraform validate` | Validates configuration |
| `terraform plan` | Previews infrastructure changes |
| `terraform apply` | Applies infrastructure changes |
| `terraform destroy` | Destroys managed resources |
| Ansible | Configuration management and automation |
| Playbook | YAML automation instructions |
| Inventory | List of managed machines |
| Role | Reusable Ansible automation structure |
| Idempotency | Repeated execution converges on the desired state |
| Infrastructure Drift | Difference between desired and actual infrastructure |

---

# 38. Completion Checklist

- [ ] Understand Infrastructure as Code.
- [ ] Understand declarative vs imperative infrastructure.
- [ ] Understand Terraform architecture.
- [ ] Install Terraform.
- [ ] Understand HCL syntax.
- [ ] Configure Terraform providers.
- [ ] Create Terraform resources.
- [ ] Use Terraform variables.
- [ ] Use locals and outputs.
- [ ] Understand Terraform initialization.
- [ ] Use `terraform plan` and `terraform apply`.
- [ ] Understand Terraform state.
- [ ] Configure remote state.
- [ ] Understand state locking.
- [ ] Create Terraform modules.
- [ ] Use Terraform workspaces.
- [ ] Use data sources.
- [ ] Understand provisioners.
- [ ] Manage resource dependencies.
- [ ] Understand lifecycle rules.
- [ ] Create an AWS VPC.
- [ ] Create public and private subnets.
- [ ] Configure route tables and Internet Gateways.
- [ ] Create EC2 instances.
- [ ] Configure security groups.
- [ ] Create S3 buckets.
- [ ] Create RDS databases.
- [ ] Import existing infrastructure.
- [ ] Automate Terraform using GitHub Actions.
- [ ] Understand Ansible architecture.
- [ ] Create Ansible inventories and playbooks.
- [ ] Understand Ansible roles and handlers.
- [ ] Compare Terraform and Ansible.
- [ ] Design production IaC architecture.
- [ ] Apply infrastructure security best practices.

---

# 39. Official Documentation and Learning Resources

### Terraform

- [Terraform Official Documentation](https://developer.hashicorp.com/terraform/docs?utm_source=chatgpt.com)
- [Terraform Getting Started Tutorials](https://developer.hashicorp.com/terraform/tutorials?utm_source=chatgpt.com)
- [Terraform Providers](https://developer.hashicorp.com/terraform/language/providers?utm_source=chatgpt.com)
- [Terraform Resources](https://developer.hashicorp.com/terraform/language/resources?utm_source=chatgpt.com)
- [Terraform Variables](https://developer.hashicorp.com/terraform/language/values/variables?utm_source=chatgpt.com)
- [Terraform State](https://developer.hashicorp.com/terraform/language/state?utm_source=chatgpt.com)
- [Terraform Modules](https://developer.hashicorp.com/terraform/language/modules?utm_source=chatgpt.com)

### AWS

- [AWS Terraform Provider Documentation](https://registry.terraform.io/providers/-/aws/latest/docs/docs?utm_source=chatgpt.com)
- [AWS VPC Documentation](https://docs.aws.amazon.com/vpc/?utm_source=chatgpt.com)
- [Amazon EC2 Documentation](https://docs.aws.amazon.com/ec2/?utm_source=chatgpt.com)
- [Amazon S3 Documentation](https://docs.aws.amazon.com/s3/?utm_source=chatgpt.com)
- [Amazon RDS Documentation](https://docs.aws.amazon.com/rds/?utm_source=chatgpt.com)

### Ansible

- [Ansible Official Documentation](https://docs.ansible.com/projects/ansible/latest/?utm_source=chatgpt.com)
- [Ansible Playbooks](https://docs.ansible.com/projects/ansible/latest/user_guide/playbooks.html?utm_source=chatgpt.com)
- [Ansible Roles](https://docs.ansible.com/projects/ansible/latest/playbook_guide/playbooks_reuse_roles.html?utm_source=chatgpt.com)

---

# Phase 43 Completed

You have now covered **Infrastructure as Code, Terraform and Cloud Infrastructure Automation**.

The most important practical skills from this phase are:

1. Understanding IaC and Terraform architecture.
2. Writing Terraform configuration using HCL.
3. Managing providers, resources and variables.
4. Understanding Terraform state and remote backends.
5. Creating reusable Terraform modules.
6. Provisioning AWS infrastructure.
7. Automating server configuration with Ansible.
8. Integrating Terraform with GitHub Actions.
9. Designing secure and reusable cloud infrastructure.

## Next: Phase 44 – Advanced Cloud Networking, AWS VPC, DNS, Load Balancing and CDN

Upcoming topics:

1. Computer Networking Refresher.
2. IP Addressing and CIDR.
3. Public vs Private IP Addresses.
4. Subnetting and Network Segmentation.
5. AWS VPC Architecture.
6. Public and Private Subnets.
7. Internet Gateways.
8. NAT Gateways.
9. Route Tables.
10. Security Groups vs Network ACLs.
11. VPC Peering.
12. Transit Gateway.
13. VPC Endpoints and PrivateLink.
14. DNS Fundamentals.
15. Amazon Route 53.
16. DNS Records and Routing Policies.
17. Load Balancing Fundamentals.
18. AWS Elastic Load Balancing.
19. Application Load Balancer vs Network Load Balancer.
20. SSL/TLS and HTTPS.
21. CloudFront and CDN Architecture.
22. WAF and DDoS Protection.
23. Network Troubleshooting.
24. Production AWS Network Architecture.

**When you're ready, say: "OK, Phase 44".**