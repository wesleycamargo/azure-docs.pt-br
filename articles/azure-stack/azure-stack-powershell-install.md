---
title: Instale o PowerShell para o Azure Stack | Microsoft Docs
description: Saiba como instalar o PowerShell para Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/18/2018
ms.author: sethm
ms.reviewer: thoroet
ms.openlocfilehash: c87b7f18ff5bf94bf842fa7a7e31cad4c7f47dfe
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128015"
---
# <a name="install-powershell-for-azure-stack"></a>Instale o PowerShell para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Para trabalhar com sua nuvem, você deve instalar os módulos do Azure Stack compatíveis do PowerShell. Compatibilidade é habilitada por meio de um recurso chamado *perfis de API*.

Perfis de API fornecem uma maneira de gerenciar as diferenças de versão entre o Azure e o Azure Stack. Um perfil de versão de API é um conjunto de módulos do PowerShell do Azure Resource Manager com as versões de API específicas. Cada plataforma de nuvem tem um conjunto de perfis de versão de API com suporte. Por exemplo, Azure Stack dá suporte a uma versão de perfil com data específica, como **2018-03-01-hybrid**, e o Azure dá suporte a **mais recente** perfil da versão de API. Quando você instala um perfil, são instalados os módulos do PowerShell do Azure Resource Manager que correspondem ao perfil especificado.  

Você pode instalar o Azure Stack compatível com módulos do PowerShell na Internet conectado, parcialmente conectada ou desconectada cenários. Este artigo explica as instruções detalhadas para instalar o PowerShell para o Azure Stack para esses cenários.

## <a name="1-verify-your-prerequisites"></a>1. Verifique se os pré-requisitos

Antes de começar com o Azure Stack e o PowerShell, você deve ter os seguintes pré-requisitos:

- **PowerShell versão 5.0**  
Para verificar sua versão, execute **$PSVersionTable.PSVersion** e comparar as **principais** versão. Se você não tiver o PowerShell 5.0, execute as [instalando o Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]  
  > PowerShell 5.0 requer uma máquina Windows.

- **Executar o Powershell no prompt de comando elevado**  
  Você deve executar o PowerShell com privilégios administrativos.

- **Acesso de galeria do PowerShell**  
  Você precisa de acesso para o [da Galeria do PowerShell](https://www.powershellgallery.com). A Galeria é o repositório central do conteúdo do PowerShell. O **PowerShellGet** módulo contém cmdlets para descoberta, instalação, atualização e publicação artefatos do PowerShell como módulos, recursos DSC, capacidades de função e de scripts na Galeria do PowerShell e outro particular repositórios. Se você estiver usando o PowerShell em um cenário desconectado, você deve recuperar os recursos de um computador com uma conexão à Internet e armazená-los em um local acessível em seu computador desconectado.


## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Validar a acessibilidade de galeria do PowerShell

Valide se PSGallery for registrado como um repositório.

> [!Note]  
> Esta etapa requer acesso à Internet. 

Abra um prompt do PowerShell com privilégios elevados e execute os seguintes cmdlets:

````PowerShell  
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
````

Se o repositório não estiver registrado, abra uma sessão do PowerShell com privilégios elevados e execute o seguinte comando:

```PowerShell
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Desinstalar as versões existentes dos módulos do PowerShell do Azure Stack

Antes de instalar a versão necessária, certifique-se de que você desinstale todos os módulos do Azure Stack AzureRM PowerShell instalados anteriormente. Você pode desinstalá-los usando um dos dois métodos a seguir:

1. Para desinstalar os módulos do AzureRM PowerShell existentes, feche todas as sessões ativas do PowerShell e execute os seguintes cmdlets:

  ````PowerShell  
    Uninstall-Module AzureRM.AzureStackAdmin -Force
    Uninstall-Module AzureRM.AzureStackStorage -Force
    Uninstall-Module -Name AzureStack -Force
    Get-Module Azs.* -ListAvailable | Uninstall-Module -Force
  ````

2. Excluir todas as pastas que começam com `Azure` do `C:\Program Files\WindowsPowerShell\Modules` e `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` pastas. A exclusão dessas pastas remove todos os módulos do PowerShell existentes.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Conectado: Instale o PowerShell para o Azure Stack com conectividade com a Internet

O Azure Stack requer o **2018-03-01-hybrid** perfil da versão de API para a versão do Azure Stack 1808. O perfil está disponível por meio da instalação do **AzureRM.Bootstrapper** módulo. Além disso, para os módulos AzureRM, você também deve instalar os módulos do PowerShell do Azure específicas de pilha. O perfil da versão de API e os módulos do Azure Stack PowerShell você exige dependerá da versão do Azure Stack estiver em execução.

Execute o seguinte script do PowerShell para instalar esses módulos em sua estação de trabalho de desenvolvimento:

  - O Azure Stack 1808 ou posterior.

    ```PowerShell  
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0 -Force
    ```

> [!Note]  
> Para atualizar o Azure PowerShell do **2017-03-09-perfil** ao **2018-03-01-hybrid**, consulte a [guia de migração](https://github.com/bganapa/azure-powershell/blob/migration-guide/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

  - O Azure Stack 1807 ou anterior.

    ```PowerShell  
    Install-Module -Name AzureRm.BootStrapper
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.4.0 -Force
    ```

  - O Azure Stack 1804 ou anterior.

    ```PowerShell  
    Install-Module -Name AzureRm.BootStrapper
    Use-AzureRmProfile -Profile 2017-03-09-profile -Force
    Install-Module -Name AzureStack -RequiredVersion 1.2.11 -Force
    ```

Confirme a instalação executando o seguinte comando:

```PowerShell  
Get-Module "Azure*" -ListAvailable
Get-Module "Azs*" -ListAvailable
```

Se a instalação for bem-sucedida, os módulos AzureRM e AzureStack são exibidos na saída.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Desconectado: Instale o PowerShell sem uma conexão de Internet

Em um cenário desconectado, você deve primeiro baixar os módulos do PowerShell para um computador que tenha conectividade com a Internet e, em seguida, transferi-las para o Kit de desenvolvimento do Azure Stack para instalação.

Entre em um computador com acesso à Internet e usar os scripts a seguir para baixar os pacotes do Azure Resource Manager e AzureStack, dependendo da sua versão do Azure Stack:

  - O Azure Stack 1808 ou posterior.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

      $Path = "<Path that is used to save the packages>"
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
      Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ````

  - O Azure Stack 1807 ou anterior.

    > [!Note]  
    Para atualizar a partir de 1.2.11 versão, consulte a [guia de migração](https://aka.ms/azspowershellmigration).

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.4.0
    ````

  - O Azure Stack 1804 ou anterior.

    ````PowerShell  
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 1.2.11
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.3.0
    ````

2. Copie os pacotes baixados para um dispositivo USB.

3. Entrar para a estação de trabalho e copie os pacotes do dispositivo USB em um local na estação de trabalho.

4. Agora Registre esse local como o repositório padrão e instalar os módulos AzureRM e AzureStack deste repositório:

   ```PowerShell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

   Install-Module AzureRM -Repository $RepoName

   Install-Module AzureStack -Repository $RepoName 
   ```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Configurar o PowerShell para usar um servidor proxy

Em cenários que exigem um servidor proxy para acessar a Internet, você primeiro deve configurar o PowerShell para usar um servidor proxy existente:

1. Abra um prompt elevado do PowerShell.
2. Execute os seguintes comandos:

   ```PowerShell  
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Próximas etapas

 - [Baixar ferramentas do Azure Stack no GitHub](azure-stack-powershell-download.md)
 - [Configurar o ambiente do PowerShell do usuário do Azure Stack](user/azure-stack-powershell-configure-user.md)  
 - [Configurar o ambiente do PowerShell do operador do Azure Stack](azure-stack-powershell-configure-admin.md) 
 - [Gerenciar perfis de versão de API no Azure Stack](user/azure-stack-version-profiles.md)  
