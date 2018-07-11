---
title: Instale o PowerShell para o Azure Stack | Microsoft Docs
description: Saiba como instalar o PowerShell para Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: e2785b0beeab042d4b1ad9a9eb5f545dbb58b8b9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/11/2018
ms.locfileid: "38487494"
---
# <a name="install-powershell-for-azure-stack"></a>Instale o PowerShell para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Azure Stack compatível com módulos do PowerShell do Azure são necessárias para trabalhar com o Azure Stack. Neste guia, vamos orientá-lo pelas etapas necessárias para instalar o PowerShell para o Azure Stack.

Este artigo possui instruções detalhadas para instalar o PowerShell para Azure Stack.

> [!Note]  
> As etapas a seguir exigem o PowerShell 5.0. Para verificar sua versão, execute $PSVersionTable.PSVersion e compare as **principais** versão.

Comandos do PowerShell para o Azure Stack são instalados por meio da Galeria do PowerShell. Você pode usar o procedimento a seguir para validar se PSGallery for registrado como um repositório, abra uma sessão do PowerShell com privilégios elevados e execute o seguinte comando:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Se o repositório não estiver registrado, abra uma sessão do PowerShell com privilégios elevados e execute o seguinte comando:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Esta etapa requer acesso à Internet. 

## <a name="uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>Desinstalar as versões existentes dos módulos do PowerShell do Azure Stack

Antes de instalar a versão necessária, certifique-se de que você desinstale todos os módulos do Azure Stack AzureRM PowerShell instalados anteriormente. Você pode desinstalá-los usando um dos dois métodos a seguir:

 - Para desinstalar os módulos do AzureRM PowerShell existentes, feche todas as sessões ativas do PowerShell e execute o seguinte comando:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Excluir todas as pastas que começam com "Azure" das `C:\Program Files\WindowsPowerShell\Modules` e `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` pastas. A exclusão dessas pastas remove todos os módulos do PowerShell existentes.

As seções a seguir descrevem as etapas necessárias para instalar o PowerShell para o Azure Stack. PowerShell pode ser instalado no Azure Stack é operada em conectados, parcialmente conectada, ou em um cenário desconectado.

## <a name="install-the-azure-stack-powershell-modules-in-a-connected-scenario-with-internet-connectivity"></a>Instalar os módulos do PowerShell do Azure Stack em um cenário conectado (com conectividade com a Internet)

Módulos do AzureRM compatíveis pilha do Azure são instalados por meio de perfis de versão de API. O Azure Stack requer o **2017-03-09-perfil** perfil de versão de API, que está disponível instalando o módulo AzureRM.Bootstrapper. Para saber mais sobre perfis de versão de API e os cmdlets fornecidos por eles, consulte a [gerenciar perfis de versão de API](user/azure-stack-version-profiles.md). Além dos módulos do AzureRM, você também deve instalar os módulos do PowerShell do Azure específicas de pilha. Execute o seguinte script do PowerShell para instalar esses módulos em sua estação de trabalho de desenvolvimento:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.3.0 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.3.0 

# Install Module Version 1.2.11 if Azure Stack is running a lower version than 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Para confirmar a instalação, execute o seguinte comando:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Se a instalação for bem-sucedida, os módulos AzureRM e AzureStack são exibidos na saída.

## <a name="install-the-azure-stack-powershell-modules-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Instalar os módulos do PowerShell do Azure Stack em um cenário parcialmente conectado ou um desconectada (com conectividade de Internet limitada)

Em um cenário desconectado, você deve primeiro baixar os módulos do PowerShell para um computador que tenha conectividade com a Internet e, em seguida, transferi-las para o Kit de desenvolvimento do Azure Stack para instalação.

> [!IMPORTANT]  
> A versão do módulo do Azure Stack 1.3.0 PowerShell vem com uma lista de alterações significativas. Para atualizar a partir de 1.2.11 versão, consulte a [guia de migração](https://aka.ms/azspowershellmigration).

1. Entre em um computador em que você tem conectividade com a internet e usa o script a seguir para baixar o AzureRM e AzureStack pacotes em seu computador local:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.11

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.3.0 
   ```

  > [!Important]  
  > Se você não estiver executando o Azure Stack com atualização 1804 ou maior, altere o **requiredversion** valor do parâmetro `1.2.11`. 

2. Copie os pacotes baixados para um dispositivo USB.

3. Entrar para a estação de trabalho e copie os pacotes do dispositivo USB em um local na estação de trabalho.

4. Agora você deve registrar esse local como o repositório padrão e instalar os módulos AzureRM e AzureStack deste repositório:

   ```PowerShell
   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository `
     -Name $RepoName `
     -SourceLocation $SourceLocation `
     -InstallationPolicy Trusted

   Install-Module AzureRM `
     -Repository $RepoName

   Install-Module AzureStack `
     -Repository $RepoName 
   ```

## <a name="configure-powershell-to-use-a-proxy-server"></a>Configurar o PowerShell para usar um servidor proxy

Cenários que exigem um servidor proxy para acessar a internet, primeiro você deve configurar o PowerShell para usar um servidor proxy existente.

1. Abra um prompt elevado do PowerShell.
2. Execute os seguintes comandos:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Próximas etapas

 - [Baixar ferramentas do Azure Stack no GitHub](azure-stack-powershell-download.md)
 - [Configurar o ambiente do PowerShell do usuário do Azure Stack](user/azure-stack-powershell-configure-user.md)  
 - [Configurar o ambiente do PowerShell do operador do Azure Stack](azure-stack-powershell-configure-admin.md) 
 - [Gerenciar perfis de versão de API no Azure Stack](user/azure-stack-version-profiles.md)  
