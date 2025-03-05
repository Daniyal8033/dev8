# Terraform Configuration for Azure Infrastructure

provider "azurerm" {
  features {}
}

resource "azurerm_resource_group" "example" {
  name     = "example-rg"
  location = "East US"
}

resource "azurerm_storage_account" "example" {
  name                     = "examplestoragedevops"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}

resource "azurerm_key_vault" "example" {
  name                        = "example-keyvault"
  resource_group_name         = azurerm_resource_group.example.name
  location                    = azurerm_resource_group.example.location
  tenant_id                   = "<your-tenant-id>"
  sku_name                    = "standard"
}

resource "azurerm_kubernetes_cluster" "example" {
  name                = "example-aks"
  location            = azurerm_resource_group.example.location
  resource_group_name = azurerm_resource_group.example.name
  dns_prefix          = "exampleaks"

  default_node_pool {
    name       = "default"
    node_count = 2
    vm_size    = "Standard_DS2_v2"
  }

  identity {
    type = "SystemAssigned"
  }
}

output "resource_group_name" {
  value = azurerm_resource_group.example.name
}

output "storage_account_name" {
  value = azurerm_storage_account.example.name
}

output "key_vault_name" {
  value = azurerm_key_vault.example.name
}

output "aks_cluster_name" {
  value = azurerm_kubernetes_cluster.example.name
}

# Azure DevOps CI/CD Pipeline YAML
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: TerraformInstaller@1
  displayName: 'Install Terraform'
  inputs:
    terraformVersion: '1.4.6'

- script: terraform init
  displayName: 'Terraform Init'

- script: terraform validate
  displayName: 'Terraform Validate'

- script: terraform plan -out=tfplan
  displayName: 'Terraform Plan'

- script: terraform apply -auto-approve tfplan
  displayName: 'Terraform Apply'

# README.md
# Terraform and Azure DevOps CI/CD Pipeline

## Overview
This project demonstrates Infrastructure as Code (IaC) using **Terraform** to deploy **Azure cloud resources** and an **Azure DevOps CI/CD pipeline** to automate deployments.

## Features
- **Deploys an Azure Resource Group, Storage Account, Key Vault, and AKS cluster** using Terraform.
- **Automates deployment using an Azure DevOps pipeline**.
- **Ensures security and best practices** with RBAC and Key Vault integration.

## Prerequisites
- Azure Subscription
- Terraform CLI installed
- Azure DevOps account
- GitHub repository for version control

## Deployment Steps
### 1. Clone the Repository
```sh
git clone https://github.com/yourusername/terraform-azure-devops.git
cd terraform-azure-devops
```
### 2. Configure Azure Authentication
```sh
az login
az account set --subscription "<your-subscription-id>"
```
### 3. Initialize and Deploy with Terraform
```sh
terraform init
terraform plan -out=tfplan
terraform apply -auto-approve tfplan
```
### 4. Set Up Azure DevOps Pipeline
- Navigate to Azure DevOps.
- Create a new pipeline linked to your **GitHub repo**.
- Use `azure-pipelines.yml` to automate Terraform deployment.

## Outputs
- **Resource Group Name**
- **Storage Account Name**
- **Key Vault Name**
- **AKS Cluster Name**

## License
MIT License - Free to use and modify.
