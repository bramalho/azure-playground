# Azure Playground

## 01 Virtual Machines

### 06 Create Custom Windows Virtual Machine Image

```powershell
# 1 Connect to Azure
Connect-AzureRmAccount -Subscription 'DemoSubscription'

# 2 Generalize VM
# Open RDp to Windows VM and run the command in the command prompt
%WINDIR%\system32\sysprep\sysprep.exe /generalize /shutdown /oobe

# 3 Get status
Get-AzureRmVM `
    -ResourceGroupName 'demo-rg'
    -Name 'demo-vm-1'
    -Status

# 4 Get the Resource Group
$rg = Get-AzureRmResourceGroup `
    -Name 'demo-rg' `
    -Location 'centralus'

# 5 Get the VM
$vm = Get-AzureRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Name 'demo-vm-1'

# 6 Dealocate VM
Stop-AzureRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Name $vm.Name `
    -Force

# 7 Check status
Get-AzureRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Name $vm.Name `
    -Status

# 8 Mark VM as generalized
Set-AzureRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Name $vm.Name `
    -Generalized

# 9 Start Image Configuration
$image = New-AzureRmImageConfig `
    -Location $rg.Location `
    -SourceVirtualMachineId $vm.ID

# 10 Create VM
New-AzureImage `
    -ResourceGroupName $rg.ResourceGroupName `
    -Image $image `
    -ImageName 'demo-ci'

# 11 Get images
Get-AzureRmImage `
    -ResourceGroupName $rg.ResourceGroupName

# 12 Createe user credentials
$password = ConvertTo-SecureString 'pass123!@#' -AsPlainText -Force
$credentials = New-Object System.Management.Automation.PSCredential ('admin', $password)

# 13 Create VM
New-AzureRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Name 'demo-cvm' `
    -ImageName 'demo-ci' `
    -Location 'centralus'
    -Cretential $credentials
    -VirtualNetworkName 'demo-vnet-2' `
    -SubnetName 'demo-subnet-2' `
    -SecurityGroupName 'demo-nsg-2' `
    -OpenPorts 3389

# 14 Check status
Get-AzyreRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Name 'demo-cvm'

# 15 Delete VM
Remove-AzureRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Name 'demo-vm'
    -Force

# 16 List images
Get-AzureRmImage `
    -ResourceGroupName $rg.ResourceGroupName `
    -ImageName 'demo-ci'
```
