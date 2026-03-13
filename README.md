# Azure CLI VM Deployment Practice (WSL)

## Overview
This lab documents the commands used to deploy networking and a Virtual Machine in Microsoft Azure using Azure CLI. The goal was to understand the dependency flow between Azure resources such as Resource Groups, VNets, Subnets, NSGs, NICs, and Virtual Machines.

Environment used:
- Azure CLI
- WSL (Windows Subsystem for Linux)
- Ubuntu image

---

# 1. Login to Azure

```bash
az login
```

Authenticates the CLI session with the Azure account.

---

# 2. Create Resource Group

```bash
az group create --name rg-prod-VM2 --location westus2
```

Resource Groups act as logical containers for Azure resources.

Verify:

```bash
az group list --output table
```

---

# 3. Create Virtual Network

```bash
az network vnet create \
--resource-group rg-prod-VM \
--name vnet-prod \
--address-prefix 10.0.0.0/18
```

Purpose:
Creates the main Virtual Network where resources will be deployed.

Verify:

```bash
az network vnet list --output table
```

---

# 4. Create Subnet

```bash
az network vnet subnet create \
--resource-group rg-prod-VM \
--vnet-name vnet-prod \
--name subnet-web-dev \
--address-prefix 10.0.1.0/24
```

Purpose:
Subnets divide a VNet into smaller network segments.

Verify:

```bash
az network vnet subnet list -g rg-prod-VM --vnet-name vnet-prod --output table
```

---

# 5. Create Network Security Group (NSG)

```bash
az network nsg create \
--resource-group rg-prod-VM \
--name nsg-web
```

Purpose:
NSG controls inbound and outbound traffic to Azure resources.

---

# 6. Create Security Rules

Allow SSH access:

```bash
az network nsg rule create \
--resource-group rg-prod-VM \
--nsg-name nsg-web \
--name allow-ssh \
--priority 1000 \
--destination-port-ranges 22 \
--access Allow \
--protocol Tcp
```

Allow HTTP access:

```bash
az network nsg rule create \
--resource-group rg-prod-VM \
--nsg-name nsg-web \
--name allow-http \
--priority 1100 \
--destination-port-ranges 80 \
--access Allow \
--protocol Tcp
```

---

# 7. Create Network Interface (NIC)

```bash
az network nic create \
--resource-group rg-prod-VM \
--name vm-nic \
--vnet-name vnet-prod \
--subnet subnet-web-dev \
--network-security-group nsg-web \
--public-ip-address vm-public-ip
```

Purpose:
NIC connects the Virtual Machine to the Virtual Network.

Components attached:

- Subnet
- Network Security Group
- Public IP

---

# 8. Create Virtual Machine

```bash
az vm create \
--resource-group rg-prod-VM \
--name VMbycli \
--image Ubuntu2204 \
--size Standard_D2s_v3 \
--admin-username siddhesh09 \
--admin-password 'Siddhesh@123' \
--authentication-type password \
--public-ip-sku Standard \
--os-disk-size-gb 30
```

Purpose:
Deploys an Ubuntu Virtual Machine with:

- 2 vCPUs
- Standard SSD disk
- Public IP

---

# Resource Dependency Flow

Azure resources must be created in the following order:

1. Login
2. Resource Group
3. Virtual Network
4. Subnet
5. Network Security Group
6. NSG Rules
7. Network Interface
8. Virtual Machine


Diagram:

```
Resource Group
      |
      v
Virtual Network
      |
      v
Subnet
      |
      v
Network Security Group
      |
      v
Network Interface
      |
      v
Virtual Machine
```

---

# Useful Debug Commands

Check VMs:

```bash
az vm list --output table
```

Check VNets:

```bash
az network vnet list --output table
```

Check NICs:

```bash
az network nic list --output table
```

Check NSGs:

```bash
az network nsg list --output table
```

---

# Cleanup (Delete Resource Group)

To delete all resources created in the lab:

```bash
az group delete --name rg-prod-VM --yes --no-wait
```

This removes all associated resources including VM, disks, NICs, and networking.

---

# Learning Outcome

This lab helped understand:

- Azure CLI workflow
- Networking dependencies
- VM provisioning
- Security configuration using NSG

---

# Generic Practice Commands (Editable Template)

If you want to practice this lab yourself, replace the placeholder values below with your own names.

Example placeholders:

- `<RESOURCE_GROUP>`
- `<VNET_NAME>`
- `<SUBNET_NAME>`
- `<NSG_NAME>`
- `<VM_NAME>`
- `<USERNAME>`

---

## 1. Login

```bash
az login
```

---

## 2. Create Resource Group

```bash
az group create --name <RESOURCE_GROUP> --location westus2
```

Example:

```bash
az group create --name rg-demo --location westus2
```

---

## 3. Create Virtual Network

```bash
az network vnet create \
--resource-group <RESOURCE_GROUP> \
--name <VNET_NAME> \
--address-prefix 10.0.0.0/16
```

---

## 4. Create Subnet

```bash
az network vnet subnet create \
--resource-group <RESOURCE_GROUP> \
--vnet-name <VNET_NAME> \
--name <SUBNET_NAME> \
--address-prefix 10.0.1.0/24
```

---

## 5. Create Network Security Group

```bash
az network nsg create \
--resource-group <RESOURCE_GROUP> \
--name <NSG_NAME>
```

---

## 6. Allow SSH Rule

```bash
az network nsg rule create \
--resource-group <RESOURCE_GROUP> \
--nsg-name <NSG_NAME> \
--name allow-ssh \
--priority 1000 \
--destination-port-ranges 22 \
--access Allow \
--protocol Tcp
```

---

## 7. Create Network Interface

```bash
az network nic create \
--resource-group <RESOURCE_GROUP> \
--name <NIC_NAME> \
--vnet-name <VNET_NAME> \
--subnet <SUBNET_NAME> \
--network-security-group <NSG_NAME>
```

---

## 8. Create Virtual Machine

```bash
az vm create \
--resource-group <RESOURCE_GROUP> \
--name <VM_NAME> \
--image Ubuntu2204 \
--size Standard_B2s \
--admin-username <USERNAME> \
--generate-ssh-keys
```

---

## Cleanup

Delete the entire lab environment:

```bash
az group delete --name <RESOURCE_GROUP> --yes --no-wait
```

---

# Base Azure CLI Commands (Quick Reference)

These commands show the **basic syntax** with simple dummy values so beginners can quickly understand the structure before customizing.

## Login

```bash
az login
```

## Create Resource Group

```bash
az group create --name rg-demo --location eastus
```

## List Resource Groups

```bash
az group list --output table
```

## Create Virtual Network

```bash
az network vnet create \
--resource-group rg-demo \
--name vnet-demo \
--address-prefix 10.0.0.0/16
```

## Create Subnet

```bash
az network vnet subnet create \
--resource-group rg-demo \
--vnet-name vnet-demo \
--name subnet-demo \
--address-prefix 10.0.1.0/24
```

## Create Network Security Group

```bash
az network nsg create \
--resource-group rg-demo \
--name nsg-demo
```

## Create NSG Rule (Allow SSH)

```bash
az network nsg rule create \
--resource-group rg-demo \
--nsg-name nsg-demo \
--name allow-ssh \
--priority 1000 \
--destination-port-ranges 22 \
--access Allow \
--protocol Tcp
```

## Create NSG Rule (Allow HTTP - Port 80)

```bash
az network nsg rule create \
--resource-group rg-demo \
--nsg-name nsg-demo \
--name allow-http \
--priority 1100 \
--destination-port-ranges 80 \
--access Allow \
--protocol Tcp


## Create Public IP

```bash
az network public-ip create \
--resource-group rg-demo \
--name public-ip-demo
```

## Create Network Interface

```bash
az network nic create \
--resource-group rg-demo \
--name nic-demo \
--vnet-name vnet-demo \
--subnet subnet-demo \
--network-security-group nsg-demo \
--public-ip-address public-ip-demo
```

## Create Virtual Machine

```bash
az vm create \
--resource-group rg-demo \
--name vm-demo \
--image Ubuntu2204 \
--admin-username azureuser \
--generate-ssh-keys
```

## Connect to Virtual Machine

```bash
ssh azureuser@<public-ip>
```

---

# Delete Entire Lab Environment

To remove **all resources at once**, delete the resource group:

```bash
az group delete --name rg-demo --yes --no-wait
```

This will delete:

- Virtual Machine
- Virtual Network
- Subnets
- Network Interfaces
- Public IP
- Network Security Groups
- Disks

Everything inside the resource group will be removed.

---

# Author

Siddhesh Khanorkar

