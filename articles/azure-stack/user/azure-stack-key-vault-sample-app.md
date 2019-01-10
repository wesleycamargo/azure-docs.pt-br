---
title: Permitir que os aplicativos recuperar segredos do Cofre de chaves do Azure Stack | Microsoft Docs
description: Usar um aplicativo de exemplo para trabalhar com o Cofre de chaves do Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/27/2018
ms.author: sethm
ms.openlocfilehash: 09a31103b2a29bc335653dd92e6b11a5f8294eda
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192344"
---
# <a name="a-sample-application-that-uses-keys-and-secrets-stored-in-a-key-vault"></a>Um aplicativo de exemplo que usa chaves e segredos armazenados em um cofre de chaves

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Siga as etapas neste artigo para executar um aplicativo de exemplo denominado **HelloKeyVault** que recupera as chaves e segredos de uma chave do cofre no Azure Stack.

## <a name="prerequisites"></a>Pré-requisitos

Você pode instalar os pré-requisitos a seguir do Azure Stack [Development Kit](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop), ou de um cliente de externo com base no Windows se você estiver [conectados por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

* Instale [módulos do Azure Stack-compatível com o Azure PowerShell](azure-stack-powershell-install.md).
* Baixe o [as ferramentas necessárias para trabalhar com o Azure Stack](azure-stack-powershell-download.md).

## <a name="create-and-get-the-key-vault-and-application-settings"></a>Criar e obter o Cofre de chaves e as configurações do aplicativo

Para preparar para o aplicativo de exemplo:

* Crie um cofre de chaves no Azure Stack.
* Registre um aplicativo no Azure Active Directory (Azure AD).

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
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

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

A imagem a seguir mostra a saída do script usado para criar o Cofre de chaves:

![Cofre de chaves com chaves de acesso](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Anote o **VaultUrl**, **AuthClientId**, e **AuthClientSecret** valores retornados pelo script anterior. Você pode usar esses valores para executar o aplicativo HelloKeyVault.

## <a name="download-and-configure-the-sample-application"></a>Baixar e configurar o aplicativo de exemplo

Baixe o exemplo de Cofre de chaves do Azure [exemplos de cliente do Cofre de chaves](https://www.microsoft.com/download/details.aspx?id=45343) página. Extraia o conteúdo do arquivo. zip em sua estação de trabalho de desenvolvimento. Há dois aplicativos na pasta exemplos. Este artigo usa **HelloKeyVault**.

Para carregar o **HelloKeyVault** exemplo:

* Navegue até a **Microsoft.Azure.KeyVault.Samples** > **exemplos** > **HelloKeyVault** pasta.
* Abra o **HelloKeyVault** aplicativo no Visual Studio.

### <a name="configure-the-sample-application"></a>Configurar o aplicativo de exemplo

No Visual Studio:

* Abra o arquivo HelloKeyVault\App.config e localize o &lt; **appSettings** &gt; elemento.
* Atualizar o **VaultUrl**, **AuthClientId**, e **AuthClientSecret** chaves com os valores retornados por aqueles usados para criar o Cofre de chaves. Por padrão, o arquivo App. config tem um espaço reservado para `AuthCertThumbprint`. Substitua esse espaço reservado com `AuthClientSecret`.

  ![Configurações do aplicativo](media/azure-stack-key-vault-sample-app/appconfig.png)

* Recriar a solução.

## <a name="run-the-application"></a>Executar o aplicativo

Quando você executa HelloKeyVault, o aplicativo se conecta ao Azure AD e, em seguida, usa o token AuthClientSecret para autenticar para o Cofre de chaves no Azure Stack.

Você pode usar o exemplo HelloKeyVault para:

* Execute operações básicas, como criar, criptografar, encapsular e excluir em chaves e segredos.
* Passar parâmetros como `encrypt` e `decrypt` para HelloKeyVault e aplicar as alterações especificadas para um cofre de chaves.

## <a name="next-steps"></a>Próximas etapas

- [Implantar uma VM com uma senha do Cofre de Chaves](azure-stack-key-vault-deploy-vm-with-secret.md)
- [Implantar uma VM com um certificado de Cofre de chaves](azure-stack-key-vault-push-secret-into-vm.md)
