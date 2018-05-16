---
title: Permitir que os aplicativos recuperar segredos do Cofre de chaves do Azure pilha | Microsoft Docs
description: Usar um aplicativo de exemplo para trabalhar com o Cofre de chaves de pilha do Azure
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/11/2018
ms.author: mabrigg
ms.openlocfilehash: 39bce286c756660cd8755358cf98f2c8d35ce351
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Um aplicativo de exemplo que usa chaves e segredos armazenados em um cofre de chaves

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Siga as etapas neste artigo para executar um aplicativo de exemplo (HelloKeyVault) que recupera as chaves e segredos de um cofre de chaves na pilha do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Você pode instalar os pré-requisitos a seguir da pilha do Azure [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente com Windows externo se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).
* Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Criar e obter o Cofre de chaves e as configurações do aplicativo

Para se preparar para o aplicativo de exemplo:

* Crie um cofre de chaves na pilha do Azure.
* Registre um aplicativo no Azure Active Directory (AD do Azure).

Você pode usar o portal do Azure ou o PowerShell para se preparar para o aplicativo de exemplo. Este artigo mostra como criar um cofre de chaves e registrar um aplicativo usando o PowerShell.

>[!NOTE]
>Por padrão, o script do PowerShell cria um novo aplicativo no Active Directory. No entanto, você pode registrar um de seus aplicativos existentes.

 Antes de executar o script a seguir, certifique-se de fornecer valores para o `aadTenantName` e `applicationPassword` variáveis. Se você não especificar um valor para `applicationPassword`, esse script gera uma senha aleatória.

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
Add-AzureRmAccount `
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

# Create a new resource group and a key vault in that resource group.
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

A próxima captura de tela mostra a saída do script usado para criar o Cofre de chaves:

![Cofre de chave com chaves de acesso](media/azure-stack-kv-sample-app/settingsoutput.png)

Anote o **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valores retornados pelo script anterior. Você pode usar esses valores para executar o aplicativo HelloKeyVault.

## <a name="download-and-configure-the-sample-application"></a>Baixar e configurar o aplicativo de exemplo

Baixe o exemplo de Cofre de chaves do Azure [exemplos de cliente do Cofre de chaves](https://www.microsoft.com/en-us/download/details.aspx?id=45343) página. Extraia o conteúdo do arquivo. zip na sua estação de trabalho de desenvolvimento. Há dois aplicativos na pasta exemplos, este artigo usa HelloKeyVault.

Para carregar o exemplo HelloKeyVault:

* Navegue até o **Microsoft.Azure.KeyVault.Samples** > **exemplos** > **HelloKeyVault** pasta.
* Abra o aplicativo HelloKeyVault no Visual Studio.

### <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

No Visual Studio:

* Abra o arquivo HelloKeyVault\App.config e localizar navegue até o &lt; **appSettings** &gt; elemento.
* Atualização de **VaultUrl**, **AuthClientId**, e **AuthClientSecret** chaves com os valores retornados pelo usado para criar o Cofre de chaves. (Por padrão, o arquivo App. config tem um espaço reservado para *AuthCertThumbprint*. Substitua esse espaço reservado com *AuthClientSecret*.)

  ![Configurações do aplicativo](media/azure-stack-kv-sample-app/appconfig.png)

* Recriar a solução.

## <a name="run-the-application"></a>Executar o aplicativo

Quando você executa HelloKeyVault, o aplicativo entra AD do Azure e, em seguida, usa o token AuthClientSecret para autenticar para o Cofre de chaves na pilha do Azure.

Você pode usar o exemplo HelloKeyVault para:

* Execute operações básicas, como criar, criptografar, incluir e excluir em chaves e segredos.
* Passar parâmetros como *criptografar* e *descriptografar* para HelloKeyVault e aplicar as alterações especificadas em um cofre de chave.

## <a name="next-steps"></a>Próximas etapas

[Implantar uma VM com uma senha do Cofre de Chaves](azure-stack-kv-deploy-vm-with-secret.md)

[Implantar uma VM com um certificado de chave de cofre](azure-stack-kv-push-secret-into-vm.md)
