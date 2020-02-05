# 02 Key Vault

## 04 Enable ADE

```powershell
# 1 Connect to Azure
Connect-AzureRmAccount

# 2 Register the resource provider
Register-AzureRmResourceProvider `
    -ProviderNamespace 'Miscorsoft.keyVault'

# 3 Create a new key vault
New-AzureRmKeyVault `
    -ValutName 'demo-key-vault-disk-1' `
    -ResourceGroupName 'demo-rg' `
    -Location 'centralus' `
    -EnableForDiskEncription

# 4 Create cryptographic key
Add-AzureKeyVaultKey `
    -ValutName 'demo-key-vault-disk-1' `
    -Name 'demo-de-key' `
    -Destination 'Software'

# 5 Create an application
$appName = 'demo-app-1'
$password = ConvertTo-SecureString 'pass123!@#' -AsPlainText -Force
$app = New-AzureRmADApplication `
    -DisplayName $appName `
    -HomePage 'https://demo-app-1.azurewebsites.net' `
    -IdentifiersUri 'https://demo-app-1.azurewebsites.net/contact' `
    -Password $passowrd

New-AzureRmADServicePrincipal `
    -ApplicationId $app.ApplicationId

# 6 Set key vault access policy
Set-AzureRmKeyVaultAccessPolicy `
    -ValutName 'demo-key-vault-disk-1' `
    -ServicePrincipalName $app.ApplicationId
    -PermissionsToKeys 'wrapKey'
    -PermissionsToSecrets 'Set'

# 7 Encrypt the disk
$keyVault = Get-AzureRmKeyVault `
    -ValutName 'demo-key-vault-disk-1' `
    -ResourceGroupNAme 'demo-de-key'
$keyEncryptionKeyUrl = (`
    Get-AzureKeyVaultKey `
    -ValutName 'demo-key-vault-disk-1' `
    -Name 'demo-de-key' `
    ).key.kid

Set-AzureRmVMDiskEncryptionExtension `
    -ResourceGroupNAme 'demo-de-key' `
    -VMName 'demo-vm-1' `
    -AadClientID $app.ApplicationId
    -AddClientSecret (New-Object PSCredential "user",$passowrd).GetNetworkCredentials().Password `
    -DiskEncryptionKeyVaultUrl $KeyVault.VaultUrl `
    -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
    -KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
    -KeyEncryptionKeyVaultId $KeyVault.ResourceId

# 8 Get encryption status
Get-AzureRmVmDiskEncryptionStatus `
    -ResourceGroupNAme 'demo-de-key' `
    -VMName 'demo-vm-1'

# 9 Disable encryptions
Disable-AzureRmVMDiskEncryption `
    -ResourceGroupNAme 'demo-de-key' `
    -VMName 'demo-vm-1'
```
