---
title: Permitir que os aplicativos recuperar segredos do Cofre de chaves do Azure pilha | Microsoft Docs
description: Usar um aplicativo de exemplo para trabalhar com o Cofre de chaves de pilha do Azure
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/26/2017
ms.author: mabrigg
ms.openlocfilehash: 50103dca21d047c5cee211b2250e750739131bc1
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/11/2017
---
# <a name="sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Aplicativo de exemplo que usa chaves e segredos armazenados em um cofre de chaves

Neste artigo, mostramos como executar um aplicativo de exemplo (HelloKeyVault) que recupera as chaves e segredos de um cofre de chaves na pilha do Azure.

## <a name="prerequisites"></a>Pré-requisitos 

Execute os seguintes pré-requisitos do [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).  
* Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md). 

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Criar e obter o Cofre de chaves e as configurações do aplicativo

Primeiro, você deve criar um cofre de chaves na pilha do Azure e registrar um aplicativo no Azure Active Directory (AD do Azure). Você pode criar e registrar os cofres de chaves usando o portal do Azure ou o PowerShell. Este artigo mostra o método de PowerShell para executar as tarefas. Por padrão, este script do PowerShell cria um novo aplicativo no Active Directory. No entanto, você também pode usar um dos seus aplicativos existentes. Certifique-se de fornecer um valor para o `aadTenantName` e `applicationPassword` variáveis. Se você não especificar um valor para o `applicationPassword` variável, esse script gera uma senha aleatória. 

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local' 

# Password for the application. If not specified, this script will generate a random password during app creation.
$applicationPassword = '' 
                         
# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "PLEASE FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator’s PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

Set-AzureRmEnvironment `
  -Name "AzureStackAdmin" `
  -GraphAudience "https://graph.windows.net/"

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Login-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `
  
$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey
    
# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "http://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault within that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location   

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host

``` 

Captura de tela a seguir mostra a saída do script anterior:

![Configuração de aplicativo](media/azure-stack-kv-sample-app/settingsoutput.png)

Anote o **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valores retornados pelo script anterior. Você pode usar esses valores para executar o aplicativo HelloKeyVault.

## <a name="download-and-run-the-sample-application"></a>Baixe e execute o aplicativo de exemplo

Baixe o exemplo de Cofre de chaves do Azure [exemplos de cliente do Cofre de chaves](https://www.microsoft.com/en-us/download/details.aspx?id=45343) página. Extraia o conteúdo do arquivo. zip em sua estação de trabalho de desenvolvimento. Há duas amostras dentro da pasta de exemplos. Usamos o exemplo HellpKeyVault neste artigo. Navegue até o **Microsoft.Azure.KeyVault.Samples** > **exemplos** > **HelloKeyVault** pasta e abra o aplicativo HelloKeyVault no Visual Studio. 

Abra o arquivo HelloKeyVault\App.config e substitua os valores da <appSettings> elemento com o **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valores retornado pelo script anterior. Observe que, por padrão, o App. config contém um espaço reservado para *AuthCertThumbprint*, mas usar *AuthClientSecret* em vez disso. Depois de substituir as configurações, recompile a solução e iniciar o aplicativo.

![Configurações do aplicativo](media/azure-stack-kv-sample-app/appconfig.png)
 
O aplicativo entra AD do Azure e, em seguida, usa esse token para autenticar para o Cofre de chaves na pilha do Azure. O aplicativo executa operações como criar, criptografar, incluir e excluir em chaves e segredos do cofre da chave. Você também pode passar parâmetros específicos, como *criptografar* e *descriptografar* para o aplicativo, que garante que o aplicativo executa somente as operações com o cofre. 


## <a name="next-steps"></a>Próximas etapas
[Implantar uma VM com uma senha do Cofre de Chaves](azure-stack-kv-deploy-vm-with-secret.md)

[Implantar uma VM com um certificado de chave de cofre](azure-stack-kv-push-secret-into-vm.md)



