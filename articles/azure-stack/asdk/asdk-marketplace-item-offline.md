---
title: Adicionar um item do marketplace do Azure pilha de local de origem | Microsoft Docs
description: Descreve como adicionar uma imagem de sistema operacional local para a pilha do Azure Marketplace.
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
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 296719ddd23fb9ee717455420906e9a634a71a8d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Tutorial: adicionar um item do marketplace do Azure pilha do local de origem

Como um operador de pilha do Azure, a primeira coisa que você precisa fazer para habilitar usuários para implantar uma máquina Virtual (VM) é adicionar uma imagem de VM para o marketplace de pilha do Azure. Por padrão, nada é publicado no mercado de pilha do Azure, mas você pode carregar imagens de VM ISO para torná-los disponíveis aos usuários. Use esta opção se você tiver implantado pilha do Azure em um cenário de desconectado em cenários com conectividade limitada.

Neste tutorial, você baixar uma imagem de VM do Windows Server 2016 das avaliações de servidor Windows página e carregá-lo no mercado de pilha do Azure.

Neste tutorial, você aprenderá como:

> [!div class="checklist"]
> * Adicionar uma imagem de VM do Windows Server 2016
> * Verifique se que a imagem da VM está disponível 
> * A imagem VM de teste

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

- Instalar o [módulos do PowerShell do Azure compatíveis de pilha do Azure](asdk-post-deploy.md#install-azure-stack-powershell)
- Baixe o [ferramentas de pilha do Azure](asdk-post-deploy.md#download-the-azure-stack-tools)
- Baixe o [imagem de ISO de máquina virtual do Windows Server 2106](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) da página de avaliações do Windows Server

## <a name="add-a-windows-server-vm-image"></a>Adicionar uma imagem de VM do Windows Server
Você pode publicar a imagem do Windows Server 2016 no mercado de pilha do Azure, adicionando uma imagem ISO baixada anteriormente usando o PowerShell. 

Use esta opção se você tiver implantado pilha do Azure em um cenário de desconectado em cenários com conectividade limitada.

1. Importar a pilha do Azure `Connect` e `ComputeAdmin` módulos do PowerShell incluídos no diretório de ferramentas do Azure pilha usando os seguintes comandos:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Execute um dos seguintes scripts de logon no computador de host ASDK dependendo se você implantou o seu ambiente de pilha do Azure usando o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS):

  - Comandos para **implantações do AD do Azure**: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - Comandos para **implantações do AD FS**:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Adicione a imagem do Windows Server 2016 no mercado de pilha do Azure. (Substitua *fully_qualified_path_to_ISO* com o caminho para o Windows Server 2016 ISO que você baixou.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>Verifique se que o item do marketplace está disponível
Use estas etapas para verificar se a nova imagem VM está disponível no mercado de pilha do Azure.

1. Entrar para o [portal de administração ASDK](https://adminportal.local.azurestack.external).

2. Selecione **mais serviços** > **Marketplace gerenciamento**. 

3. Verifique se a imagem de VM do Datacenter do Windows Server 2016 foi adicionada com êxito.

   ![Imagem baixada do Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>A imagem VM de teste
Como um operador de pilha do Azure, você pode usar o [portal do administrador](https://adminportal.local.azurestack.external) para criar um teste de VM para validar a imagem ter sido disponibilizada com êxito. 

1. Entrar para o [portal de administração ASDK](https://adminportal.local.azurestack.external).

2. Clique em **novo** > **de computação** > **Datacenter do Windows Server 2016** > **criar**.  
 ![Criar VM a partir da imagem do marketplace](media/asdk-marketplace-item/new-compute.png)

3. No **Noções básicas de** folha, insira as informações a seguir e, em seguida, clique em **Okey**:
  - **Name**: test-vm-1
  - **Nome de usuário**: AdminTestUser
  - **Password**: AzS-TestVM01
  - **Assinatura**: aceitar a assinatura do provedor de padrão
  - **Grupo de recursos**: rg de vm de teste
  - **Local**: local

4. No **escolher um tamanho de** folha, clique em **A1 padrão**e, em seguida, clique em **selecione**.  

5. No **configurações** folha, aceite os padrões e clique em **Okey**

6. No **resumo** folha, clique em **Okey** para criar a máquina virtual.  
> [!NOTE]
> Implantação da máquina virtual leva alguns minutos para ser concluída.

7. Para exibir a nova VM, clique em **máquinas virtuais**, em seguida, procure **teste 1 de vm** e clique em seu nome.
    ![Imagem VM de teste primeiro exibida](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Limpar recursos
Depois de ter com êxito conectado à máquina virtual e verificar se a imagem de teste está funcionando corretamente, você deve excluir o grupo de recursos de teste. Isso liberará recursos limitados disponíveis para instalações de ASDK de nó único.

Quando não é mais necessário, exclua o grupo de recursos, a VM e todos os recursos relacionados seguindo estas etapas:

1. Entrar para o [portal de administração ASDK](https://adminportal.local.azurestack.external).
2. Clique em **grupos de recursos** > **rg de vm de teste** > **excluir grupo de recursos**.
3. Tipo **rg de vm de teste** como o nome do grupo de recursos e clique **excluir**.

## <a name="next-steps"></a>Próximas etapas

Neste tutorial, você aprendeu como:

> [!div class="checklist"]
> * Adicionar uma imagem de VM do Windows Server 2016
> * Verifique se que a imagem da VM está disponível 
> * A imagem VM de teste

Avança para o próximo tutorial para aprender a criar uma oferta de pilha do Azure e o plano.

> [!div class="nextstepaction"]
> [Oferecer serviços do Azure IaaS de pilha](asdk-offer-services.md)