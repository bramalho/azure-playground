# 01 Virtual Machines

## 04 Exploring Virtual Machine Cloud Shell

```powershell
Get-AzureRmVM -ResourceGroupName "demo-rg" -Name "demo-vm-1" -Status

Get-ChildItem 'Azure:/DemoSubscription/VirtualMachines'

Get-ChildItem 'Azure:/DemoSubscription/VirtualMachines/demo-vm-1'

# Stop VM
Get-ChildItem 'Azure:/DemoSubscription/VirtualMachines/demo-vm-1' | `
    Stop-AzureRmVM -StayProvisioned -Force -AsJob

# The status is cached, -Force to refresh
Get-ChildItem 'Azure:/DemoSubscription/VirtualMachines/demo-vm-1' -Force

# Dealocate VM (not charged for it)
Get-ChildItem 'Azure:/DemoSubscription/VirtualMachines/demo-vm-1' | `
    Stop-AzureRmVM -Force -AsJob

# Start VM
Start-AzureRmVM -ResourceGroupName "demo-rg" -Name "demo-vm-1" -AsJob

# Remove VM
Get-ChildItem 'Azure:/DemoSubscription/VirtualMachines/demo-vm-1' | `
    Remove-AzureRmVM -Force -AsJob
```
