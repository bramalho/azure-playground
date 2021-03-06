# 01 Virtual Machines

## 05 Create Custom Linux Virtual Machine Image

```bash
# 1 Login and set subscription
az login --subscription "DemoSubscription"

# 2 Get VM IP
az vm list-ip-addresses --name "demo-vm-1" --output table

# 3 Connect via SSH
ssh admin@W.X.Y.Z

# 4 Deprovision VM
sudo waagent -deprovision+user -force

# 5 Exit
exit

# 6 Dealocate VM
az vm deallocate \
    --resource-group "demo-rg" \
    --name "demo-vm-1"

# 7 Check status
az vm list \
    --show-details \
    --output table

# 8 Mark VM as generalized
az vm generalize \
    --resource-group "demo-rg" \
    --name "demo-vm-1"

# 9 Create custom image
az image create\
    --resource-group "demo-rg"
    --name "demo-ci"
    --source "demo-vm"

# 10 Show image details
az vm image \
    --output table

# 11 Create VM
az vm create \
    --resource-group "demo-rg" \
    --location "centralus" \
    --name "demo-cvm" \
    --image "demo-ci" \
    --admin-username "admin" \
    --authentication-type "ssh" \
    --ssh-key-value ~/.ssh/id_rsa.pub

# 12 Check status
az vm list \
    --show-details \
    --output table

# 13 Try to start - will show an error that VM is generalized
az vm start \
    --name "demo-vm" \
    --resource-group "demo-rg"

# 14 Delete VM
az vm delete \
    --name "demo-vm" \
    --resource-group "demo-rg"

# 15 List images
az resource list \
    --resouce-type=Microsoft.Compute/images \
    --output table
```
