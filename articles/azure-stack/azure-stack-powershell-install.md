---
title: Instale o PowerShell para a pilha do Azure | Microsoft Docs
description: Saiba como instalar o PowerShell para Azure pilha.
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
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: thoroet
ms.openlocfilehash: 8fea502457275c89d99084a5b025b620872d796b
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/12/2018
---
# <a name="install-powershell-for-azure-stack"></a>Instale o PowerShell para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Pilha do Azure compatíveis módulos do PowerShell do Azure são necessárias para trabalhar com a pilha do Azure. Neste guia, vamos orientá-lo pelas etapas necessárias para instalar o PowerShell para Azure pilha.

Este artigo possui instruções detalhadas para instalar o PowerShell para Azure pilha.

> [!Note]
> As etapas a seguir exigem o PowerShell 5.0. Para verificar a versão, execute $PSVersionTable.PSVersion e compare o **principais** versão.

Comandos do PowerShell para Azure pilha são instalados por meio da Galeria do PowerShell. Você pode usar o procedimento a seguir para validar se PSGallery é registrado como um repositório, abra uma sessão do PowerShell com privilégios elevados e execute o seguinte comando:

```PowerShell  
Get-PSRepository -Name "PSGallery"
```

Se o repositório não estiver registrado, abra uma sessão do PowerShell com privilégios elevados e execute o seguinte comando:

```PowerShell  
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```
> [!Note]  
> Esta etapa requer acesso à Internet. 

## <a name="uninstall-existing-versions-of-powershell"></a>Desinstale as versões existentes do PowerShell

Antes de instalar a versão necessária, certifique-se de que você desinstale quaisquer módulos da pilha do PowerShell do Azure instalados anteriormente. Você pode desinstalá-los usando um dos dois métodos a seguir:

 - Para desinstalar os módulos do PowerShell existentes, feche todas as sessões ativas do PowerShell e execute o seguinte comando:

  ```PowerShell
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
  ```

 - Excluir todas as pastas que começam com "Azure" do `C:\Program Files\WindowsPowerShell\Modules` e `C:\Users\AzureStackAdmin\Documents\WindowsPowerShell\Modules` pastas. A exclusão dessas pastas remove todos os módulos do PowerShell existentes.

As seções a seguir descrevem as etapas necessárias para instalar o PowerShell para Azure pilha. PowerShell pode ser instalado na pilha do Azure operado na conectado, parcialmente conectado, ou em um cenário desconectado.

## <a name="install-powershell-in-a-connected-scenario-with-internet-connectivity"></a>Instale o PowerShell em um cenário conectado (com conectividade com a Internet)

Módulos de AzureRM compatíveis pilha do Azure são instalados por meio de perfis de versão de API. A pilha do Azure requer o **2017-03-09-perfil** perfil de versão de API, que está disponível ao instalar o módulo AzureRM.Bootstrapper. Para saber mais sobre perfis de versão de API e os cmdlets fornecidos por elas, consulte o [gerenciar perfis de versão de API](user/azure-stack-version-profiles.md). Além dos módulos AzureRM, você também deve instalar os módulos do PowerShell de pilha específicos do Azure. Execute o seguinte script do PowerShell para instalar esses módulos em sua estação de trabalho de desenvolvimento:

  ```PowerShell  
# Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
Install-Module -Name AzureRm.BootStrapper 

# Install and import the API Version Profile required by Azure Stack into the current PowerShell session. 
Use-AzureRmProfile -Profile 2017-03-09-profile -Force 

# Install Module Version 1.2.12 if Azure Stack is running 1804 at a minimum 
Install-Module -Name AzureStack -RequiredVersion 1.2.12 

# Install Module Version 1.2.11 if Azure Stack is running a lower version then 1804 
Install-Module -Name AzureStack -RequiredVersion 1.2.11 
  ```

Para confirmar a instalação, execute o seguinte comando:

```PowerShell  
Get-Module -ListAvailable | where-Object {$_.Name -like "Azs*"}
```

Se a instalação for bem-sucedida, os módulos AzureRM e AzureStack são exibidos na saída.

## <a name="install-powershell-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Instale o PowerShell em um cenário parcialmente conectado ou um desconectada (com conectividade de Internet limitada)

Em um cenário desconectado, primeiro baixar os módulos do PowerShell para um computador que tenha conectividade com a Internet e, em seguida, transferi-los para o Kit de desenvolvimento de pilha do Azure para instalação.

> [!IMPORTANT]
> A versão do módulo do PowerShell AzureRM 1.2.12 vem com uma lista de alterações significativas. Para atualizar a partir de 1.2.10 versão, consulte o [guia de migração](https://github.com/bganapa/azure-powershell/blob/stack-migration/documentation/migration-guides/Stack/migration-guide.1.2.12.md).

1. Entrar em um computador em que você tem conectividade com a internet e usa o script a seguir para baixar o AzureRM e AzureStack pacotes no computador local:

   ```PowerShell  
   $Path = "<Path that is used to save the packages>"

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureRM `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.12

   Save-Package `
     -ProviderName NuGet `
     -Source https://www.powershellgallery.com/api/v2 `
     -Name AzureStack `
     -Path $Path `
     -Force `
     -RequiredVersion 1.2.12 
   ```

> [!Important]  
> Se você não estiver executando o Azure pilha com atualização 1804 ou maior, altere o **requiredversion** valor do parâmetro `1.2.11`. 

2. Copie os pacotes baixados em um dispositivo USB.

3. Entrar na estação de trabalho e copiar os pacotes do dispositivo USB em um local na estação de trabalho.

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

Em cenários que exigem um servidor proxy para acessar a internet, primeiro você deve configurar o PowerShell para usar um servidor proxy existente.

1. Abra um prompt elevado do PowerShell.
2. Execute os seguintes comandos:

````PowerShell  
  #To use Windows credentials for proxy authentication
  [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

  #Alternatively, to prompt for separate credentials that can be used for #proxy authentication

  [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
````

## <a name="next-steps"></a>Próximas etapas

 - [Baixar ferramentas de pilha do Azure do GitHub](azure-stack-powershell-download.md)
 - [Configurar o ambiente do PowerShell do usuário a pilha do Azure](user/azure-stack-powershell-configure-user.md)  
 - [Configurar o ambiente do PowerShell do operador de pilha do Azure](azure-stack-powershell-configure-admin.md) 
 - [Gerenciar perfis de versão de API na pilha do Azure](user/azure-stack-version-profiles.md)  
