---
title: "Configurações de implantação para o Kit de desenvolvimento do Azure pilha (ASDK) de postagem | Microsoft Docs"
description: "Descreve as alterações de configuração recomendada para fazer depois de instalar o Kit de desenvolvimento de pilha do Azure (ASDK)."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 2183576e87aa2fb31f8be8f676a5aee7d52f68df
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="post-asdk-installation-configuration-tasks"></a>Pós-instalação de ASDK tarefas de configuração
Depois de [instalando o ASDK](asdk-install.md), há algumas alterações de configuração de pós-instalação recomendada ser feita. 

## <a name="install-azure-stack-powershell"></a>Instalar o PowerShell do Azure Stack 
Pilha do Azure compatíveis módulos do PowerShell do Azure são necessárias para trabalhar com a pilha do Azure.

Comandos do PowerShell para Azure pilha são instalados por meio da Galeria do PowerShell. Para registrar o repositório PSGallery, abra uma sessão do PowerShell com privilégios elevados e execute o seguinte comando:

``` Powershell
Set-PSRepository `
  -Name "PSGallery" `
  -InstallationPolicy Trusted
```

 Módulos de AzureRM compatíveis pilha do Azure são instalados por meio de perfis de versão de API. A pilha do Azure exige que o perfil de versão de API de 2017-03-09-perfil, que está disponível ao instalar o módulo AzureRM.Bootstrapper. 
 
 Você pode instalar o Azure PowerShell de pilha com ou sem conectividade com a internet no computador de host ASDK:

- **Com uma conexão de internet** do computador host ASDK. Execute o seguinte script do PowerShell para instalar esses módulos em sua instalação do kit de desenvolvimento:

  ``` PowerShell
  # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet 
  Install-Module `
    -Name AzureRm.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile `
    -Profile 2017-03-09-profile -Force

  Install-Module `
    -Name AzureStack `
    -RequiredVersion 1.2.11
  ```
  Se a instalação for bem-sucedida, os módulos AzureRM e AzureStack são exibidos na saída.

- **Sem uma conexão de internet** do computador host ASDK. Em um cenário desconectado, você deve primeiro baixar os módulos do PowerShell para uma máquina que tenha conectividade com a internet usando os seguintes comandos do PowerShell:

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
    -RequiredVersion 1.2.11
  ```
  Em seguida, copie os pacotes baixados para o computador ASDK e registrar o local como o repositório padrão e instalar os módulos AzureRM e AzureStack deste repositório:

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

## <a name="download-the-azure-stack-tools"></a>Baixar as ferramentas de pilha do Azure
[Ferramentas de AzureStack](https://github.com/Azure/AzureStack-Tools) é um repositório do GitHub que hospeda os módulos do PowerShell para gerenciar e implantar recursos a pilha do Azure. Para obter essas ferramentas, clone o repositório GitHub ou baixar a pasta Ferramentas AzureStack executando o script a seguir:

  ```PowerShell
  # Change directory to the root directory. 
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip `
    -DestinationPath . `
    -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Validar a instalação ASDK
Para garantir que a implantação de ASDK foi bem-sucedida, você pode usar o cmdlet Test-AzureStack seguindo estas etapas:

1. Faça logon como AzureStack\CloudAdmin no computador host ASDK.
2. Abra o PowerShell como administrador (não o PowerShell ISE).
3. Execute: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Execute: `Test-AzureStack`

Os testes levar alguns minutos para ser concluída. Se a instalação foi bem-sucedida, a saída parecida com:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Se houver uma falha, siga as etapas de solução de problemas para obter ajuda.

## <a name="activate-the-administrator-and-tenant-portals"></a>Ativar os portais de administrador e locatário
Depois de implantações que usam o AD do Azure, você deve ativar ambos os portais de administrador e locatário pilha do Azure. Essa ativação consente fornecendo o portal de pilha do Azure e o Azure Resource Manager as permissões corretas (listadas na página de consentimento) para todos os usuários do diretório.

- Para o portal do administrador, navegue até a https://adminportal.local.azurestack.external/guest/signup, leia as informações e, em seguida, clique em **aceitar**. Depois de aceitar, você pode adicionar administradores de serviço que não são administradores de locatário de diretório também.

- Para o portal de locatário, navegue até https://portal.local.azurestack.external/guest/signup, leia as informações e, em seguida, clique em **aceitar**. Após aceitar, os usuários no diretório podem entrar portal de locatário. 

> [!NOTE] 
> Se os portais não estão ativados, somente o administrador de diretório pode entrar e usar os portais. Se outro usuário fizer logon, eles verão um erro informando que o administrador não concedeu permissões a outros usuários. Quando o administrador não pertence ao diretório para que Azure pilha está registrada nativamente, o diretório de pilha do Azure deve ser anexado à URL de ativação. Por exemplo, se for registrada Azure pilha fabrikam.onmicrosoft.com e o usuário administrador é admin@contoso.com, navegue até https://portal.local.azurestack.external/guest/signup/fabrikam.onmicrosoft.com para ativar o portal. 

## <a name="reset-the-password-expiration-policy"></a>Redefina a política de expiração de senha 
Para certificar-se de que a senha para o host do kit de desenvolvimento não expira antes de seu período de avaliação terminar, siga estas etapas depois de implantar o ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Para alterar a política de expiração de senha do Powershell:
Em um console do Powershell com privilégios elevados, execute o comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Para alterar manualmente a política de expiração de senha:
1. No host do kit de desenvolvimento, abra **Group Policy Management** (GPMC. MMC) e navegue até **Group Policy Management** – **floresta: azurestack.local** – **domínios** – **azurestack.local**.
2. Clique com botão direito **Default Domain Policy** e clique em **editar**.
3. No Editor de gerenciamento de diretiva de grupo, navegue até **configuração do computador** – **políticas** – **configurações do Windows** – **as configurações de segurança**– **Políticas de conta** – **política de senha**.
4. No painel direito, clique duas vezes em **duração máxima da senha**.
5. No **duração máxima da senha propriedades** caixa de diálogo, altere o **senha expirará em** valor **180**e, em seguida, clique em **Okey**.

![Console de gerenciamento de diretiva de grupo](media/asdk-post-deploy/gpmc.png)


## <a name="next-steps"></a>Próximas etapas
[Registrar o ASDK com o Azure](asdk-register.md)
