<properties
	pageTitle="Configurando o acesso do WinRM para as máquinas virtuais no Azure Resource Manager | Microsoft Azure"
	description="Como configurar o acesso do WinRM para uso com uma máquina virtual do Azure Resource Manager"
	services="virtual-machines-windows"
	documentationCenter=""
	authors="singhkay"
	manager="drewm"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/16/2016"
	ms.author="singhkay"/>

# Configurando o acesso do WinRM para as máquinas virtuais no Azure Resource Manager

## WinRM no Gerenciamento de Serviços do Azure vs. Azure Resource Manager

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] modelo de implantação clássico

* Para uma visão geral do Azure Resource Manager, consulte este [artigo](../resource-group-overview.md)
* Para encontrar as diferenças entre o Gerenciamento de Serviços do Azure e o Azure Resource Manager, consulte este [artigo](../resource-manager-deployment-model.md)

A principal diferença na definição da configuração do WinRM entre as duas pilhas é como o certificado é instalado na VM. Na pilha do Azure Resource Manager, os certificados são modelados como recursos gerenciados pelo Provedor de Recursos do Cofre de Chaves. Portanto, o usuário precisa fornecer seu próprio certificado e carregá-lo em um Cofre de Chaves antes de usá-lo em uma VM.

Aqui estão as etapas que você precisa realizar para configurar uma VM com conectividade do WinRM

1. Criar um cofre de chaves
2. Crie um certificado autoassinado
3. Carregar seu certificado autoassinado no Cofre de Chaves
4. Obtenha a URL para seu certificado autoassinado no Cofre de Chaves
5. Referenciar a URL de seus certificados autoassinados ao criar uma VM

## Etapa 1: Criar um cofre de chaves

Você pode usar o comando abaixo para criar o Cofre de Chaves

```
New-AzureRmKeyVault -VaultName "<vault-name>" -ResourceGroupName "<rg-name>" -Location "<vault-location>" -EnabledForDeployment -EnabledForTemplateDeployment
```

## Etapa 2: Criar um certificado autoassinado
Você pode criar um certificado autoassinado usando esse script do PowerShell

```
$certificateName = "somename"

$thumbprint = (New-SelfSignedCertificate -DnsName "$certificateName" -CertStoreLocation Cert:\CurrentUser\My -KeySpec KeyExchange).Thumbprint

$cert = (Get-ChildItem -Path cert:\CurrentUser\My\$thumbprint)

$password = Read-Host -Prompt "Please enter the certificate password." -AsSecureString

Export-PfxCertificate -Cert $cert -FilePath ".\$certificateName.pfx" -Password $password
```

## Etapa 3: Carregar seu certificado autoassinado no Cofre de Chaves

Antes de carregar o certificado para o Cofre de Chaves criado na Etapa 1, ele precisa ser convertido em um formato que o provedor de recursos Microsoft.Compute entenda. O script do PowerShell abaixo permitirá que você faça isso

```
$fileName = "<Path to the .pfx file>"
$fileContentBytes = get-content $fileName -Encoding Byte
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)

$jsonObject = @"
{
  "data": "$filecontentencoded",
  "dataType" :"pfx",
  "password": "<password>"
}
"@

$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

$secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText –Force
Set-AzureRmKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>" -SecretValue $secret
```

## Etapa 4: Obter a URL para seu certificado autoassinado no Cofre de Chaves

O provedor de recursos Microsoft.Compute precisa de uma URL para o segredo do Cofre de Chaves ao provisionar a VM. Isso permite que o provedor de recursos Microsoft.Compute baixe o segredo e crie o certificado equivalente na VM.

>[AZURE.NOTE]A URL do segredo precisa incluir a versão também. Um exemplo de URL se parece com a abaixo https://contosovault.vault.azure.net:443/secrets/contososecret/01h9db0df2cd4300a20ence585a6s7ve


#### Modelos

Você pode obter o link para a URL no modelo usando o código abaixo

    "certificateUrl": "[reference(resourceId(resourceGroup().name, 'Microsoft.KeyVault/vaults/secrets', '<vault-name>', '<secret-name>'), '2015-06-01').secretUriWithVersion]"

#### PowerShell

Você pode obter essa URL usando o comando do PowerShell abaixo

	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id

## Etapa 5: Referenciar a URL de seus certificados autoassinados ao criar uma VM

#### Modelos do Azure Resource Manager

Ao criar uma VM por meio de modelos, o certificado é referenciado na seção de segredos e na seção winRM como abaixo

	"osProfile": {
          ...
          "secrets": [
            {
              "sourceVault": {
                "id": "<resource id of the Key Vault containing the secret>"
              },
              "vaultCertificates": [
                {
                  "certificateUrl": "<URL for the certificate you got in Step 4>",
                  "certificateStore": "<Name of the certificate store on the VM>"
                }
              ]
            }
          ],
          "windowsConfiguration": {
            ...
            "winRM": {
              "listeners": [
                {
                  "protocol": "http"
                },
                {
                  "protocol": "https",
                  "certificateUrl": "<URL for the certificate you got in Step 4>"
                }
              ]
            },
            ...
          }
        },

É possível encontrar um modelo de exemplo para o indicado acima em [201-vm-winrm-keyvault-windows](https://azure.microsoft.com/documentation/templates/201-vm-winrm-keyvault-windows)

O código-fonte para este modelo pode ser encontrado no [Github](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-winrm-keyvault-windows)

#### PowerShell

	$vm = New-AzureRmVMConfig -VMName "<VM name>" -VMSize "<VM Size>"
	$credential = Get-Credential
	$secretURL = (Get-AzureKeyVaultSecret -VaultName "<vault name>" -Name "<secret name>").Id
	$vm = Set-AzureRmVMOperatingSystem -VM $vm  -Windows -ComputerName "<Computer Name>" -Credential $credential -WinRMHttp -WinRMHttps -WinRMCertificateUrl $secretURL
	$sourceVaultId = (Get-AzureRmKeyVault -ResourceGroupName "<Resource Group name>" -VaultName "<Vault Name>").ResourceId
	$CertificateStore = "My"
	$vm = Add-AzureRmVMSecret -VM $vm -SourceVaultId $sourceVaultId -CertificateStore $CertificateStore -CertificateUrl $secretURL

## Etapa 6: Conectando-se à VM
Antes de poder se conectar à VM você precisará se certificar de que seu computador esteja configurado para o gerenciamento remoto do WinRM. Inicie o PowerShell como administrador e execute o comando abaixo para garantir que você está configurado.

    Enable-PSRemoting -Force

>[AZURE.NOTE] Você precisará garantir que o serviço WinRM está em execução se o indicado acima não funcionar. Você pode fazer isso usando `Get-Service WinRM`

Quando a instalação estiver concluída, você poderá se conectar à VM usando o comando abaixo

    Enter-PSSession -ConnectionUri https://<public-ip-dns-of-the-vm>:5986 -Credential $cred -SessionOption (New-PSSessionOption -SkipCACheck -SkipCNCheck -SkipRevocationCheck) -Authentication Negotiate

<!---HONumber=AcomDC_0622_2016-->