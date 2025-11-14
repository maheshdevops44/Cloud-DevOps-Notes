# DevOps Interview Preparation Guide

> **By DevOps Architect**  
> A comprehensive guide for preparing DevOps interviews with real-world scenarios and practical examples.

## 📋 Table of Contents

1. [About the Author](#about-the-author)
2. [Prerequisites](#prerequisites)
3. [Linux Interview Questions](#linux-interview-questions)
4. [Bash Scripting Interview Questions](#bash-scripting-interview-questions)
5. [Git and GitHub Interview Questions](#git-and-github-interview-questions)
6. [GitLab CI/CD Interview Questions](#gitlab-cicd-interview-questions)
7. [Azure Interview Questions](#azure-interview-questions)
8. [Terraform Interview Questions](#terraform-interview-questions)
9. [Ansible Interview Questions](#ansible-interview-questions)
10. [Docker Interview Questions](#docker-interview-questions)
11. [Kubernetes Interview Questions](#kubernetes-interview-questions)

---

## 👤 About the Author

**Name:** Jai M  
**Role:** DevOps Architect  
**Experience:**
- Total years in IT: 15 years
- Specialization: DevOps for 7+ years
- Experience in training: 10 years

> 💡 **Note:** The goal of this guide is not to teach you specific answers to every possible question but to provide you with the understanding, strategies, and confidence you need to excel in DevOps interviews.

---

## 📚 Prerequisites

Before diving into the interview questions, ensure you have a solid understanding of:

- **Linux and Bash Scripting**
- **Git and GitHub**
- **GitLab CI/CD**
- **Azure Cloud**
- **Terraform**
- **Ansible**
- **Docker**
- **Kubernetes**

---

## 🐧 Linux Interview Questions

### Scenario 1: SSH Troubleshooting
**Question:** You're unable to SSH into a Linux server. What steps would you take to troubleshoot this?

**Answer:** Follow these systematic steps:

#### 1. Check Server Accessibility
```bash
# Verify if the server is reachable
ping your_server_ip
```
If you see "Request timed out" or "Destination Host Unreachable", there's likely a network issue.

#### 2. Verify SSH Service Status
```bash
# Check if SSH service is running
sudo systemctl status ssh

# If not running, start the service
sudo systemctl start ssh

# Enable SSH to start on boot
sudo systemctl enable ssh
```

#### 3. Check SSH Port Availability
```bash
# Test if port 22 (default SSH port) is open
telnet your_server_ip 22
```
You should see "SSH-2.0-OpenSSH..." if the port is open.

#### 4. Review Firewall Rules
```bash
# Check firewall status (for ufw)
sudo ufw status

# Allow SSH if not already permitted
sudo ufw allow ssh
```

#### 5. Inspect SSH Configuration
```bash
# Review SSH configuration
sudo less /etc/ssh/sshd_config

# After making changes, restart SSH
sudo systemctl restart ssh
```

#### 6. Check System Logs
```bash
# Review authentication logs for errors
sudo tail -n 50 /var/log/auth.log
```

### Scenario 2: Performance Troubleshooting
**Question:** Your Linux server is running slower than usual. What steps can you take to identify and resolve the issue?

**Answer:**
- Examine CPU usage with `top`, `htop`, or `atop`
- Check I/O operations and memory usage
- Look for swapping issues which significantly reduce performance
- Review system logs in `/var/log/` for software or hardware errors

### Scenario 3: File Permission Issues
**Question:** An application is unable to write to a file on a Linux server. How would you diagnose the problem?

**Answer:**
- Check file permissions to ensure the application has write access
- Verify if the file is locked by another process
- Check if the filesystem is read-only due to disk issues

### Scenario 4: Task Automation
**Question:** You need to automate a task to run at a specific time every day. How would you achieve this?

**Answer:** Use cron daemon:
```bash
# Edit crontab
crontab -e

# Add entry (format: minute hour day_of_month month day_of_week command)
0 0 * * * /usr/local/bin/app_backup.sh  # Runs every day at midnight
```

### Scenario 5: Disk Space Management
**Question:** A Linux server is out of disk space. What steps do you take?

**Answer:**
```bash
# Check disk usage by filesystem
df -h

# Find directories consuming most space
du -sh /* | sort -rh | head -10

# Clean up old logs, core dumps, and unnecessary files
```

### Additional Linux Questions

#### File Operations
- **Change file ownership:** `chown user:group filename`
- **Find a file:** `find / -name example.txt`
- **Check memory usage:** `free -h`

#### Network and Security
- **Check open ports:**
  ```bash
  netstat -tulpn
  ss -tulpn
  lsof -i
  ```

- **Set up basic firewall (Red Hat-based):**
  ```bash
  sudo systemctl status firewalld
  sudo firewall-cmd --list-all
  ```

- **Set up basic firewall (Debian-based):**
  ```bash
  sudo ufw enable
  sudo ufw status
  ```

#### Process Management
- **Identify high CPU processes:** `top` or `htop`
- **Adjust process priority:** `nice` and `renice` commands

---

## 🔧 Bash Scripting Interview Questions

### Scenario 1: Resource Monitoring Script
**Question:** Create a script to monitor system resources and send alerts when thresholds are exceeded.

**Answer:**
```bash
#!/bin/bash
# resource_monitor.sh

# Set thresholds
CPU_THRESHOLD=80
MEMORY_THRESHOLD=90
DISK_THRESHOLD=85

# Get current usage
CPU_USAGE=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}' | cut -d'%' -f1)
MEMORY_USAGE=$(free | grep Mem | awk '{print ($3/$2) * 100.0}')
DISK_USAGE=$(df -h / | awk 'NR==2 {print $5}' | sed 's/%//')

# Check and alert
if (( $(echo "$CPU_USAGE > $CPU_THRESHOLD" | bc -l) )); then
    echo "High CPU usage: ${CPU_USAGE}%"
fi

if (( $(echo "$MEMORY_USAGE > $MEMORY_THRESHOLD" | bc -l) )); then
    echo "High memory usage: ${MEMORY_USAGE}%"
fi

if [ "$DISK_USAGE" -gt "$DISK_THRESHOLD" ]; then
    echo "High disk usage: ${DISK_USAGE}%"
fi
```

### Scenario 2: Log Rotation Script
**Question:** Write a script to rotate logs when they exceed a certain size.

**Answer:**
```bash
#!/bin/bash
# log_rotation.sh

LOG_DIR="/var/log/myapp"
MAX_SIZE="100M"  # Maximum size before rotation
KEEP_DAYS=30     # Keep logs for 30 days

# Rotate logs
find "$LOG_DIR" -name "*.log" -size +$MAX_SIZE -exec mv {} {}.$(date +%Y%m%d) \;

# Compress old logs
find "$LOG_DIR" -name "*.log.*" -mtime +1 -exec gzip {} \;

# Delete logs older than KEEP_DAYS
find "$LOG_DIR" -name "*.log.*.gz" -mtime +$KEEP_DAYS -delete
```

### Scenario 3: Backup Automation
**Question:** Create a backup script with error handling and logging.

**Answer:**
```bash
#!/bin/bash
# backup.sh

# Configuration
SOURCE_DIR="/data/important"
BACKUP_DIR="/backup"
LOG_FILE="/var/log/backup.log"
DATE=$(date +%Y%m%d_%H%M%S)

# Function to log messages
log_message() {
    echo "$(date '+%Y-%m-%d %H:%M:%S') - $1" >> "$LOG_FILE"
}

# Error handling
set -e
trap 'log_message "Backup failed at line $LINENO"' ERR

# Start backup
log_message "Starting backup"

# Create backup
tar -czf "$BACKUP_DIR/backup_$DATE.tar.gz" "$SOURCE_DIR"

if [ $? -eq 0 ]; then
    log_message "Backup completed successfully"
else
    log_message "Backup failed"
    exit 1
fi

# Cleanup old backups (keep last 7 days)
find "$BACKUP_DIR" -name "backup_*.tar.gz" -mtime +7 -delete
log_message "Old backups cleaned up"
```

### Additional Bash Tips

#### Variables and Arrays
```bash
# Variable declaration
NAME="DevOps"
readonly CONSTANT="unchangeable"

# Arrays
SERVICES=("nginx" "mysql" "redis")
echo "${SERVICES[0]}"  # Access first element
echo "${#SERVICES[@]}"  # Array length
```

#### Control Structures
```bash
# If-else
if [ -f "$FILE" ]; then
    echo "File exists"
elif [ -d "$FILE" ]; then
    echo "Directory exists"
else
    echo "Not found"
fi

# Loops
for service in "${SERVICES[@]}"; do
    systemctl status "$service"
done

while [ $counter -lt 10 ]; do
    echo $counter
    ((counter++))
done
```

---

## 🔀 Git and GitHub Interview Questions

### Scenario 1: Merge Conflict Resolution
**Question:** How do you resolve a merge conflict between two branches?

**Answer:**
```bash
# Merge branch and encounter conflict
git merge feature-branch

# View conflicted files
git status

# Edit conflicted files manually, then:
git add <resolved-files>
git commit -m "Resolved merge conflict"
```

### Scenario 2: Reverting Changes
**Question:** How would you undo the last commit while keeping the changes?

**Answer:**
```bash
# Soft reset (keeps changes in staging)
git reset --soft HEAD~1

# Mixed reset (keeps changes in working directory)
git reset HEAD~1

# Hard reset (discards all changes - use with caution!)
git reset --hard HEAD~1
```

### Scenario 3: Branch Management
**Question:** Describe a branching strategy for a team project.

**Answer:** **GitFlow Strategy:**
- `main/master`: Production-ready code
- `develop`: Integration branch for features
- `feature/*`: Individual feature branches
- `release/*`: Preparation for production release
- `hotfix/*`: Emergency fixes for production

```bash
# Create and switch to feature branch
git checkout -b feature/new-feature develop

# After development, merge back
git checkout develop
git merge --no-ff feature/new-feature
git branch -d feature/new-feature
```

### Scenario 4: Git History
**Question:** How do you clean up commit history before merging?

**Answer:**
```bash
# Interactive rebase for last 3 commits
git rebase -i HEAD~3

# Squash commits
# In the editor, mark commits to squash with 's'

# Amend last commit message
git commit --amend -m "New commit message"
```

### Advanced Git Operations

#### Cherry-picking
```bash
# Apply specific commit to current branch
git cherry-pick <commit-hash>
```

#### Stashing
```bash
# Save work temporarily
git stash save "Work in progress"

# List stashes
git stash list

# Apply latest stash
git stash apply

# Apply and remove stash
git stash pop
```

#### Tagging
```bash
# Create annotated tag
git tag -a v1.0.0 -m "Release version 1.0.0"

# Push tags
git push origin --tags
```

---

## 🚀 GitLab CI/CD Interview Questions

### Scenario 1: Basic Pipeline Setup
**Question:** Create a basic CI/CD pipeline for a Node.js application.

**Answer:**
```yaml
# .gitlab-ci.yml
stages:
  - build
  - test
  - deploy

variables:
  NODE_VERSION: "14"

before_script:
  - node --version
  - npm --version

build:
  stage: build
  image: node:${NODE_VERSION}
  script:
    - npm ci
    - npm run build
  artifacts:
    paths:
      - dist/
    expire_in: 1 hour

test:
  stage: test
  image: node:${NODE_VERSION}
  script:
    - npm ci
    - npm run test
  coverage: '/Coverage: \d+\.\d+%/'

deploy:
  stage: deploy
  script:
    - echo "Deploying to production"
    - # Add deployment commands
  only:
    - main
  environment:
    name: production
    url: https://myapp.example.com
```

### Scenario 2: Multi-Environment Deployment
**Question:** Set up deployments to different environments based on branch.

**Answer:**
```yaml
# Deploy to different environments
.deploy_template: &deploy_definition
  stage: deploy
  script:
    - echo "Deploying to $CI_ENVIRONMENT_NAME"
    - # Add deployment script

deploy_dev:
  <<: *deploy_definition
  environment:
    name: development
    url: https://dev.example.com
  only:
    - develop

deploy_staging:
  <<: *deploy_definition
  environment:
    name: staging
    url: https://staging.example.com
  only:
    - staging

deploy_prod:
  <<: *deploy_definition
  environment:
    name: production
    url: https://example.com
  only:
    - main
  when: manual
```

### Scenario 3: Docker Build and Push
**Question:** Create a pipeline that builds and pushes Docker images.

**Answer:**
```yaml
variables:
  DOCKER_REGISTRY: registry.gitlab.com
  IMAGE_NAME: $CI_PROJECT_PATH

build_image:
  stage: build
  image: docker:latest
  services:
    - docker:dind
  before_script:
    - docker login -u $CI_REGISTRY_USER -p $CI_REGISTRY_PASSWORD $CI_REGISTRY
  script:
    - docker build -t $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA .
    - docker tag $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA $CI_REGISTRY_IMAGE:latest
    - docker push $CI_REGISTRY_IMAGE:$CI_COMMIT_SHA
    - docker push $CI_REGISTRY_IMAGE:latest
  only:
    - main
```

### Pipeline Optimization

#### Using Cache
```yaml
cache:
  key: ${CI_COMMIT_REF_SLUG}
  paths:
    - node_modules/
    - .npm/

before_script:
  - npm ci --cache .npm --prefer-offline
```

#### Parallel Jobs
```yaml
test:
  stage: test
  parallel:
    matrix:
      - TEST_SUITE: unit
      - TEST_SUITE: integration
      - TEST_SUITE: e2e
  script:
    - npm run test:$TEST_SUITE
```

---

## ☁️ Azure Interview Questions

### Scenario 1: Virtual Network Setup
**Question:** Design a secure network architecture in Azure for a multi-tier application.

**Answer:**
```bash
# Create resource group
az group create --name MyAppRG --location eastus

# Create virtual network
az network vnet create \
  --resource-group MyAppRG \
  --name MyAppVNet \
  --address-prefix 10.0.0.0/16

# Create subnets
az network vnet subnet create \
  --resource-group MyAppRG \
  --vnet-name MyAppVNet \
  --name WebSubnet \
  --address-prefix 10.0.1.0/24

az network vnet subnet create \
  --resource-group MyAppRG \
  --vnet-name MyAppVNet \
  --name AppSubnet \
  --address-prefix 10.0.2.0/24

az network vnet subnet create \
  --resource-group MyAppRG \
  --vnet-name MyAppVNet \
  --name DBSubnet \
  --address-prefix 10.0.3.0/24

# Create Network Security Groups
az network nsg create \
  --resource-group MyAppRG \
  --name WebNSG

# Add NSG rules
az network nsg rule create \
  --resource-group MyAppRG \
  --nsg-name WebNSG \
  --name AllowHTTPS \
  --priority 100 \
  --destination-port-ranges 443 \
  --access Allow \
  --protocol Tcp
```

### Scenario 2: Azure DevOps Pipeline
**Question:** Create an Azure DevOps pipeline for deploying to Azure App Service.

**Answer:**
```yaml
# azure-pipelines.yml
trigger:
  - main

pool:
  vmImage: 'ubuntu-latest'

variables:
  azureSubscription: 'MyAzureSubscription'
  webAppName: 'MyWebApp'
  resourceGroupName: 'MyResourceGroup'

stages:
- stage: Build
  jobs:
  - job: BuildJob
    steps:
    - task: NodeTool@0
      inputs:
        versionSpec: '14.x'
    
    - script: |
        npm install
        npm run build
      displayName: 'Build application'
    
    - task: ArchiveFiles@2
      inputs:
        rootFolderOrFile: '$(Build.SourcesDirectory)'
        includeRootFolder: false
        archiveType: 'zip'
        archiveFile: '$(Build.ArtifactStagingDirectory)/$(Build.BuildId).zip'
    
    - publish: '$(Build.ArtifactStagingDirectory)'
      artifact: drop

- stage: Deploy
  jobs:
  - deployment: DeployWeb
    environment: 'Production'
    strategy:
      runOnce:
        deploy:
          steps:
          - task: AzureWebApp@1
            inputs:
              azureSubscription: $(azureSubscription)
              appType: 'webAppLinux'
              appName: $(webAppName)
              package: '$(Pipeline.Workspace)/drop/*.zip'
```

### Scenario 3: Storage Management
**Question:** Implement a backup strategy using Azure Storage.

**Answer:**
```bash
# Create storage account
az storage account create \
  --name mystorageaccount \
  --resource-group MyRG \
  --location eastus \
  --sku Standard_LRS \
  --kind StorageV2

# Create container for backups
az storage container create \
  --name backups \
  --account-name mystorageaccount \
  --public-access off

# Enable soft delete for blob storage
az storage account blob-service-properties update \
  --account-name mystorageaccount \
  --enable-delete-retention true \
  --delete-retention-days 30

# Set up lifecycle management
cat > lifecycle.json << EOF
{
  "rules": [
    {
      "name": "moveToArchive",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": ["blockBlob"],
          "prefixMatch": ["backups/"]
        },
        "actions": {
          "baseBlob": {
            "tierToArchive": {
              "daysAfterModificationGreaterThan": 90
            },
            "delete": {
              "daysAfterModificationGreaterThan": 365
            }
          }
        }
      }
    }
  ]
}
EOF

az storage account management-policy create \
  --account-name mystorageaccount \
  --policy @lifecycle.json \
  --resource-group MyRG
```

### Azure Key Concepts

#### Identity and Access Management
```bash
# Create service principal
az ad sp create-for-rbac \
  --name MyServicePrincipal \
  --role Contributor \
  --scopes /subscriptions/{subscription-id}/resourceGroups/MyRG

# Assign role to user
az role assignment create \
  --assignee user@example.com \
  --role "Virtual Machine Contributor" \
  --scope /subscriptions/{subscription-id}/resourceGroups/MyRG
```

#### Monitoring and Diagnostics
```bash
# Enable diagnostics for VM
az vm diagnostics set \
  --resource-group MyRG \
  --vm-name MyVM \
  --settings @diagnostic-config.json

# Create alert rule
az monitor metrics alert create \
  --name high-cpu-alert \
  --resource-group MyRG \
  --scopes /subscriptions/{subscription-id}/resourceGroups/MyRG/providers/Microsoft.Compute/virtualMachines/MyVM \
  --condition "avg Percentage CPU > 80" \
  --description "Alert when CPU exceeds 80%"
```

---

## 🏗️ Terraform Interview Questions

### Scenario 1: Multi-Environment Infrastructure
**Question:** Create a Terraform configuration for deploying infrastructure across multiple environments.

**Answer:**
```hcl
# variables.tf
variable "environment" {
  description = "Environment name"
  type        = string
}

variable "instance_count" {
  description = "Number of instances"
  type        = map(number)
  default = {
    dev     = 1
    staging = 2
    prod    = 5
  }
}

# main.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
  
  backend "s3" {
    bucket = "terraform-state-bucket"
    key    = "${var.environment}/terraform.tfstate"
    region = "us-east-1"
  }
}

provider "aws" {
  region = var.region
}

# VPC Module
module "vpc" {
  source = "./modules/vpc"
  
  environment   = var.environment
  vpc_cidr      = var.vpc_cidrs[var.environment]
  subnet_count  = var.instance_count[var.environment]
}

# EC2 Instances
resource "aws_instance" "app_server" {
  count = var.instance_count[var.environment]
  
  ami           = data.aws_ami.latest_amazon_linux.id
  instance_type = var.instance_types[var.environment]
  subnet_id     = module.vpc.subnet_ids[count.index]
  
  tags = {
    Name        = "${var.environment}-app-${count.index + 1}"
    Environment = var.environment
  }
}

# outputs.tf
output "instance_ips" {
  value = aws_instance.app_server[*].public_ip
}
```

### Scenario 2: State Management
**Question:** How do you handle Terraform state in a team environment?

**Answer:**
```hcl
# backend.tf - Remote state configuration
terraform {
  backend "s3" {
    bucket         = "company-terraform-state"
    key            = "infrastructure/terraform.tfstate"
    region         = "us-east-1"
    dynamodb_table = "terraform-state-lock"
    encrypt        = true
  }
}

# Create DynamoDB table for state locking
resource "aws_dynamodb_table" "terraform_lock" {
  name           = "terraform-state-lock"
  billing_mode   = "PAY_PER_REQUEST"
  hash_key       = "LockID"
  
  attribute {
    name = "LockID"
    type = "S"
  }
  
  tags = {
    Name = "Terraform State Lock Table"
  }
}
```

### Scenario 3: Module Creation
**Question:** Create a reusable Terraform module for deploying a web application.

**Answer:**
```hcl
# modules/web-app/variables.tf
variable "app_name" {
  description = "Application name"
  type        = string
}

variable "instance_type" {
  description = "EC2 instance type"
  type        = string
  default     = "t3.micro"
}

variable "min_size" {
  description = "Minimum number of instances"
  type        = number
  default     = 2
}

variable "max_size" {
  description = "Maximum number of instances"
  type        = number
  default     = 10
}

# modules/web-app/main.tf
# Application Load Balancer
resource "aws_lb" "app_lb" {
  name               = "${var.app_name}-lb"
  internal           = false
  load_balancer_type = "application"
  subnets            = var.public_subnet_ids
  
  security_groups = [aws_security_group.lb_sg.id]
  
  tags = {
    Name = "${var.app_name}-lb"
  }
}

# Auto Scaling Group
resource "aws_autoscaling_group" "app_asg" {
  name                = "${var.app_name}-asg"
  vpc_zone_identifier = var.private_subnet_ids
  min_size            = var.min_size
  max_size            = var.max_size
  desired_capacity    = var.min_size
  
  launch_template {
    id      = aws_launch_template.app_lt.id
    version = "$Latest"
  }
  
  target_group_arns = [aws_lb_target_group.app_tg.arn]
  
  tag {
    key                 = "Name"
    value               = "${var.app_name}-instance"
    propagate_at_launch = true
  }
}

# Launch Template
resource "aws_launch_template" "app_lt" {
  name_prefix   = "${var.app_name}-"
  image_id      = data.aws_ami.latest_amazon_linux.id
  instance_type = var.instance_type
  
  vpc_security_group_ids = [aws_security_group.app_sg.id]
  
  user_data = base64encode(templatefile("${path.module}/user_data.sh", {
    app_name = var.app_name
  }))
}

# modules/web-app/outputs.tf
output "load_balancer_dns" {
  value = aws_lb.app_lb.dns_name
}

output "autoscaling_group_name" {
  value = aws_autoscaling_group.app_asg.name
}
```

### Terraform Best Practices

#### Resource Dependencies
```hcl
# Explicit dependency
resource "aws_instance" "app" {
  # ... configuration ...
  
  depends_on = [aws_db_instance.database]
}

# Implicit dependency
resource "aws_instance" "app" {
  subnet_id = aws_subnet.private.id  # Implicit dependency
}
```

#### Dynamic Blocks
```hcl
resource "aws_security_group" "dynamic_sg" {
  name = "dynamic-sg"
  
  dynamic "ingress" {
    for_each = var.ingress_rules
    content {
      from_port   = ingress.value.from_port
      to_port     = ingress.value.to_port
      protocol    = ingress.value.protocol
      cidr_blocks = ingress.value.cidr_blocks
    }
  }
}
```

---

## 🔧 Ansible Interview Questions

### Scenario 1: Playbook for Web Server Setup
**Question:** Create an Ansible playbook to set up a web server with Nginx.

**Answer:**
```yaml
---
# webserver-setup.yml
- name: Configure Web Servers
  hosts: webservers
  become: yes
  vars:
    nginx_port: 80
    app_root: /var/www/html
    
  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
        cache_valid_time: 3600
      when: ansible_os_family == "Debian"
      
    - name: Install Nginx
      package:
        name: nginx
        state: present
        
    - name: Create application directory
      file:
        path: "{{ app_root }}"
        state: directory
        owner: www-data
        group: www-data
        mode: '0755'
        
    - name: Deploy application files
      copy:
        src: app/
        dest: "{{ app_root }}/"
        owner: www-data
        group: www-data
        
    - name: Configure Nginx site
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/default
      notify: restart nginx
      
    - name: Enable and start Nginx
      systemd:
        name: nginx
        enabled: yes
        state: started
        
  handlers:
    - name: restart nginx
      systemd:
        name: nginx
        state: restarted
```

### Scenario 2: Role-Based Configuration
**Question:** Create an Ansible role for database setup.

**Answer:**
```yaml
# roles/mysql/tasks/main.yml
---
- name: Install MySQL packages
  package:
    name:
      - mysql-server
      - python3-pymysql
    state: present
    
- name: Start and enable MySQL
  systemd:
    name: mysql
    state: started
    enabled: yes
    
- name: Set root password
  mysql_user:
    name: root
    password: "{{ mysql_root_password }}"
    login_unix_socket: /var/run/mysqld/mysqld.sock
    
- name: Remove anonymous users
  mysql_user:
    name: ''
    host_all: yes
    state: absent
    login_user: root
    login_password: "{{ mysql_root_password }}"
    
- name: Create application database
  mysql_db:
    name: "{{ mysql_database }}"
    state: present
    login_user: root
    login_password: "{{ mysql_root_password }}"
    
- name: Create application user
  mysql_user:
    name: "{{ mysql_user }}"
    password: "{{ mysql_password }}"
    priv: "{{ mysql_database }}.*:ALL"
    state: present
    login_user: root
    login_password: "{{ mysql_root_password }}"

# roles/mysql/handlers/main.yml
---
- name: restart mysql
  systemd:
    name: mysql
    state: restarted

# roles/mysql/defaults/main.yml
---
mysql_root_password: "changeme"
mysql_database: "app_db"
mysql_user: "app_user"
mysql_password: "app_password"
```

### Scenario 3: Dynamic Inventory
**Question:** Use dynamic inventory with AWS EC2.

**Answer:**
```yaml
# aws_ec2.yml - Dynamic inventory configuration
plugin: aws_ec2
regions:
  - us-east-1
  - us-west-2
keyed_groups:
  - key: tags.Environment
    prefix: env
  - key: tags.Application
    prefix: app
filters:
  tag:Environment:
    - production
    - staging
hostnames:
  - dns-name
  - private-ip-address
compose:
  ansible_host: public_dns_name

# Use with: ansible-inventory -i aws_ec2.yml --graph
```

### Ansible Vault
```bash
# Create encrypted file
ansible-vault create secrets.yml

# Encrypt existing file
ansible-vault encrypt vars.yml

# Edit encrypted file
ansible-vault edit secrets.yml

# Use in playbook
ansible-playbook site.yml --ask-vault-pass
# Or with vault password file
ansible-playbook site.yml --vault-password-file ~/.vault_pass
```

### Advanced Ansible Features

#### Custom Modules
```python
#!/usr/bin/python
# library/check_service.py

from ansible.module_utils.basic import AnsibleModule
import subprocess

def check_service_status(service_name):
    try:
        result = subprocess.run(
            ['systemctl', 'is-active', service_name],
            capture_output=True,
            text=True
        )
        return result.stdout.strip() == 'active'
    except Exception as e:
        return False

def main():
    module = AnsibleModule(
        argument_spec=dict(
            name=dict(type='str', required=True)
        )
    )
    
    service_name = module.params['name']
    is_active = check_service_status(service_name)
    
    module.exit_json(
        changed=False,
        active=is_active,
        service=service_name
    )

if __name__ == '__main__':
    main()
```

---

## 🐳 Docker Interview Questions

### Scenario 1: Dockerfile Optimization
**Question:** Create an optimized Dockerfile for a Node.js application.

**Answer:**
```dockerfile
# Multi-stage build for optimization
FROM node:14-alpine AS builder

# Set working directory
WORKDIR /app

# Copy package files
COPY package*.json ./

# Install dependencies
RUN npm ci --only=production

# Copy application code
COPY . .

# Build application
RUN npm run build

# Production stage
FROM node:14-alpine

# Install dumb-init for proper signal handling
RUN apk add --no-cache dumb-init

# Create non-root user
RUN addgroup -g 1001 -S nodejs && \
    adduser -S nodejs -u 1001

# Set working directory
WORKDIR /app

# Copy built application from builder stage
COPY --from=builder --chown=nodejs:nodejs /app/dist ./dist
COPY --from=builder --chown=nodejs:nodejs /app/node_modules ./node_modules
COPY --from=builder --chown=nodejs:nodejs /app/package*.json ./

# Switch to non-root user
USER nodejs

# Expose port
EXPOSE 3000

# Use dumb-init to handle signals properly
ENTRYPOINT ["dumb-init", "--"]

# Start application
CMD ["node", "dist/index.js"]
```

### Scenario 2: Docker Compose Setup
**Question:** Create a Docker Compose setup for a full-stack application.

**Answer:**
```yaml
# docker-compose.yml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "3000:3000"
    environment:
      - REACT_APP_API_URL=http://backend:8080
    depends_on:
      - backend
    networks:
      - app-network

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "8080:8080"
    environment:
      - DATABASE_URL=postgresql://user:password@postgres:5432/appdb
      - REDIS_URL=redis://redis:6379
    depends_on:
      - postgres
      - redis
    networks:
      - app-network
    volumes:
      - ./backend/uploads:/app/uploads

  postgres:
    image: postgres:13-alpine
    environment:
      - POSTGRES_DB=appdb
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:6-alpine
    command: redis-server --appendonly yes
    volumes:
      - redis_data:/data
    networks:
      - app-network

  nginx:
    image: nginx:alpine
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - frontend
      - backend
    networks:
      - app-network

volumes:
  postgres_data:
  redis_data:

networks:
  app-network:
    driver: bridge
```

### Scenario 3: Container Security
**Question:** Implement security best practices for Docker containers.

**Answer:**
```dockerfile
# Secure Dockerfile
FROM alpine:3.14

# Update and patch
RUN apk update && \
    apk upgrade && \
    apk add --no-cache \
    ca-certificates \
    && rm -rf /var/cache/apk/*

# Create non-root user
RUN addgroup -S appgroup && \
    adduser -S appuser -G appgroup

# Set up application directory
WORKDIR /app

# Copy application with correct ownership
COPY --chown=appuser:appgroup app /app

# Drop capabilities
RUN setcap -r /app/binary || true

# Security labels
LABEL security.scan="true" \
      security.vulnerabilities="none"

# Health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080/health || exit 1

# Switch to non-root user
USER appuser

# Read-only root filesystem
# Use with: docker run --read-only --tmpfs /tmp

# No new privileges
# Use with: docker run --security-opt=no-new-privileges

EXPOSE 8080
CMD ["/app/binary"]
```

### Docker Commands Cheat Sheet
```bash
# Container Management
docker run -d --name myapp -p 8080:80 nginx
docker stop myapp
docker start myapp
docker restart myapp
docker rm myapp
docker logs -f myapp
docker exec -it myapp bash

# Image Management
docker build -t myapp:latest .
docker push myregistry/myapp:latest
docker pull myregistry/myapp:latest
docker tag myapp:latest myapp:v1.0
docker rmi myapp:latest
docker prune -a  # Remove unused images

# Volume Management
docker volume create mydata
docker volume ls
docker volume inspect mydata
docker volume rm mydata

# Network Management
docker network create mynetwork
docker network ls
docker network inspect mynetwork
docker network connect mynetwork mycontainer

# Docker System
docker system df  # Show docker disk usage
docker system prune -a  # Clean up everything
docker stats  # Show container resource usage
```

---

## ☸️ Kubernetes Interview Questions

### Scenario 1: Application Deployment
**Question:** Deploy a scalable web application with persistent storage in Kubernetes.

**Answer:**
```yaml
# namespace.yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-app

---
# configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: app-config
  namespace: my-app
data:
  database_host: "postgres-service"
  cache_host: "redis-service"

---
# secret.yaml
apiVersion: v1
kind: Secret
metadata:
  name: app-secrets
  namespace: my-app
type: Opaque
stringData:
  database_password: "supersecret"
  api_key: "myapikey"

---
# persistent-volume.yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: app-storage
  namespace: my-app
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: standard
  resources:
    requests:
      storage: 10Gi

---
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-app
  namespace: my-app
  labels:
    app: web-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-app
  template:
    metadata:
      labels:
        app: web-app
    spec:
      containers:
      - name: app
        image: myregistry/web-app:latest
        ports:
        - containerPort: 8080
        env:
        - name: DATABASE_HOST
          valueFrom:
            configMapKeyRef:
              name: app-config
              key: database_host
        - name: DATABASE_PASSWORD
          valueFrom:
            secretKeyRef:
              name: app-secrets
              key: database_password
        resources:
          requests:
            memory: "256Mi"
            cpu: "250m"
          limits:
            memory: "512Mi"
            cpu: "500m"
        volumeMounts:
        - name: app-storage
          mountPath: /data
        livenessProbe:
          httpGet:
            path: /health
            port: 8080
          initialDelaySeconds: 30
          periodSeconds: 10
        readinessProbe:
          httpGet:
            path: /ready
            port: 8080
          initialDelaySeconds: 5
          periodSeconds: 5
      volumes:
      - name: app-storage
        persistentVolumeClaim:
          claimName: app-storage

---
# service.yaml
apiVersion: v1
kind: Service
metadata:
  name: web-app-service
  namespace: my-app
spec:
  selector:
    app: web-app
  ports:
  - port: 80
    targetPort: 8080
  type: LoadBalancer

---
# hpa.yaml
apiVersion: autoscaling/v2
kind: HorizontalPodAutoscaler
metadata:
  name: web-app-hpa
  namespace: my-app
spec:
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: web-app
  minReplicas: 3
  maxReplicas: 10
  metrics:
  - type: Resource
    resource:
      name: cpu
      target:
        type: Utilization
        averageUtilization: 70
  - type: Resource
    resource:
      name: memory
      target:
        type: Utilization
        averageUtilization: 80
```

### Scenario 2: Ingress and TLS Configuration
**Question:** Set up ingress with TLS termination for multiple services.

**Answer:**
```yaml
# cert-manager-issuer.yaml
apiVersion: cert-manager.io/v1
kind: ClusterIssuer
metadata:
  name: letsencrypt-prod
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: admin@example.com
    privateKeySecretRef:
      name: letsencrypt-prod
    solvers:
    - http01:
        ingress:
          class: nginx

---
# ingress.yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: app-ingress
  namespace: my-app
  annotations:
    kubernetes.io/ingress.class: nginx
    cert-manager.io/cluster-issuer: letsencrypt-prod
    nginx.ingress.kubernetes.io/rewrite-target: /
    nginx.ingress.kubernetes.io/ssl-redirect: "true"
spec:
  tls:
  - hosts:
    - app.example.com
    - api.example.com
    secretName: app-tls
  rules:
  - host: app.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend-service
            port:
              number: 80
  - host: api.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: backend-service
            port:
              number: 8080
```

### Scenario 3: StatefulSet for Database
**Question:** Deploy a stateful database with persistent storage.

**Answer:**
```yaml
# statefulset.yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: postgres
  namespace: my-app
spec:
  serviceName: postgres-headless
  replicas: 3
  selector:
    matchLabels:
      app: postgres
  template:
    metadata:
      labels:
        app: postgres
    spec:
      containers:
      - name: postgres
        image: postgres:13
        ports:
        - containerPort: 5432
        env:
        - name: POSTGRES_DB
          value: myapp
        - name: POSTGRES_USER
          value: postgres
        - name: POSTGRES_PASSWORD
          valueFrom:
            secretKeyRef:
              name: postgres-secret
              key: password
        - name: PGDATA
          value: /var/lib/postgresql/data/pgdata
        volumeMounts:
        - name: postgres-storage
          mountPath: /var/lib/postgresql/data
        resources:
          requests:
            memory: "1Gi"
            cpu: "500m"
          limits:
            memory: "2Gi"
            cpu: "1000m"
  volumeClaimTemplates:
  - metadata:
      name: postgres-storage
    spec:
      accessModes: ["ReadWriteOnce"]
      storageClassName: "fast-ssd"
      resources:
        requests:
          storage: 20Gi

---
# headless-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: postgres-headless
  namespace: my-app
spec:
  clusterIP: None
  selector:
    app: postgres
  ports:
  - port: 5432
    targetPort: 5432
```

### Kubernetes RBAC
```yaml
# rbac.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: app-service-account
  namespace: my-app

---
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  name: app-role
  namespace: my-app
rules:
- apiGroups: [""]
  resources: ["pods", "services"]
  verbs: ["get", "list", "watch"]
- apiGroups: ["apps"]
  resources: ["deployments"]
  verbs: ["get", "list", "watch", "update", "patch"]

---
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: app-rolebinding
  namespace: my-app
subjects:
- kind: ServiceAccount
  name: app-service-account
  namespace: my-app
roleRef:
  kind: Role
  name: app-role
  apiGroup: rbac.authorization.k8s.io
```

### Kubernetes Commands Cheat Sheet
```bash
# Cluster Management
kubectl cluster-info
kubectl get nodes
kubectl describe node node-name

# Namespace Operations
kubectl create namespace my-app
kubectl get namespaces
kubectl delete namespace my-app

# Deployment Operations
kubectl apply -f deployment.yaml
kubectl get deployments -n my-app
kubectl describe deployment web-app -n my-app
kubectl scale deployment web-app --replicas=5 -n my-app
kubectl rollout status deployment web-app -n my-app
kubectl rollout history deployment web-app -n my-app
kubectl rollout undo deployment web-app -n my-app

# Pod Operations
kubectl get pods -n my-app
kubectl describe pod pod-name -n my-app
kubectl logs pod-name -n my-app
kubectl logs -f pod-name -c container-name -n my-app
kubectl exec -it pod-name -n my-app -- /bin/bash
kubectl port-forward pod-name 8080:80 -n my-app

# Service Operations
kubectl get services -n my-app
kubectl describe service service-name -n my-app
kubectl expose deployment web-app --port=80 --type=LoadBalancer -n my-app

# ConfigMap and Secret
kubectl create configmap app-config --from-file=config.yaml -n my-app
kubectl create secret generic app-secret --from-literal=password=secret -n my-app
kubectl get configmaps -n my-app
kubectl get secrets -n my-app

# Debugging
kubectl get events -n my-app
kubectl top nodes
kubectl top pods -n my-app
kubectl describe pod pod-name -n my-app
kubectl get pod pod-name -o yaml -n my-app
```

---

## 🎯 Interview Tips

### Technical Preparation
1. **Hands-on Practice**: Set up a home lab or use cloud providers' free tiers
2. **Understand Concepts**: Don't just memorize commands, understand the underlying concepts
3. **Real-world Scenarios**: Practice solving real-world problems
4. **Stay Updated**: Keep up with the latest tools and best practices

### During the Interview
1. **Think Aloud**: Explain your thought process while solving problems
2. **Ask Questions**: Clarify requirements before jumping into solutions
3. **Consider Trade-offs**: Discuss pros and cons of different approaches
4. **Admit Unknowns**: It's okay to say "I don't know, but here's how I would find out"

### Common Topics to Review
- **CI/CD Pipelines**: Jenkins, GitLab CI, GitHub Actions, Azure DevOps
- **Container Orchestration**: Kubernetes, Docker Swarm, ECS
- **Infrastructure as Code**: Terraform, CloudFormation, ARM Templates
- **Configuration Management**: Ansible, Puppet, Chef
- **Monitoring**: Prometheus, Grafana, ELK Stack, DataDog
- **Cloud Platforms**: AWS, Azure, GCP
- **Scripting**: Bash, Python, PowerShell
- **Version Control**: Git workflows, branching strategies
- **Security**: DevSecOps practices, vulnerability scanning, secrets management
- **Networking**: TCP/IP, DNS, Load Balancing, CDN

---

## 📚 Additional Resources

### Online Platforms
- **Kubernetes Documentation**: https://kubernetes.io/docs/
- **Docker Documentation**: https://docs.docker.com/
- **Terraform Registry**: https://registry.terraform.io/
- **Ansible Galaxy**: https://galaxy.ansible.com/
- **Azure Documentation**: https://docs.microsoft.com/azure/

### Hands-on Learning
- **Katacoda**: Interactive learning platform
- **Play with Docker**: Free Docker playground
- **Play with Kubernetes**: Free K8s playground
- **Linux Academy**: Comprehensive DevOps courses
- **A Cloud Guru**: Cloud and DevOps training

### Books
- "The DevOps Handbook" by Gene Kim, Patrick Debois, John Willis
- "Site Reliability Engineering" by Google
- "Kubernetes in Action" by Marko Lukša
- "Terraform: Up & Running" by Yevgeniy Brikman

---

## 🤝 Contributing

Feel free to contribute to this guide by:
- Adding new scenarios and solutions
- Updating outdated information
- Fixing errors or typos
- Adding explanations for complex topics

---

## 📝 License

This guide is provided as-is for educational purposes. Feel free to use and share it with attribution.

---

**Good luck with your DevOps journey! 🚀**

Remember: DevOps is not just about tools, it's about culture, collaboration, and continuous improvement.
