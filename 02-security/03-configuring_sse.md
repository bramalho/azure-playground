# 02 Security

## 03 Configuring SSE

```powershell
# 1 Connect to Azure
Connect-AzureRmAccount

# 2 Assign a storage account
Set-AzureRmStorageAccount `
    -ResourceGroupName 'demo-rg' `
    -Name 'demo-sa-1' `
    -AssignIdentity

# 3 Associate Key to storage account
$storageAccount = Get-AzureRmStorageAccount `
    -ResourceGroupName 'demo-rg' `
    -AccountName 'demo-sa-1'

$keyVault = Get-AzureRmKeyVault `
    -VaultName 'demo-key-vault-1' `

$key = Get-AzureRmKey `
    -VaultName 'demo-key-vault-1' `
    -Name 'demo-key-1'

# 4 Set access policy
Set-AzureRmKeyVaultAccessPolicy `
    -VaultName 'demo-key-vault-1' `
    -ObjectId $storageAccount.Identity.PrincipalId `
    -PermissionsToKeys wrapkey,unwrapkey,get

# 5 Set storage account
Set-AzureRmStorageAccount `
    -ResourceGroupName $storageAccount.ResourceGroupName `
    -AccountName $storageAccount.StorageAccountName `
    -KeyVaultEncryption `
    -KeyName $key.Name `
    -KeyVersion $key.Version `
    -KeyVaultUri $keyVault.VaultUri
```
