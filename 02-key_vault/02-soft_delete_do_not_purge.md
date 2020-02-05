# 02 Key Vault

## 02 Soft Delete and Do Not Purge

```powershell
# 1 Connect to Azure
Connect-AzureRmAccount

# 2 Get key vault
Get-AzureRmKeyVault `
    -VaultName 'demo-key-vault-1'

# 3 Enable Soft Delete
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName 'demo-key-vault-1').ResourceId).Properties | `
    Add-Member `
    -MemberType 'NoteProperty' `
    -Name 'enableSoftDelete' `
    -Value 'true'

Set-AzureRmKeyValt `
    -EesrouceId $resource.ResourceId
    -Properties $resource.Properties

# 4 Remove a key vault
Remove-AzureRmKeyVault `
    -VaultName 'demo-key-vault-1' `
    -ResourceGroupName 'demo-rg'

# 5 Recover soft deleted vault
Undo-AzureRmKeyVaultRemoval `
    -VaultName 'demo-key-vault-1' `
    -ResourceGroupName 'demo-rg' `
    -Location 'centralus'

# 6 Enable Do Not Purge
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName 'demo-key-vault-1').ResourceId).Properties | `
    Add-Member `
    -MemberType 'NoteProperty' `
    -Name 'enablePurgeProtection' `
    -Value 'true'

Set-AzureRmKeyValt `
    -EesrouceId $resource.ResourceId
    -Properties $resource.Properties

# 7 Permanantly delete the vault - does not work with Do Not Purge enabled
Remove-AzureRmKeyVault `
    -VaultName 'demo-key-vault-1' `
    -ResourceGroupName 'demo-rg'

Remove-AzureRmKeyVault `
    -VaultName 'demo-key-vault-1' `
    -ResourceGroupName 'demo-rg' `
    -InRemovedState

# 8 Recover vault again
Undo-AzureRmKeyVaultRemoval `
    -VaultName 'demo-key-vault-1' `
    -ResourceGroupName 'demo-rg' `
    -Location 'centralus'
```
