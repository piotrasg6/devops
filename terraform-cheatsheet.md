# Terraform and OpenTofu Cheat Sheet

## Installation and Setup

### Installation
```bash
# Terraform
# Linux (via package manager)
sudo apt-get install terraform  # Debian/Ubuntu
sudo yum install terraform      # RHEL/CentOS
brew install terraform          # macOS with Homebrew

# OpenTofu
# Linux (via package manager)
sudo apt-get install opentofu  # Debian/Ubuntu
sudo yum install opentofu      # RHEL/CentOS
brew install opentofu          # macOS with Homebrew

# Manual installation (download binary and add to PATH)
# Terraform: https://www.terraform.io/downloads.html
# OpenTofu: https://opentofu.org/docs/intro/install/
```

### Version Management
```bash
# Install tfenv (Terraform version manager)
brew install tfenv   # macOS
git clone https://github.com/tfutils/tfenv.git ~/.tfenv  # Linux/manual

# Install specific version
tfenv install 1.5.7

# Switch version
tfenv use 1.5.7

# List installed versions
tfenv list

# Initialize .terraform-version file for project
echo "1.5.7" > .terraform-version
```

### Configuration Files
```bash
# Configuration locations (in order of precedence)
# 1. terraform.rc or .terraformrc in current directory
# 2. .terraform.d/terraform.rc in home directory (Unix)
# 3. %APPDATA%\terraform.rc (Windows)

# Sample configuration (.terraformrc)
plugin_cache_dir = "$HOME/.terraform.d/plugin-cache"
disable_checkpoint = true

provider_installation {
  network_mirror {
    url = "https://terraform-mirror.company.com/"
  }
}

credentials "app.terraform.io" {
  token = "xxxxxx.atlasv1.zzzzzzzzzzzzz"
}
```

## Basic CLI Commands

### Initialization and Setup
```bash
# Initialize working directory
terraform init
terraform init -upgrade  # Force plugin upgrades

# Initialize with backend configuration
terraform init -backend-config=backend.hcl
terraform init -backend-config="key=prod/terraform.tfstate"

# Reconfigure backend without asking for input
terraform init -reconfigure -backend-config=backend.hcl

# Download providers only 
terraform init -backend=false

# Initialize from existing state
terraform init -from-module=MODULE-SOURCE
```

### Planning and Applying Changes
```bash
# Create execution plan
terraform plan
terraform plan -out=tfplan  # Save plan to file

# Apply changes
terraform apply
terraform apply tfplan      # Apply saved plan
terraform apply -auto-approve  # Skip confirmation prompt

# Target specific resources
terraform apply -target=aws_instance.web
terraform apply -target=module.vpc

# Show plan details in compact form
terraform plan -compact-warnings
```

### Inspecting State
```bash
# Show current state
terraform show
terraform show tfplan  # Show saved plan

# List resources in state
terraform state list
terraform state list module.vpc

# Show specific resource details
terraform state show aws_instance.web

# Advanced state queries
terraform state list | grep aws_instance
terraform state list aws_s3_bucket.*
```

### Modifying State
```bash
# Move resource within state (rename)
terraform state mv aws_instance.app aws_instance.web

# Move resource between modules
terraform state mv module.app.aws_instance.ec2 module.web.aws_instance.ec2

# Import existing infrastructure
terraform import aws_instance.web i-abcd1234
terraform import 'aws_instance.web[0]' i-abcd1234  # For resources in count/for_each

# Remove resource from state
terraform state rm aws_instance.web
terraform state rm module.old_vpc

# Replace a resource
terraform apply -replace=aws_instance.web
terraform apply -replace="module.app.aws_instance.web[\"server1\"]"

# Pull remote state
terraform state pull > terraform.tfstate

# Push local state
terraform state push terraform.tfstate
```

### Debugging and Information
```bash
# Validate configuration
terraform validate

# Format configuration files
terraform fmt
terraform fmt -recursive  # Format all files in directory and subdirectories
terraform fmt -check      # Check if files are formatted correctly
terraform fmt -diff       # Show formatting changes

# Show providers
terraform providers

# Show outputs
terraform output
terraform output vpc_id   # Show specific output

# Get help
terraform help
terraform workspace -help
```

### Workspaces
```bash
# List workspaces
terraform workspace list

# Create workspace
terraform workspace new dev

# Select workspace
terraform workspace select prod

# Show current workspace
terraform workspace show

# Delete workspace
terraform workspace delete dev
```

### Miscellaneous Commands
```bash
# Destroy infrastructure
terraform destroy
terraform destroy -target=aws_instance.web  # Destroy specific resource

# Refresh state without making changes
terraform refresh

# Console for interactive expression evaluation
terraform console

# Get Terraform version
terraform version

# Create visual graph
terraform graph | dot -Tsvg > graph.svg
```

## Configuration Language (HCL)

### Basic Syntax
```hcl
# Resource definition
resource "aws_instance" "web" {
  ami           = "ami-a1b2c3d4"
  instance_type = "t2.micro"
  tags = {
    Name = "HelloWorld"
  }
}

# Data source
data "aws_ami" "ubuntu" {
  most_recent = true
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
  owners = ["099720109477"] # Canonical
}

# Provider configuration
provider "aws" {
  region = "us-west-2"
  profile = "default"
}

# Backend configuration
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "path/to/my/key"
    region = "us-east-1"
  }
}

# Required providers and versions
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.16"
    }
  }
  required_version = ">= 1.2.0"
}
```

### Variables and Outputs
```hcl
# Variable definition
variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t2.micro"
  
  validation {
    condition     = contains(["t2.micro", "t3.micro"], var.instance_type)
    error_message = "Only t2.micro and t3.micro are allowed."
  }
}

# Variable types
variable "simple_string" {
  type = string
}

variable "simple_number" {
  type = number
}

variable "simple_bool" {
  type = bool
}

variable "string_list" {
  type = list(string)
  default = ["a", "b", "c"]
}

variable "number_set" {
  type = set(number)
  default = [1, 2, 3]
}

variable "string_map" {
  type = map(string)
  default = {
    key1 = "value1"
    key2 = "value2"
  }
}

variable "complex_object" {
  type = object({
    name    = string
    age     = number
    enabled = bool
    tags    = list(string)
  })
  default = {
    name    = "John"
    age     = 30
    enabled = true
    tags    = ["dev", "admin"]
  }
}

variable "any_type" {
  type = any
}

# Tuple type (fixed-length list with mixed types)
variable "tuple_example" {
  type = tuple([string, number, bool])
  default = ["a", 1, true]
}

# Output definition
output "instance_ip" {
  value       = aws_instance.web.public_ip
  description = "The public IP of the web server"
  sensitive   = false
  depends_on  = [aws_route53_record.www]
}
```

### Local Values
```hcl
# Define local values
locals {
  common_tags = {
    Environment = var.environment
    Project     = var.project_name
    Owner       = "DevOps Team"
    ManagedBy   = "Terraform"
  }
  
  name_prefix = "${var.project_name}-${var.environment}"
  
  # Computed locals
  is_production = var.environment == "prod"
  instance_type = local.is_production ? "m5.large" : "t3.micro"
}

# Using locals
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = local.instance_type
  tags          = merge(local.common_tags, { Name = "${local.name_prefix}-web" })
}
```

### Modules
```hcl
# Module usage
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  version = "3.14.0"
  
  name = "my-vpc"
  cidr = "10.0.0.0/16"
  
  azs             = ["us-west-2a", "us-west-2b", "us-west-2c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  
  enable_nat_gateway = true
  single_nat_gateway = true
  
  tags = local.common_tags
}

# Module with for_each
module "security_group" {
  source  = "terraform-aws-modules/security-group/aws"
  version = "4.9.0"
  
  for_each = var.security_groups
  
  name        = each.key
  description = each.value.description
  vpc_id      = module.vpc.vpc_id
  
  ingress_cidr_blocks = each.value.ingress_cidr_blocks
  ingress_rules       = each.value.ingress_rules
  egress_rules        = ["all-all"]
}

# Module with count
module "instance" {
  source = "./modules/instance"
  count  = var.instance_count
  
  name         = "instance-${count.index}"
  subnet_id    = module.vpc.private_subnets[count.index % length(module.vpc.private_subnets)]
  instance_type = var.instance_type
}

# Accessing module outputs
resource "aws_route53_record" "www" {
  zone_id = data.aws_route53_zone.main.zone_id
  name    = "www.example.com"
  type    = "A"
  ttl     = 300
  records = [module.instance[0].public_ip]
}
```

### Dynamic Blocks
```hcl
# Dynamic block for security group rules
resource "aws_security_group" "web" {
  name        = "web-sg"
  description = "Web server security group"
  vpc_id      = module.vpc.vpc_id
  
  # Static ingress rule
  ingress {
    description = "HTTPS from anywhere"
    from_port   = 443
    to_port     = 443
    protocol    = "tcp"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  # Dynamic ingress rules
  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      description = ingress.value.description
      from_port   = ingress.value.port
      to_port     = ingress.value.port
      protocol    = "tcp"
      cidr_blocks = ingress.value.cidr_blocks
    }
  }
  
  egress {
    from_port   = 0
    to_port     = 0
    protocol    = "-1"
    cidr_blocks = ["0.0.0.0/0"]
  }
  
  tags = local.common_tags
}
```

### Meta-Arguments
```hcl
# count meta-argument
resource "aws_instance" "server" {
  count = var.server_count
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  subnet_id     = var.subnet_ids[count.index % length(var.subnet_ids)]
  
  tags = {
    Name = "server-${count.index + 1}"
  }
}

# for_each meta-argument with map
resource "aws_instance" "web" {
  for_each = var.websites
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = each.value.instance_type
  subnet_id     = each.value.subnet_id
  
  tags = {
    Name = each.key
    Type = each.value.type
  }
}

# for_each with set
resource "aws_route53_record" "www" {
  for_each = toset(var.domains)
  
  zone_id = data.aws_route53_zone.main.zone_id
  name    = each.value
  type    = "A"
  ttl     = 300
  records = [aws_instance.web[each.value].public_ip]
}

# depends_on meta-argument
resource "aws_instance" "app" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  depends_on = [
    aws_db_instance.database,
    aws_iam_role_policy.app_policy
  ]
}

# lifecycle meta-argument
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  lifecycle {
    create_before_destroy = true
    prevent_destroy       = false
    ignore_changes        = [tags]
  }
}

# provider meta-argument
resource "aws_instance" "replica" {
  provider = aws.replica
  ami      = data.aws_ami.replica.id
  # ...
}
```

## Functions and Expressions

### String Functions
```hcl
# String manipulation
locals {
  upper_name    = upper(var.name)                # Convert to uppercase
  lower_name    = lower(var.name)                # Convert to lowercase
  title_name    = title(var.name)                # Title case
  trimmed       = trimspace(var.description)     # Trim whitespace
  replaced      = replace(var.text, "/[^a-z]/", "") # Replace with regex
  joined        = join(", ", var.list)           # Join list elements
  split_values  = split(",", var.comma_string)   # Split string into list
  substring     = substr(var.text, 0, 10)        # Extract substring
  formatted     = format("Hello, %s!", var.name) # Format string
  templates     = "Hello, ${var.name}!"          # String interpolation
  multiline     = <<-EOT
    This is a
    multiline string
    with ${var.name}
  EOT
}
```

### Collection Functions
```hcl
locals {
  # List functions
  first_item    = element(var.list, 0)              # Get element at index
  merged_lists  = concat(var.list1, var.list2)      # Combine lists
  unique_items  = distinct(var.list)                # Remove duplicates
  sorted_list   = sort(var.list)                    # Sort list
  
  # List transformation
  instance_ids  = [for i in aws_instance.web : i.id]  # Create list from resources
  
  # Map functions
  merged_maps   = merge(var.map1, var.map2)        # Combine maps
  map_keys      = keys(var.map)                    # Get map keys
  map_values    = values(var.map)                  # Get map values
  lookup_value  = lookup(var.map, "key", "default") # Look up value with default
  
  # Map transformation
  name_map      = { for i, v in var.list : "item-${i}" => v }
  
  # Set operations
  set_union     = setunion(var.set1, var.set2)     # Union of sets
  set_intersection = setintersection(var.set1, var.set2) # Intersection
  set_difference = setdifference(var.set1, var.set2) # Difference
  
  # Type conversion
  list_to_set   = toset(var.list)                  # Convert list to set
  set_to_list   = tolist(var.set)                  # Convert set to list
  map_to_list   = [for k, v in var.map : { key = k, value = v }] # Map to list of objects
}
```

### Numeric Functions
```hcl
locals {
  # Math operations
  max_value     = max(var.num1, var.num2, var.num3)  # Maximum
  min_value     = min(var.numbers...)               # Minimum (unpacked)
  absolute      = abs(var.number)                   # Absolute value
  ceiling       = ceil(1.1)                         # Ceiling (2)
  floor_value   = floor(1.9)                        # Floor (1)
  log_value     = log(16, 2)                        # Log base 2 of 16 (4)
  power         = pow(2, 3)                         # 2^3 (8)
  sign_value    = signum(-5)                        # Sign (-1, 0, or 1)
  
  # Random
  random_id     = random_id.server.hex              # Use with random provider
  random_pet    = random_pet.server.id              # Random pet name
}
```

### Conditional Expressions
```hcl
locals {
  # Ternary operator
  instance_type = var.environment == "prod" ? "t3.large" : "t3.micro"
  
  # Conditional with null default
  use_name      = var.custom_name != "" ? var.custom_name : null
  
  # Null coalescing
  effective_name = coalesce(var.custom_name, var.default_name, "unnamed")
  
  # Can function for error handling
  valid_cidr    = can(cidrnetmask(var.cidr_block)) ? var.cidr_block : "10.0.0.0/16"
}
```

### Type Functions
```hcl
locals {
  is_string    = can(tostring(var.value))
  is_number    = can(tonumber(var.value))
  is_bool      = can(tobool(var.value))
  
  # Type conversion
  to_string    = tostring(123)                    # "123"
  to_number    = tonumber("123")                  # 123
  to_bool      = tobool("true")                   # true
  
  type_of      = type(var.value)                  # Get type as string
  
  # Try/can for safe operations
  safe_divide  = try(var.numerator / var.denominator, 0)
  has_key      = can(var.map.key)
}
```

### Filesystem and Path Functions
```hcl
locals {
  # File content
  file_content = file("${path.module}/files/script.sh")     # Read file content
  template     = templatefile("${path.module}/templates/config.tpl", {
    port = var.port
    ips  = var.server_ips
  })
  
  # Path manipulation
  basename     = basename("/path/to/file.txt")       # "file.txt"
  dirname      = dirname("/path/to/file.txt")        # "/path/to"
  file_exists  = fileexists("${path.module}/file.txt")  # Check if file exists
  
  # Path references
  module_path  = path.module                        # Current module path
  root_path    = path.root                          # Root module path
  cwd_path     = path.cwd                           # Current working directory
}
```

### Encoding and Crypto Functions
```hcl
locals {
  # Encoding
  base64_encode = base64encode("Hello, world!")      # Base64 encode
  base64_decode = base64decode(local.base64_encode)  # Base64 decode
  url_encode    = urlencode("Hello, world!")         # URL encode
  
  # Crypto
  md5_hash      = md5("Hello, world!")               # MD5 hash
  sha1_hash     = sha1("Hello, world!")              # SHA1 hash
  sha256_hash   = sha256("Hello, world!")            # SHA256 hash
  uuid          = uuid()                             # Generate UUID
  uuid_v5       = uuidv5("dns", "example.com")       # Generate UUID v5
  bcrypt_hash   = bcrypt("password")                 # bcrypt hash (for passwords)
  
  # JSON encoding/decoding
  json_encode   = jsonencode({ name = "value" })     # Convert to JSON string
  json_decode   = jsondecode("{\"name\": \"value\"}") # Parse JSON string
  
  # YAML encoding/decoding
  yaml_encode   = yamlencode({ name = "value" })     # Convert to YAML string
  yaml_decode   = yamldecode("name: value")          # Parse YAML string
}
```

### Network Functions
```hcl
locals {
  # CIDR functions
  subnet_ips    = cidrsubnets("10.0.0.0/16", 8, 8, 8, 8)  # Create subnets
  # Results in ["10.0.0.0/24", "10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  
  hosts         = cidrhost("10.0.0.0/24", 5)       # Get host IP "10.0.0.5"
  netmask       = cidrnetmask("10.0.0.0/24")       # "255.255.255.0"
  prefix        = cidrprefix("10.0.0.0/24")        # 24
  subnet        = cidrsubnet("10.0.0.0/16", 8, 1)  # "10.0.1.0/24"
}
```

## Provisioners

### File Provisioner
```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  key_name      = aws_key_pair.deployer.key_name
  
  # File provisioner
  provisioner "file" {
    source      = "files/app.conf"
    destination = "/etc/app/app.conf"
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }

  # Inline content provisioner
  provisioner "file" {
    content     = templatefile("${path.module}/templates/config.tpl", {
      port = var.port
      ip   = self.private_ip
    })
    destination = "/etc/app/generated.conf"
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
}
```

### Remote-exec Provisioner
```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx",
      "sudo systemctl start nginx"
    ]
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
  
  # Script provisioner
  provisioner "remote-exec" {
    script = "${path.module}/scripts/setup.sh"
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
}
```

### Local-exec Provisioner
```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  
  provisioner "local-exec" {
    command = "echo ${self.private_ip} >> private_ips.txt"
  }
  
  # Local exec with environment variables
  provisioner "local-exec" {
    command = "setup_database.sh"
    
    environment = {
      DB_HOST     = aws_db_instance.default.address
      DB_PORT     = aws_db_instance.default.port
      DB_NAME     = aws_db_instance.default.name
      DB_USER     = aws_db_instance.default.username
      DB_PASSWORD = aws_db_instance.default.password
    }
  }
  
  # Local exec with working directory
  provisioner "local-exec" {
    command     = "./update_inventory.sh"
    working_dir = "${path.module}/scripts"
  }
  
  # Local exec for destroying resources
  provisioner "local-exec" {
    when    = destroy
    command = "echo 'Destroying instance ${self.id}'"
  }
}
```

### Provisioner Connections
```hcl
# Common connection block
connection {
  type        = "ssh"
  user        = "ubuntu"
  private_key = file("~/.ssh/id_rsa")
  host        = self.public_ip
  port        = 22
  agent       = false
  timeout     = "5m"
  
  # Bastion host configuration
  bastion_host        = "bastion.example.com"
  bastion_user        = "bastion-user"
  bastion_private_key = file("~/.ssh/bastion_key")
}

# WinRM connection
connection {
  type     = "winrm"
  user     = "Administrator"
  password = var.admin_password
  host     = self.public_ip
  port     = 5985
  https    = false
  insecure = true
  timeout  = "10m"
}
```

### Failure Handling
```hcl
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  
  # Continue on failure
  provisioner "remote-exec" {
    inline = [
      "sudo apt-get update",
      "sudo apt-get install -y nginx"
    ]
    
    on_failure = continue
    
    connection {
      type        = "ssh"
      user        = "ubuntu"
      private_key = file("~/.ssh/id_rsa")
      host        = self.public_ip
    }
  }
}
```

## Advanced Terraform Techniques

### Data Sources
```hcl
# Basic data source
data "aws_ami" "ubuntu" {
  most_recent = true
  
  filter {
    name   = "name"
    values = ["ubuntu/images/hvm-ssd/ubuntu-focal-20.04-amd64-server-*"]
  }
  
  filter {
    name   = "virtualization-type"
    values = ["hvm"]
  }
  
  owners = ["099720109477"] # Canonical
}

# Remote state data source
data "terraform_remote_state" "vpc" {
  backend = "s3"
  
  config = {
    bucket = "terraform-state"
    key    = "vpc/terraform.tfstate"
    region = "us-west-2"
  }
}

# Using remote state outputs
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  subnet_id     = data.terraform_remote_state.vpc.outputs.public_subnets[0]
}

# Template file data source (legacy approach)
data "template_file" "init" {
  template = file("${path.module}/templates/init.tpl")
  
  vars = {
    region = var.region
    name   = var.name
  }
}

# HTTP data source
data "http" "example" {
  url = "https://checkpoint-api.hashicorp.com/v1/check/terraform"
  
  request_headers = {
    Accept = "application/json"
  }
}

# External data source
data "external" "example" {
  program = ["python", "${path.module}/scripts/get_data.py"]
  
  query = {
    env = var.environment
  }
}
```

### Provider Configuration
```hcl
# Multiple provider configurations
provider "aws" {
  region = "us-west-2"
  alias  = "west"
}

provider "aws" {
  region = "us-east-1"
  alias  = "east"
}

# Use specific provider configuration
resource "aws_instance" "west_app" {
  provider = aws.west
  ami      = data.aws_ami.west_ubuntu.id
  # ...
}

resource "aws_instance" "east_app" {
  provider = aws.east
  ami      = data.aws_ami.east_ubuntu.id
  # ...
}

# Provider with dynamic configuration
provider "aws" {
  region     = var.region
  access_key = var.access_key
  secret_key = var.secret_key
  
  assume_role {
    role_arn     = var.role_arn
    session_name = "terraform"
    external_id  = var.external_id
  }
  
  default_tags {
    tags = local.common_tags
  }
}
```

### Terraform Cloud/Enterprise Integration
```hcl
# Cloud configuration
terraform {
  cloud {
    organization = "example-org"
    
    workspaces {
      name = "my-app-prod"
    }
  }
}

# Terraform Enterprise (self-hosted)
terraform {
  backend "remote" {
    hostname     = "terraform.example.com"
    organization = "example-org"
    
    workspaces {
      name = "my-app-prod"
    }
  }
}
```

### State Management
```hcl
# S3 backend
terraform {
  backend "s3" {
    bucket         = "terraform-state"
    key            = "app/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-lock"
  }
}

# Azure backend
terraform {
  backend "azurerm" {
    resource_group_name  = "terraform-rg"
    storage_account_name = "terraformstate"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}

# GCS backend
terraform {
  backend "gcs" {
    bucket = "terraform-state"
    prefix = "app"
  }
}

# Local backend
terraform {
  backend "local" {
    path = "terraform.tfstate"
  }
}

# Partial backend configuration in code (rest in init command)
terraform {
  backend "s3" {}
}
```

### Terraform Built-in Functions
```hcl
# Special functions
locals {
  # Get path information
  module_path = path.module
  root_path   = path.root
  cwd_path    = path.cwd
  
  # Get Terraform information
  terraform_version = terraform.workspace
  
  # Get workspace information
  workspace_name = terraform.workspace
  is_prod        = terraform.workspace == "prod"
  
  # Sensitive information handling
  sensitive_value = nonsensitive(var.sensitive_input)
  
  # Error handling
  safe_result = try(var.map[var.key], "default_value")
}

# The can function (test if expression can be evaluated)
output "can_cidr" {
  value = can(cidrnetmask(var.cidr_block))
}

# The try function (try expressions until one succeeds)
output "try_example" {
  value = try(
    var.map[var.key],
    var.default_map[var.key],
    "default_value"
  )
}
```

### Dynamic Resource Count
```hcl
# Count based on condition
resource "aws_instance" "bastion" {
  count = var.create_bastion ? 1 : 0
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  
  tags = {
    Name = "bastion"
  }
}

# Reference conditional resource (with risk of error if doesn't exist)
output "bastion_ip" {
  value = var.create_bastion ? aws_instance.bastion[0].public_ip : null
}

# Dynamic count with list length
resource "aws_instance" "web" {
  count = length(var.subnet_ids)
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t2.micro"
  subnet_id     = var.subnet_ids[count.index]
  
  tags = {
    Name = "web-${count.index + 1}"
  }
}
```

### For Expressions
```hcl
# Map transformation
locals {
  # Convert list to map
  instance_map = { for i, v in aws_instance.web : "instance-${i}" => v.public_ip }
  
  # Filter objects
  production_instances = { 
    for k, v in var.instances : k => v 
    if v.environment == "production" 
  }
  
  # Transform lists
  upper_names = [for name in var.names : upper(name)]
  
  # Filter lists
  long_names = [for name in var.names : name if length(name) > 5]
  
  # Nested for expressions
  matrix = [
    for x in range(3) : [
      for y in range(3) : {
        x = x
        y = y
        product = x * y
      }
    ]
  ]
  
  # Generate object
  user_map = {
    for user in var.users :
    user.name => {
      id = user.id
      role = user.role
      active = user.active
    }
  }
}

# For expressions in resource blocks
resource "aws_security_group_rule" "ingress" {
  for_each = {
    for rule in var.ingress_rules :
    "${rule.port}-${rule.protocol}" => rule
  }
  
  type              = "ingress"
  security_group_id = aws_security_group.main.id
  
  from_port   = each.value.port
  to_port     = each.value.port
  protocol    = each.value.protocol
  cidr_blocks = each.value.cidr_blocks
  
  description = each.value.description
}
```

### Advanced Validation
```hcl
# Complex variable validation
variable "instance_type" {
  type        = string
  description = "EC2 instance type"
  
  validation {
    condition     = contains(["t2.micro", "t3.micro", "t3.small"], var.instance_type)
    error_message = "Instance type must be t2.micro, t3.micro, or t3.small."
  }
}

variable "cidr_block" {
  type        = string
  description = "VPC CIDR block"
  
  validation {
    condition     = can(regex("^(10|172\\.(1[6-9]|2[0-9]|3[0-1])|192\\.168)\\.[0-9]{1,3}\\.[0-9]{1,3}/([8-9]|1[0-9]|2[0-8])$", var.cidr_block))
    error_message = "CIDR block must be valid private network: 10.x.x.x/8-28, 172.16-31.x.x/8-28, or 192.168.x.x/16-28."
  }
}

variable "tags" {
  type        = map(string)
  description = "Resource tags"
  
  validation {
    condition     = contains(keys(var.tags), "Environment")
    error_message = "Tags must include 'Environment' key."
  }
}

# Preconditions and postconditions (Terraform 1.2.0+)
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  subnet_id     = var.subnet_id
  
  lifecycle {
    # Check before resource is created/modified
    precondition {
      condition     = data.aws_subnet.selected.availability_zone == var.availability_zone
      error_message = "Subnet must be in the specified availability zone."
    }
    
    # Check after resource is created/modified
    postcondition {
      condition     = self.public_ip != null
      error_message = "EC2 instance must have a public IP address."
    }
  }
}

# Output with postcondition
output "dns_name" {
  value = aws_lb.main.dns_name
  
  precondition {
    condition     = length(aws_lb.main.dns_name) > 0
    error_message = "Load balancer DNS name must not be empty."
  }
}
```

### Module Creation Best Practices
```hcl
# Standard module structure
# my-module/
# ├── README.md
# ├── main.tf
# ├── variables.tf
# ├── outputs.tf
# ├── versions.tf
# ├── examples/
# │   └── basic/
# │       ├── main.tf
# │       ├── variables.tf
# │       └── outputs.tf
# └── test/
#     └── my-module_test.go

# versions.tf (required providers and versions)
terraform {
  required_version = ">= 1.0.0"
  
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = ">= 4.0.0, < 5.0.0"
    }
  }
}

# variables.tf
variable "name" {
  type        = string
  description = "Name to be used for resources"
}

variable "vpc_id" {
  type        = string
  description = "VPC ID"
}

variable "subnet_ids" {
  type        = list(string)
  description = "List of subnet IDs"
}

variable "instance_type" {
  type        = string
  description = "EC2 instance type"
  default     = "t3.micro"
}

variable "tags" {
  type        = map(string)
  description = "A map of tags to add to all resources"
  default     = {}
}

# outputs.tf
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.main.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.main.public_ip
}

output "security_group_id" {
  description = "ID of the security group"
  value       = aws_security_group.main.id
}
```

## Best Practices

### Code Organization
```
project/
├── main.tf                 # Main resources
├── variables.tf            # Input variables declarations
├── outputs.tf              # Output declarations
├── providers.tf            # Provider configuration
├── versions.tf             # Terraform and provider versions
├── backend.tf              # Backend configuration
├── locals.tf               # Local values
├── data.tf                 # Data sources
├── modules/                # Local modules
│   ├── networking/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── outputs.tf
│   └── compute/
│       ├── main.tf
│       ├── variables.tf
│       └── outputs.tf
├── environments/           # Environment-specific configurations
│   ├── dev/
│   │   ├── main.tf
│   │   ├── variables.tf
│   │   └── terraform.tfvars
│   └── prod/
│       ├── main.tf
│       ├── variables.tf
│       └── terraform.tfvars
└── examples/               # Example configurations
    └── complete/
        ├── main.tf
        └── variables.tf
```

### Variable Definition Files
```hcl
# terraform.tfvars
region        = "us-west-2"
instance_type = "t3.micro"
environment   = "dev"
vpc_cidr      = "10.0.0.0/16"
subnet_cidrs  = ["10.0.1.0/24", "10.0.2.0/24"]

# dev.tfvars
environment   = "dev"
instance_type = "t3.micro"
instance_count = 1

# prod.tfvars
environment   = "prod"
instance_type = "t3.large"
instance_count = 3
```

### Naming Conventions
```hcl
# Resources
resource "aws_s3_bucket" "example" {
  bucket = "${var.project}-${var.environment}-logs"
  # ...
}

# Variables
variable "environment" {
  type        = string
  description = "Deployment environment (dev, staging, prod)"
}

# Outputs
output "vpc_id" {
  description = "The ID of the VPC"
  value       = module.vpc.vpc_id
}

# Locals
locals {
  name_prefix   = "${var.project}-${var.environment}"
  common_tags = {
    Project     = var.project
    Environment = var.environment
    ManagedBy   = "Terraform"
  }
}

# Naming patterns
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  tags = merge(
    local.common_tags,
    {
      Name = "${local.name_prefix}-web-${count.index + 1}"
    }
  )
}
```

### Tagging Strategy
```hcl
# Consistent tagging strategy
locals {
  required_tags = {
    Project     = var.project_name
    Environment = var.environment
    Owner       = var.owner
    CostCenter  = var.cost_center
    ManagedBy   = "Terraform"
    CreatedAt   = timestamp()
  }
  
  # Merge required and optional tags
  tags = merge(local.required_tags, var.optional_tags)
}

# Using tags
resource "aws_instance" "web" {
  ami           = data.aws_ami.ubuntu.id
  instance_type = var.instance_type
  
  tags = merge(
    local.tags,
    {
      Name = "${local.name_prefix}-web"
    }
  )
}

# AWS provider default tags
provider "aws" {
  region = var.region
  
  default_tags {
    tags = local.required_tags
  }
}
```

### Input Variables Validation
```hcl
# String validation
variable "environment" {
  type        = string
  description = "Deployment environment"
  
  validation {
    condition     = contains(["dev", "staging", "prod"], var.environment)
    error_message = "Environment must be dev, staging, or prod."
  }
}

# Number range validation
variable "port" {
  type        = number
  description = "Application port"
  
  validation {
    condition     = var.port > 0 && var.port < 65536
    error_message = "Port must be between 1 and 65535."
  }
}

# CIDR validation
variable "cidr_block" {
  type        = string
  description = "VPC CIDR block"
  
  validation {
    condition     = can(cidrnetmask(var.cidr_block))
    error_message = "Must be a valid CIDR block."
  }
  
  validation {
    condition     = cidrprefix(var.cidr_block) <= 24
    error_message = "CIDR block must have a prefix length of /24 or larger."
  }
}

# Map validation
variable "tags" {
  type        = map(string)
  description = "Resource tags"
  
  validation {
    condition     = length(var.tags) > 0
    error_message = "At least one tag must be provided."
  }
  
  validation {
    condition     = contains(keys(var.tags), "Name")
    error_message = "Tags must include a 'Name' key."
  }
}
```

### Secrets Management
```hcl
# DO NOT: Hard-coded secrets in configuration
# BAD PRACTICE
resource "aws_db_instance" "db" {
  username = "admin"
  password = "supersecret"  # Don't do this!
}

# Better: Use environment variables
provider "aws" {
  region     = var.region
  access_key = var.access_key    # Set from environment: export TF_VAR_access_key=...
  secret_key = var.secret_key    # Set from environment: export TF_VAR_secret_key=...
}

# Using variables for secrets
variable "db_password" {
  type        = string
  description = "Database password"
  sensitive   = true
}

resource "aws_db_instance" "db" {
  username = "admin"
  password = var.db_password
}

# Using terraform.tfvars (add to .gitignore)
# terraform.tfvars
db_password = "supersecret"

# Using external secret management
data "aws_secretsmanager_secret" "db" {
  name = "prod/db/password"
}

data "aws_secretsmanager_secret_version" "db_password" {
  secret_id = data.aws_secretsmanager_secret.db.id
}

resource "aws_db_instance" "db" {
  username = "admin"
  password = jsondecode(data.aws_secretsmanager_secret_version.db_password.secret_string)["password"]
}
```

### Error Handling
```hcl
# Recover from errors with try function
locals {
  subnet_id = try(aws_subnet.main[0].id, var.default_subnet_id)
}

# Safely access map values
locals {
  environment_config = {
    dev = {
      instance_type = "t3.micro"
      instance_count = 1
    }
    prod = {
      instance_type = "t3.large"
      instance_count = 3
    }
  }
  
  # Safe map access
  config = try(
    local.environment_config[var.environment],
    local.environment_config["dev"]  # Default if not found
  )
}

# Safe destroy provisioning
resource "null_resource" "cleanup" {
  triggers = {
    bucket_name = aws_s3_bucket.log_bucket.id
  }

  # Safe destroy
  provisioner "local-exec" {
    when    = destroy
    command = <<-EOT
      # Retain value from state since it will be destroyed
      BUCKET=${self.triggers.bucket_name}
      
      # Check if bucket exists
      if aws s3 ls s3://$BUCKET 2>&1 | grep -q 'NoSuchBucket'; then
        echo "Bucket already deleted, skipping cleanup"
      else
        echo "Emptying bucket $BUCKET before deletion"
        aws s3 rm s3://$BUCKET --recursive
      fi
    EOT
  }
}
```

### Modules and Composition
```hcl
# Module composition
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.14.0"
  
  name = "${local.name_prefix}-vpc"
  cidr = var.vpc_cidr
  
  azs             = var.availability_zones
  private_subnets = var.private_subnet_cidrs
  public_subnets  = var.public_subnet_cidrs
  
  enable_nat_gateway = true
  single_nat_gateway = !local.is_production
  
  tags = local.tags
}

module "security_groups" {
  source  = "./modules/security_groups"
  
  vpc_id     = module.vpc.vpc_id
  name_prefix = local.name_prefix
  tags       = local.tags
}

module "alb" {
  source  = "terraform-aws-modules/alb/aws"
  version = "6.0.0"
  
  name               = "${local.name_prefix}-alb"
  vpc_id             = module.vpc.vpc_id
  subnets            = module.vpc.public_subnets
  security_groups    = [module.security_groups.alb_sg_id]
  
  http_tcp_listeners = [
    {
      port               = 80
      protocol           = "HTTP"
      target_group_index = 0
    }
  ]
  
  target_groups = [
    {
      name_prefix          = "app-"
      backend_protocol     = "HTTP"
      backend_port         = 80
      target_type          = "instance"
      deregistration_delay = 300
      health_check = {
        enabled             = true
        interval            = 30
        path                = "/health"
        port                = "traffic-port"
        healthy_threshold   = 2
        unhealthy_threshold = 2
        timeout             = 5
        matcher             = "200-399"
      }
    }
  ]
  
  tags = local.tags
}

module "app" {
  source = "./modules/app"
  
  name_prefix           = local.name_prefix
  vpc_id                = module.vpc.vpc_id
  subnet_ids            = module.vpc.private_subnets
  instance_type         = local.config.instance_type
  instance_count        = local.config.instance_count
  security_group_ids    = [module.security_groups.app_sg_id]
  target_group_arn      = module.alb.target_group_arns[0]
  
  tags = local.tags
}
```

### Testing and CI/CD
```bash
# Terraform plan script for CI/CD
#!/bin/bash
set -e

# Initialize Terraform
terraform init -input=false

# Validate syntax
terraform validate

# Run plan
terraform plan -input=false -out=tfplan

# Optional: Use terraform-docs to update documentation
terraform-docs markdown . > README.md

# Optional: Use tfsec for security scanning
tfsec .

# Optional: Use checkov for compliance scanning
checkov -d .
```

### Cost Estimation
```bash
# Using infracost for cost estimation
infracost breakdown --path .

# Example output of infracost
#  Name                                                 Monthly Qty  Unit   Monthly Cost 
#  
#  aws_instance.app
#  ├─ Instance usage (Linux/UNIX, on-demand, t3.micro)         730  hours        $7.59
#  └─ root_block_device
#     └─ Storage (general purpose SSD, gp2)                      8  GB           $0.80
#  
#  aws_s3_bucket.logs
#  └─ Standard storage                                         100  GB           $2.30
#  
#  OVERALL TOTAL                                                                $10.69
```

## Environment-Specific Configurations

### Using Terraform Workspaces
```bash
# Create workspaces
terraform workspace new dev
terraform workspace new staging
terraform workspace new prod

# List workspaces
terraform workspace list

# Select workspace
terraform workspace select prod

# Show current workspace
terraform workspace show
```

```hcl
# Using workspaces in configuration
locals {
  workspace_config = {
    dev = {
      instance_type  = "t3.micro"
      instance_count = 1
      domain_name    = "dev.example.com"
    }
    staging = {
      instance_type  = "t3.small"
      instance_count = 2
      domain_name    = "staging.example.com"
    }
    prod = {
      instance_type  = "t3.medium"
      instance_count = 3
      domain_name    = "example.com"
    }
  }
  
  # Get configuration for current workspace
  config = local.workspace_config[terraform.workspace]
}

# Use workspace configuration
resource "aws_instance" "app" {
  count = local.config.instance_count
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = local.config.instance_type
  
  tags = {
    Name        = "app-${terraform.workspace}-${count.index + 1}"
    Environment = terraform.workspace
  }
}
```

### Using Directory Structure for Environments
```
environments/
├── dev/
│   ├── main.tf
│   ├── variables.tf
│   ├── terraform.tfvars
│   └── backend.tf
├── staging/
│   ├── main.tf
│   ├── variables.tf
│   ├── terraform.tfvars
│   └── backend.tf
└── prod/
    ├── main.tf
    ├── variables.tf
    ├── terraform.tfvars
    └── backend.tf
```

```hcl
# environments/dev/main.tf
provider "aws" {
  region = var.region
}

module "app" {
  source = "../../modules/app"
  
  environment    = "dev"
  instance_type  = "t3.micro"
  instance_count = 1
  domain_name    = "dev.example.com"
  
  vpc_cidr = "10.0.0.0/16"
  
  tags = {
    Environment = "dev"
    ManagedBy   = "Terraform"
  }
}

# environments/dev/terraform.tfvars
region        = "us-west-2"
instance_type = "t3.micro"
instance_count = 1
domain_name   = "dev.example.com"
```

### Using Conditional Logic
```hcl
# Conditionally create resources
resource "aws_instance" "bastion" {
  count = var.environment == "prod" ? 1 : 0
  
  ami           = data.aws_ami.ubuntu.id
  instance_type = "t3.micro"
  
  tags = {
    Name = "bastion-${var.environment}"
  }
}

# Conditionally set values
locals {
  is_production = var.environment == "prod"
  
  instance_type = local.is_production ? "t3.medium" : "t3.micro"
  
  backup_config = local.is_production ? {
    retention_days = 30
    frequency      = "daily"
  } : {
    retention_days = 7
    frequency      = "weekly"
  }
}

# Conditional module parameters
module "vpc" {
  source  = "terraform-aws-modules/vpc/aws"
  version = "3.14.0"
  
  name = "${var.environment}-vpc"
  cidr = var.vpc_cidr
  
  azs             = var.availability_zones
  private_subnets = var.private_subnet_cidrs
  public_subnets  = var.public_subnet_cidrs
  
  enable_nat_gateway = true
  single_nat_gateway = !local.is_production  # Single NAT for non-prod, multiple for prod
  one_nat_gateway_per_az = local.is_production
  
  enable_vpn_gateway = local.is_production
  
  tags = var.tags
}
```

## Import, Refactoring and Migration

### Importing Existing Resources
```bash
# Import existing resource into state
terraform import aws_instance.web i-1234567890abcdef0

# Import resource in a module
terraform import module.app.aws_instance.web i-1234567890abcdef0

# Import resource with count index
terraform import 'aws_instance.web[0]' i-1234567890abcdef0

# Import resource with for_each key
terraform import 'aws_instance.web["server1"]' i-1234567890abcdef0
```

### Migrating State Between Backends
```bash
# 1. First, make sure you can access both backends

# 2. Create a backup of current state
terraform state pull > terraform.tfstate.backup

# 3. Update backend configuration in code
# Old backend:
terraform {
  backend "s3" {
    bucket = "old-bucket"
    key    = "terraform.tfstate"
    region = "us-west-2"
  }
}

# New backend:
terraform {
  backend "s3" {
    bucket = "new-bucket"
    key    = "terraform.tfstate"
    region = "us-west-2"
  }
}

# 4. Reinitialize with new backend
terraform init -migrate-state
```

### Refactoring Resources
```bash
# Rename a resource
terraform state mv aws_instance.old_name aws_instance.new_name

# Move resource into a module
terraform state mv aws_instance.app module.app.aws_instance.web

# Move resource out of a module
terraform state mv module.old.aws_instance.web aws_instance.web

# Move from count to for_each
terraform state mv 'aws_instance.app[0]' 'aws_instance.app["server1"]'
terraform state mv 'aws_instance.app[1]' 'aws_instance.app["server2"]'

# Move resources across states (requires state files)
terraform state pull > source.tfstate
cd ../target-config/
terraform state pull > target.tfstate
terraform state mv -state=../source-config/source.tfstate -state-out=target.tfstate \
  module.app.aws_instance.web module.new_app.aws_instance.web
terraform state push target.tfstate
```

### Handling State Corruption
```bash
# 1. Make a backup of current state
terraform state pull > terraform.tfstate.backup

# 2. Examine state file manually (if needed)
less terraform.tfstate.backup

# 3. Remove problematic resource
terraform state rm aws_instance.problematic

# 4. Import the resource again
terraform import aws_instance.problematic i-1234567890abcdef0

# If state is badly corrupted:
# 1. Pull state and save backup
terraform state pull > terraform.tfstate.backup

# 2. Create empty state file
echo '{"version": 4, "terraform_version": "1.2.0", "serial": 0, "lineage": "", "outputs": {}, "resources": []}' > empty.tfstate

# 3. Push empty state (CAUTION: this removes all state)
terraform state push empty.tfstate

# 4. Reimport critical resources
terraform import aws_vpc.main vpc-1234567890abcdef0
terraform import aws_subnet.public subnet-1234567890abcdef0
# etc.
```

## Scripting and Automation

### Common Shell Commands for Automation
```bash
#!/bin/bash
set -e

# Initialize different environments
for ENV in dev staging prod; do
  echo "Initializing $ENV environment..."
  cd environments/$ENV
  terraform init -input=false
  cd ../../
done

# Plan and apply changes to an environment
apply_env() {
  local env=$1
  echo "Applying changes to $env environment..."
  cd environments/$env
  terraform init -input=false
  terraform plan -out=tfplan
  terraform apply -auto-approve tfplan
  cd ../../
}

# Run with specific environment
apply_env dev

# Output sensitive info safely
cd environments/prod
terraform output -json db_credentials | jq -r '.password' > /tmp/db_password
chmod 600 /tmp/db_password
```

### Terraform with CI/CD Pipelines
```yaml
# Example GitHub Actions workflow
name: Terraform

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs:
  terraform:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v2
    
    - name: Setup Terraform
      uses: hashicorp/setup-terraform@v1
      with:
        terraform_version: 1.2.0
    
    - name: Terraform Format
      run: terraform fmt -check -recursive
    
    - name: Terraform Init
      run: terraform init
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    
    - name: Terraform Validate
      run: terraform validate
    
    - name: Terraform Plan
      if: github.event_name == 'pull_request'
      run: terraform plan -no-color
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
    
    - name: Terraform Apply
      if: github.ref == 'refs/heads/main' && github.event_name == 'push'
      run: terraform apply -auto-approve
      env:
        AWS_ACCESS_KEY_ID: ${{ secrets.AWS_ACCESS_KEY_ID }}
        AWS_SECRET_ACCESS_KEY: ${{ secrets.AWS_SECRET_ACCESS_KEY }}
```

### Terragrunt Examples
```hcl
# terragrunt.hcl (root)
remote_state {
  backend = "s3"
  generate = {
    path      = "backend.tf"
    if_exists = "overwrite"
  }
  config = {
    bucket         = "my-terraform-state"
    key            = "${path_relative_to_include()}/terraform.tfstate"
    region         = "us-west-2"
    encrypt        = true
    dynamodb_table = "terraform-locks"
  }
}

# Generate provider configuration
generate "provider" {
  path      = "provider.tf"
  if_exists = "overwrite"
  contents  = <<EOF
provider "aws" {
  region = "us-west-2"
}
EOF
}

# Set common variables used by all modules
inputs = {
  common_tags = {
    Owner       = "DevOps Team"
    ManagedBy   = "Terragrunt"
    Environment = "${get_env("TF_VAR_environment", "dev")}"
  }
}

# environments/dev/vpc/terragrunt.hcl
include {
  path = find_in_parent_folders()
}

terraform {
  source = "tfr:///terraform-aws-modules/vpc/aws?version=3.14.0"
}

inputs = {
  name = "my-vpc-dev"
  cidr = "10.0.0.0/16"
  
  azs             = ["us-west-2a", "us-west-2b", "us-west-2c"]
  private_subnets = ["10.0.1.0/24", "10.0.2.0/24", "10.0.3.0/24"]
  public_subnets  = ["10.0.101.0/24", "10.0.102.0/24", "10.0.103.0/24"]
  
  enable_nat_gateway = true
  single_nat_gateway = true
  
  tags = {
    Environment = "dev"
  }
}

# environments/dev/app/terragrunt.hcl
include {
  path = find_in_parent_folders()
}

dependencies {
  paths = ["../vpc"]
}

dependency "vpc" {
  config_path = "../vpc"
}

terraform {
  source = "../../../modules//app"
}

inputs = {
  vpc_id     = dependency.vpc.outputs.vpc_id
  subnet_ids = dependency.vpc.outputs.private_subnets
  
  instance_type  = "t3.micro"
  instance_count = 1
  
  environment = "dev"
}
```

## Differences Between Terraform and OpenTofu

```markdown
# Key Differences Between Terraform and OpenTofu

1. Command Names:
   - Terraform uses the `terraform` command
   - OpenTofu uses either `tofu` or `opentofu` command

2. Provider Registry:
   - Terraform uses registry.terraform.io
   - OpenTofu uses registry.opentofu.org

3. Version Numbering:
   - Terraform follows HashiCorp's versioning
   - OpenTofu started with version 1.6.0 (forked from Terraform 1.5.x)

4. Configuration Files:
   - Terraform uses .terraformrc or terraform.rc
   - OpenTofu recognizes .tofurc or tofu.rc (but also supports Terraform's for backward compatibility)

5. State File Compatibility:
   - OpenTofu can use existing Terraform state files
   - State files created by OpenTofu are compatible with Terraform (with minor differences in metadata)

6. Default CLI Colors:
   - Terraform uses one set of colors
   - OpenTofu uses a different color scheme in its CLI

7. Configuration Language:
   - Both use HCL (HashiCorp Configuration Language)
   - Both support the same syntax and functions
   - Some future features may diverge

8. Cloud Integration:
   - Terraform has Terraform Cloud integration
   - OpenTofu doesn't have an equivalent cloud service (yet)

9. License:
   - Terraform: Business Source License (BSL)
   - OpenTofu: Mozilla Public License v2.0 (MPL-2.0)

10. Community Support:
    - Terraform is backed by HashiCorp
    - OpenTofu is maintained by the OpenTofu community and Linux Foundation
```
