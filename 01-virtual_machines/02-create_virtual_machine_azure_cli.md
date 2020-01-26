# Azure Playground

## 01 Virtual Machines

### Create Virtual Machine Azure CLI

**Linux**

```bash
# 1 Install Azure CLI
brew update && brew install azure-cli

# 2 Login and set subscription
az login --subscription "DemoSubscription"

# 3 Create a resource group
az group create \
    --name "demo-rg" \
    --location "centralus"

az group list -o table

# 4 Create a virtual network
az network vnet create \
    --resource-group "demo-rg" \
    --name "demo-vnet-1" \
    --address-prefix "10.1.0.0/16" \
    --subnet-name "demo-subnet-1" \
    --subnet-prefix "10.1.1.0/24"

az network vnet list --output table

# 5 Create a public IP
az network public-ip create \
    --resource-group "demo-rg" \
    --name "demo-ip-1"

# 6 Create a network secutirity group
az network nsg create \
    --resource-group "demo-rg" \
    --name "demo-nsg-1"

az network nsg list --output table

# 7 Create a virtual network interface
az network nic create \
    --resource-group "demo-rg" \
    --name "demo-nic-1" \
    --vnet-name "demo-vnet-1" \
    --subnet "demo-subnet-1" \
    --public-ip-address "demo-ip-1" \
    --network-security-group "demo-nsg-1"

az network nic list --output table

# 8 Create a virtual machine
az vm create
    --resource-group "demo-rg" \
    --location "centralus" \
    --name "demo-vm-1" \
    --nics "demo-nic-1" \
    --image "UbuntuLTS" \
    --admin-username "admin" \
    --authentication-type "ssh" \
    --ssh-key-value ~/.ssh/id_rsa.pub

# 9 Open port 22 (SSH)
az vm open-port \
    --resource-group "demo-rg" \
    --name "demo-vm-1" \
    --port "22"

# 10 Take the public IP
az vm list-ip-addresses \
    --name "demo-vm-1" \
    --output table

# 11 SSH into virtual machine
ssh -l admin X.X.X.X
```

**Windows**

```bash
# 8 Create a virtual machine
az vm create
    --resource-group "demo-rg" \
    --location "centralus" \
    --name "demo-vm-1" \
    --nics "demo-nic-1" \
    --image "win2016datacenter" \
    --admin-username "admin" \
    --admin-password "password"

# 9 Open port 3389 (RDP)
az vm open-port \
    --resource-group "demo-rg" \
    --name "demo-vm-1" \
    --port "3389"

# 11 Use the created IP to login with RDP
```
