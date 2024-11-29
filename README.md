[![Deploy Infrastructure](https://github.com/madilshahzad/infrastructure-provisioning/actions/workflows/infrastructure-provisioning.yaml/badge.svg)](https://github.com/madilshahzad/infrastructure-provisioning/actions/workflows/infrastructure-provisioning.yaml)
# Infrastructure as Code (IaC) with Terraform

This repository contains the Terraform configuration files for provisioning infrastructure. The workflow includes scanning the infrastructure code using `tfsec` and managing sensitive parameters through GitHub Actions secrets. The Terraform state is stored in an S3 bucket, with DynamoDB used for state locking.

## Workflow Overview

1. **Terraform Initialization**:
    - Initialize the Terraform configuration with the backend configuration pointing to the S3 bucket and DynamoDB table.

2. **Terraform Plan**:
    - Generate an execution plan to preview the changes that Terraform will make to the infrastructure.

3. **Terraform Apply**:
    - Apply the changes required to reach the desired state of the configuration.

4. **Security Scanning**:
    - Use `tfsec` to scan the Terraform code for security issues.

## Prerequisites

- Terraform installed on your local machine.
- AWS CLI configured with appropriate permissions.
- GitHub repository with secrets configured for sensitive parameters.

## Backend Configuration

The Terraform state is stored in an S3 bucket, and DynamoDB is used for state locking to ensure consistency.

```hcl
terraform {
  backend "s3" {
     bucket         = "your-s3-bucket-name"
     key            = "path/to/your/terraform.tfstate"
     region         = "your-region"
     dynamodb_table = "your-dynamodb-table"
  }
}
```

## GitHub Actions Workflow

The GitHub Actions workflow automates the process of provisioning infrastructure and scanning for security issues.

```yaml
name: Terraform Workflow

on:
  push:
     branches:
        - main

jobs:
  terraform:
     runs-on: ubuntu-latest

     steps:
     - name: Checkout code
        uses: actions/checkout@v2

     - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1

     - name: Terraform Init
        run: terraform init

     - name: Terraform Plan
        run: terraform plan -var-file="path/to/your/variables.tfvars"

     - name: Terraform Apply
        if: github.ref == 'refs/heads/main'
        run: terraform apply -auto-approve -var-file="path/to/your/variables.tfvars"

     - name: Run tfsec
        uses: aquasecurity/tfsec-action@v1
```

## Usage

1. **Initialize Terraform**:
    ```sh
    terraform init
    ```

2. **Plan the Infrastructure**:
    ```sh
    terraform plan -var-file="path/to/your/variables.tfvars"
    ```

3. **Apply the Changes**:
    ```sh
    terraform apply -var-file="path/to/your/variables.tfvars"
    ```

## Security Scanning

Run `tfsec` to scan the Terraform code for security issues:
```sh
tfsec .
```

## Permissions

Only DevOps team members are authorized to make changes to the infrastructure.
