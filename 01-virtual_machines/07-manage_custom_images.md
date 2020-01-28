# Azure Playground

## 01 Virtual Machines

### 07 Manage Custom Images

```bash
# 1 List images
az image list --output table

# 2 Create new resource group
az group create \
    --name "demo-rg-east" \
    --location "eastus"

# 3 Create a vm in other region
az vm create \
    --resource-group "demo-rg-east" \
    --location "eastus" \
    --name "demo-cvm" \
    --image "demo-ci" \
    --admin-username "admin" \
    --authentication-type "ssh" \
    --ssh-key-value ~/.ssh/id_rsa.pub

# error because the custom image does not exist in this region

# 4 Add image copy extenstion
az extension add \
    --name image-copy-extension

# 5 Copy images between resource groups and regions
az image copy \
    --source-object-name "demo-ci" \
    --source-resouce-group "demo-rg" \
    --target-location "eastus" \
    --target-resource-group "demo-rg-east" \
    --target-name "demo-ci-east"
    --cleanup

# 6 List images
az image list \
    --output table

# 7 Create a vm in other region
az vm create \
    --resource-group "demo-rg-east" \
    --location "eastus" \
    --name "demo-cvm-east" \
    --image "demo-ci-east" \
    --admin-username "admin" \
    --authentication-type "ssh" \
    --ssh-key-value ~/.ssh/id_rsa.pub

# 6 List VM
az vm list \
    --output table

# 7 Delete an image
az image delete \
    --resource-group "demo-rg-east" \
    --name "demo-ci-east"

```
