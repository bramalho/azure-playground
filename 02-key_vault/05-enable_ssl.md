# 02 Key Vault

## 05 Enable SSL

```powershell
# 1 Connect to Azure
Connect-AzureRmAccount

$fqdn = 'example.com'
$pfxPath = '/home/bramalho/examplecom.pfx'
$pfxPassword = 'pass123!@#'
$webapname = 'demo-app-1'
$location = 'centralus'
$resourceGroupName = 'demo-rg'

# 2 Add domain to app
Set-AzureRmWebApp `
    -Name $webappdomain `
    -ResourceGroupName $resourceGroupName `
    -HostNames @($fqdn,'$webapname.azurewebsites.net')

# 3 Upload new certificate
New-AzureRmWebAppSSLBinding `
    -WebAppName $webapname `
    -ResourceGroupName $resourceGroupName `
    -Name $fqdn `
    -CertificateFilePath $pfxPath `
    -CertificatePassword $pfxPassword `
    -Ssltate SniEnabled
```
