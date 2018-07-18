---
title: Adicionar um item do marketplace do Azure pilha de local de origem | Microsoft Docs
description: Descreve como adicionar uma imagem de sistema operacional local para a pilha do Azure Marketplace.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/20/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: a093e60718881b2fe9ca70df7596e8963dc55d9f
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/06/2018
ms.locfileid: "34808036"
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

1. [Instale o PowerShell para Azure pilha](../azure-stack-powershell-install.md).

  ```PowerShell  
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint $ArmEndpoint

    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience $GraphAudience

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ```

2. Se usar **os serviços de Federação do Active Directory**, use o seguinte cmdlet:

  ```PowerShell
  # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
  $ArmEndpoint = "<Resource Manager endpoint for your environment>"

  # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
  $GraphAudience = "<GraphAuidence endpoint for your environment>"

  # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
  Add-AzureRMEnvironment `
    -Name "AzureStackAdmin" `
    -ArmEndpoint $ArmEndpoint
    ```

3. Entre pilha do Azure como um operador. Para obter instruções, consulte [entrar pilha do Azure como um operador](../azure-stack-powershell-configure-admin.md).

   ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

4. Adicione a imagem do Windows Server 2016 no mercado de pilha do Azure.

    O **AzsPlatformimage adicionar** cmdlet usado para adicionar a imagem especifica valores usados pelos modelos de Gerenciador de recursos do Azure para fazer referência à imagem VM.
    
    Os valores incluem:
    
  - **publisher**  
    Por exemplo: `Microsoft`  
    O segmento de nome do publicador da imagem da VM que os usuários a usar ao implantar a imagem. Um exemplo é **Microsoft**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **offer**  
    Por exemplo: `WindowsServer`  
    O segmento de nome de oferta da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **WindowsServer**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **sku**  
    Por exemplo: `Datacenter2016`  
    O segmento de nome do SKU da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **Datacenter2016**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **version**  
    Por exemplo: `1.0.0`  
    A versão da imagem da VM que os usuários a usar ao implantar a imagem VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **osType**  
    Por exemplo: `Windows`  
    OsType da imagem deve ser **Windows** ou **Linux**. Substituir *fully_qualified_path_to_ISO* com o caminho para o Windows Server 2016 ISO que você baixou. 
  - **OSUri**  
    Por exemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Você pode especificar um URI de armazenamento de blob para um `osDisk`. No seu caso, você especificará o local onde você armazenou a imagem que você baixou.

    Para obter mais informações, consulte a referência do PowerShell para o [AzsPlatformimage adicionar](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet.

    Abra o PowerShell com um prompt com privilégios elevados e execute:

      ````PowerShell  
        Add-AzsPlatformimage -publisher "Microsoft" -offer "WindowsServer" -sku "Datacenter2016" -version "1.0.0” -OSType "Windows" -OSUri "<fully_qualified_path_to_ISO>"
      ````  

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
  - **Nome**: teste 1 de vm
  - **Nome de usuário**: AdminTestUser
  - **Senha**: AzS TestVM01
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
