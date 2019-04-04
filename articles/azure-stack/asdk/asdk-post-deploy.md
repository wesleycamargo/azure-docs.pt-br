---
title: Configurações de implantação para o Azure Stack desenvolvimento ASDK (Kit) de postagem | Microsoft Docs
description: Descreve as alterações de configuração recomendada para fazer depois de instalar o Azure Stack desenvolvimento ASDK (Kit).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 7699c9279138aedfdcfe63fb42e65ad102ac92c9
ms.sourcegitcommit: 22ad896b84d2eef878f95963f6dc0910ee098913
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/29/2019
ms.locfileid: "58652460"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Pós-instalação do ASDK tarefas de configuração

Após [instalando o Azure Stack desenvolvimento ASDK (Kit)](asdk-install.md), você deve fazer algumas alterações de configuração de pós-instalação recomendada enquanto estiver conectado como AzureStack\AzureStackAdmin no computador host ASDK. 

## <a name="install-azure-stack-powershell"></a>Instalar o PowerShell do Azure Stack

O Azure Stack compatível com módulos do PowerShell do Azure são necessárias para trabalhar com o Azure Stack.

Comandos do PowerShell para o Azure Stack são instalados por meio da Galeria do PowerShell. Para registrar o repositório PSGallery, abra uma sessão do PowerShell com privilégios elevados e execute o seguinte comando:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Você pode usar perfis de versão da API para especificar os módulos do AzureRM compatíveis do Azure Stack.  Perfis de versão de API fornecem uma maneira de gerenciar as diferenças de versão entre o Azure e o Azure Stack. Um perfil de versão de API é um conjunto de módulos do PowerShell do AzureRM com versões específicas de API. O **AzureRM.Bootstrapper** módulo que está disponível por meio da Galeria do PowerShell fornece cmdlets do PowerShell que são necessárias para trabalhar com perfis de versão da API.

Você pode instalar o módulo mais recente do PowerShell do Azure Stack com ou sem conectividade com a Internet para o computador de host ASDK:

> [!IMPORTANT]
> Antes de instalar a versão necessária, certifique-se de que você [desinstalar todos os módulos do PowerShell do Azure existentes](../azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Com uma conexão de internet** do computador host ASDK. Execute o seguinte script do PowerShell para instalar esses módulos em sua instalação do kit de desenvolvimento:

- O Azure Stack 1901 ou posterior:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > A versão 1.7.1 do módulo do Azure Stack é uma alteração significativa. Para migrar do Azure Stack 1.6.0, consulte o [guia de migração](https://aka.ms/azspshmigration171).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  - O Azure Stack 1810 ou anterior:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.5.0.
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

  Se a instalação for bem-sucedida, os módulos AzureRM e AzureStack são exibidos na saída.

- **Sem uma conexão de internet** do computador host ASDK. Em um cenário desconectado, você deve primeiro baixar os módulos do PowerShell para uma máquina que tenha conectividade com a internet usando os seguintes comandos do PowerShell:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Em seguida, copie os pacotes baixados para o computador ASDK e registrar o local como o repositório padrão e instalar os módulos AzureRM e AzureStack deste repositório:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Baixe as ferramentas do Azure Stack

[Ferramentas de AzureStack](https://github.com/Azure/AzureStack-Tools) é um repositório GitHub que hospeda os módulos do PowerShell para gerenciar e implantar recursos no Azure Stack. Para obter essas ferramentas, clone o repositório GitHub ou baixar a pasta de ferramentas AzureStack executando o seguinte script:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Validar a instalação ASDK

Para garantir que sua implantação ASDK foi bem-sucedida, você pode usar o cmdlet Test-AzureStack seguindo estas etapas:

1. Faça logon como AzureStack\AzureStackAdmin no computador host ASDK.
2. Abra o PowerShell como administrador (não o PowerShell ISE).
3. Execute: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Execute: `Test-AzureStack`

Os testes demorar alguns minutos para ser concluída. Se a instalação foi bem-sucedida, a saída é semelhante:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Se houver uma falha, siga as etapas de solução de problemas para obter ajuda.

## <a name="reset-the-password-expiration-policy"></a>Redefina a política de expiração de senha 

Para certificar-se de que a senha para o host do kit de desenvolvimento não expire antes de seu período de avaliação terminar, siga estas etapas depois de implantar o ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Para alterar a política de expiração de senha do Powershell

Em um console do Powershell com privilégios elevados, execute o comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Para alterar a política de expiração de senha manualmente

1. No host do kit de desenvolvimento, abra **gerenciamento de política de grupo** (GPMC. MMC) e navegue até **gerenciamento de política de grupo** – **floresta: azurestack. local** – **domínios** – **azurestack**.
2. Clique com botão direito **Default Domain Policy** e clique em **editar**.
3. No Editor de gerenciamento de diretiva de grupo, navegue até **configuração do computador** – **diretivas** – **as configurações do Windows** – **as configurações de segurança**– **Políticas de conta** – **política de senha**.
4. No painel direito, clique duas vezes **duração máxima da senha**.
5. No **duração máxima da senha propriedades** caixa de diálogo, altere o **senha expirará em** valor para **180**e, em seguida, clique em **Okey**.

![Console de gerenciamento de diretiva de grupo](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Habilitar multilocação

Para implantações usando o Azure AD, você precisará [habilitar multilocação](../azure-stack-enable-multitenancy.md#enable-multi-tenancy) para sua instalação ASDK.

> [!NOTE]
> Quando as contas de usuário ou administrador de domínios diferente daquela usada para registrar o Azure Stack são usadas para fazer logon em um portal do Azure Stack, o nome de domínio usado para registrar o Azure Stack deve ser acrescentado ao portal do url. Por exemplo, se o Azure Stack foi registrado com fabrikam.onmicrosoft.com e a conta de usuário, registro em log no está admin@contoso.com, a url a ser usada para fazer logon no portal do usuário seria: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Próximas etapas

[Registre o ASDK com o Azure](asdk-register.md)
