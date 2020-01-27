# Azure Playground

## 01 Virtual Machines

### 02 Create Virtual Machine Power Shell

**Linux**

```powershell
# 1 Get resource group
$rg = Get-AzureRmResourceGroup `
    -Name 'demo-rg' `
    -Location 'centralus'

$rg

# 2 Create subnet
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
    -Name 'demo-subnet-2' `
    -AddressPrefix '10.2.1.0/24'

$subnetConfig


# 3 Create virtual network
$vnet = New-AzureRmVirtualNetwork `
    -ResourceGroupName $rg.ResourceGroupName `
    -Localtion $tg.Location `
    -Name 'demo-vnet-2' `
    -AddressPrefix '10.2.0.0/16' `
    -Subnet $subnetConfig

$vnet

# 4 Create public IP
$pip = New-AzureRmPublicIpAddress `
    -ResourceGroupName $rg.ResourceGroupName `
    -Localtion $tg.Location `
    -Name 'demo-ip-2'
    -AllocationMethod Static

$pip

# 5 Create security rule for SSH
$ruleSSH = New-AzureRmNetworkSecurityRuleConfig `
    -Name ssh-rule `
    -Description 'Allow SSH' `
    -Access Allow `
    -Protocol Tcp `
    -Direction Inbound `
    -Priority 100 `
    -SourceAddressPrefix Internet `
    -SourcePortRange * `
    -DestinationAddressPrefix * `
    -DestinationPortRange 22

$ruleSSH

# 6 Create network security group
$nsg = New-AzureRmNetworkSecurityGroup `
    -ResourceGroupName $rg.ResourceGroupName `
    -Localtion $tg.Location `
    -Name 'demo-nsg-2' `
    -SecurityRules $ruleSSH

$nsg

# 7 Create a virtual network and associate IP
$subnet = $vnet.Subnets | Where-Object { $_.Name -eq 'demo-subnet-2' }

$nic = New-AzureRmNetworkInterface `
    -ResourceGroupName $rg.ResourceGroupName `
    -Localtion $tg.Location `
    -Name 'demo-nic-2' `
    -Subnet $subnet `
    -PublicIpAddress $pip `
    -NetworkSecurityGroup $nsg

$nic

# 8 Create a virutal machine
$vmConfig = New-AzureRmVMCanfig `
    -VMName 'demo-vm-2' `
    -VMSize 'Standard_D1'

$vmConfig

# 9 Set computer name, OS and auth
$password = ConvertTo-SecureString 'pass123!@#' -AsPlainText -Force
$credentials = New-Object System.Management.Automation.PSCredential ('admin', $password)

$vmConfig = Set-AzureRmVMOperatingSystem `
    -VM $vmConfig `
    -Linux `
    -ComputerName 'demo-vm-2'
    -DisablePasswordAuthentication `
    -Credential $credentials

# 10 Set SSH Key
$sshPublicKey = Get-Content "~/.ssh.id_rsa.pub"
Add-AzureRmVMSshPublicKey `
    -VM $vmConfig `
    -KeyData $sshPublicKey `
    -Path "/home/admin/.ssh/authorized_keys"

# 11 Set OS version
Get-AzureRmVMImageSku `
    -Location $rg.Location `
    -Publisher "Redhat" `
    -Offer "rhel"

$vmConfig = Set-AzureRmVMSourceImage `
    -VM $vmConfig
    -PublisherName 'Redhat' `
    -Offer 'rhel' `
    -Skus '7.4' `
    -Version 'latest'

# 12 Assign network interface
$vmConfig = Add-AzureRmVMNetworkInterface `
    -VM $vmConfig
    -Id $nic.Id

# 13 Create the virtual machine
New-AzureRmVM `
    -ResourceGroupName $rg.ResourceGroupName `
    -Localtion $tg.Location `
    -VM $vmConfig

# 14 Get the public IP
$ip = Get-AzureRmPublicIpAddress `
    -ResourceGroupName $rg.ResourceGroupName
    -Name $pip.Name | Select-Object -ExpandPreperty IpAddress

$ip

# 15 SSH into virtual machine
ssh -l admin $ip
```

**Windows**

```powershell
# 9 Set computer name, OS and auth
$password = ConvertTo-SecureString 'pass123!@#' -AsPlainText -Force
$credentials = New-Object System.Management.Automation.PSCredential ('admin', $password)

New-AzureRMVm -Image

$vmParams = @{
    ResourceGroupName = 'demo-rg'
    Name = 'demo-vm-2'
    Location = 'cetralus'
    Size = 'Standard_D1'
    Image = 'Win2016Datacenter'
    PublicIpAddressName = 'demo-ip-2'
    Credential = $credentials
    VirtualNetworkName = 'demo-vnet-2'
    SubnetName = 'demo-subnet-2'
    SecurityGroupName = 'demo-nsg-2'
    OpenPorts = 3389
}

New-AzureRMVm $vmParams

# 10 Get the public IP
Get-AzureRmPublicIpAddress `
    -ResourceGroupName $rg.ResourceGroupName
    -Name 'demo-vm-2' | Select-Object -ExpandPreperty IpAddress

# 11 Use the created IP to login with RDP
```
