# 02 Key Vault

## 01 Create Key Vault

```powershell
# 1 Connect to Azure
Connect-AzureRmAccount

# 2 Create a new key vault
New-AzureRmKeyVault `
    -VaultName 'demo-key-vault-1' `
    -ResourceGroup 'demo-rg' `
    -Location 'centralus'

# 3 Convert a secret to a secure string
$secretValue = ConvertTo-SecureString `
    -String 'MySecret' `
    -AsPlainText `
    -Force

# 4 Add secure string to the vault
$secret = Set-AzureKeyVautlSecret `
    -VaultName 'demo-key-vault-1' `
    -Name 'demo-secret-1' `
    -SecretValue $secretValue

# 5 Print the secret ID
$secret.Id
```
