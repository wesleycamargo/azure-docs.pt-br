---
title: "Adicionar a imagem VM padrão para a pilha do Azure Marketplace | Microsoft Docs"
description: "Adicione a imagem de VM do Windows Server 2016 padrão para a pilha do Azure Marketplace."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 2849E53F-3D58-48A5-8007-3238FC39F630
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 1/23/2018
ms.author: mabrigg
ms.openlocfilehash: b0b0a4af1d852de516d387697afb2760b967db43
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/24/2018
---
# <a name="add-the-windows-server-2016-vm-image-to-the-azure-stack-marketplace"></a>Adicionar a imagem de VM do Windows Server 2016 para a pilha do Azure Marketplace

Por padrão, nenhuma imagem de máquina virtual está disponíveis no mercado de pilha do Azure. Um operador de pilha do Azure deve adicionar uma imagem do Marketplace para os usuários acessarem. Você pode adicionar a imagem do Windows Server 2016 para a pilha do Azure Marketplace usando um dos seguintes métodos:

* [Baixar a imagem do Azure Marketplace](#add-the-image-by-downloading-it-from-the-azure-marketplace). Use esta opção se você estiver operando em um cenário conectado e registrou sua instância de pilha do Azure com o Azure.

* [Adicionar a imagem usando o PowerShell](#add-the-image-by-using-powershell). Use esta opção se você tiver implantado pilha do Azure em um cenário desconectado ou em cenários com conectividade limitada.

## <a name="add-the-image-by-downloading-it-from-the-azure-marketplace"></a>Adicionar a imagem, baixando-o no Azure Marketplace

1. Implantar pilha do Azure e, em seguida, entre seu Kit de desenvolvimento de pilha do Azure.

2. Selecione **mais serviços** > **Marketplace gerenciamento** > **adicionar do Azure**. 

3. Localizar ou procurar o **Datacenter do Windows Server 2016** da imagem e, em seguida, selecione **baixar**.

   ![Baixar a imagem do Azure](media/azure-stack-add-default-image/download-image.png)

Quando o download estiver concluído, a imagem está disponível em **Marketplace gerenciamento**. A imagem também está disponível em **de computação** e está disponível para criar novas máquinas virtuais.

## <a name="add-the-image-by-using-powershell"></a>Adicionar a imagem usando o PowerShell

### <a name="prerequisites"></a>Pré-requisitos 

Execute os seguintes pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou de um cliente com Windows externo, se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. Instalar [módulos do PowerShell do Azure pilha-compatível com o Azure](azure-stack-powershell-install.md).  

2. Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).  

3. Na página de avaliações do Windows Server, [Baixe a avaliação do Windows Server 2016](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016). Quando solicitado, selecione a versão ISO de download. Registre o caminho para o local de download, que é usado posteriormente as etapas descritas neste artigo. Esta etapa exige conectividade com a internet.  

### <a name="add-the-image-to-the-azure-stack-marketplace"></a>Adicionar a imagem para a pilha do Azure Marketplace
   
1. Importe os módulos do Azure Connect de pilha e ComputeAdmin usando os seguintes comandos:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Faça logon no seu ambiente de pilha do Azure. Execute um dos seguintes scripts, dependendo de você ter implantado em seu ambiente de pilha do Azure usando o Azure Active Directory (AD do Azure) ou os serviços de Federação do Active Directory (AD FS). (Substitua o Azure AD `tenantName`, `GraphAudience` ponto de extremidade, e `ArmEndpoint` valores para refletir a configuração do ambiente.)  

   * **Azure Active Directory**. Use o seguinte cmdlet:

    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.windows.net/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"
    
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

    Login-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID 
    ```

   * **Serviços de Federação do Active Directory**. Use o seguinte cmdlet:
    
    ```PowerShell
    # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
    $ArmEndpoint = "<Resource Manager endpoint for your environment>"

    # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
    $GraphAudience = "<GraphAuidence endpoint for your environment>"

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
   
3. Adicione a imagem do Windows Server 2016 para a pilha do Azure Marketplace. (Substitua *fully_qualified_path_to_ISO* com o caminho para o Windows Server 2016 ISO que você baixou.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

Para garantir que a imagem de VM do Windows Server 2016 tem a atualização cumulativa mais recente, incluem o `IncludeLatestCU` parâmetro ao executar o `New-AzsServer2016VMImage` cmdlet. Para obter informações sobre parâmetros permitidos para o `New-AzsServer2016VMImage` cmdlet, consulte [parâmetros](#parameters). Leva aproximadamente uma hora para publicar a imagem para a pilha do Azure Marketplace. 

## <a name="parameters-for-new-azsserver2016vmimage"></a>Parâmetros para o novo AzsServer2016VMImage

### <a name="new-azsserver2016vmimage"></a>New-AzsServer2016VMImage 

Cria e carrega um novo Server 2016 Core e, ou uma imagem completa e cria um item do marketplace para ele.

| parâmetros | Obrigatório | Exemplo | DESCRIÇÃO |
|-----|-----|------|---- |
|ISOPath|Sim| N:\ISO\en_windows_16_x64_dvd | O caminho totalmente qualificado para o Windows Server 2016 ISO baixado.|
|Net35|Não | True | O tempo de execução do .NET 3.5 é instalado na imagem do Windows Server 2016. Por padrão, esse valor é definido como **true**.|
|Versão|Não | Completo |  Especifica **Core**, **completo**, ou **ambos** imagens do Windows Server 2016. Por padrão, esse valor é definido como **completo**.|
|VHDSizeInMB|Não | 40,960 | Define o tamanho (em MB) da imagem do VHD a ser adicionado ao seu ambiente de pilha do Azure. Por padrão, esse valor é definido como 40.960 MB.|
|CreateGalleryItem|Não | True | Especifica se um item do Marketplace deve ser criado para a imagem do Windows Server 2016. Por padrão, esse valor é definido como **true**.|
|location |Não  | D:\ | Especifica o local para o qual a imagem do Windows Server 2016 deve ser publicada.|
|IncludeLatestCU|Não | Falso | Aplica-se a atualização cumulativa mais recente do Windows Server 2016 para o novo VHD. Verifique o script para garantir que ele aponta para a atualização mais recente ou usar uma das duas opções. |
|CUUri |Não  | https://yourupdateserver/winservupdate2016 | Define o Windows Server 2016 a atualização cumulativa para executar a partir de um URI específico. |
|CUPath |Não  | C:\winservupdate2016 | Define o Windows Server 2016 a atualização cumulativa para executar a partir de um caminho local. Essa opção será útil se você implantou a instância de pilha do Azure em um ambiente desconectado.|

## <a name="next-steps"></a>Próximas etapas

[Provisionar uma máquina virtual](azure-stack-provision-vm.md)
