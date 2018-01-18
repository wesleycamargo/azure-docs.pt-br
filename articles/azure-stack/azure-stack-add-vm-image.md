---
title: Adicionar uma imagem de VM a pilha do Azure | Microsoft Docs
description: "Adicione imagem personalizada do Windows ou a VM do Linux da sua organização para locatários para usar."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/17/2018
ms.author: mabrigg
ms.openlocfilehash: 3b228452d416bbb2c54243b95292f7e1198af14f
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/18/2018
---
# <a name="make-a-custom-virtual-machine-image-available-in-azure-stack"></a>Disponibilizar uma imagem de máquina virtual personalizada na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Na pilha do Azure, operadores podem disponibilizar imagens de máquina virtual personalizada para seus usuários. Essas imagens podem ser referenciadas por modelos do Azure Resource Manager, ou você pode adicioná-los para a interface do usuário do Azure Marketplace como um item do Marketplace.

## <a name="add-a-vm-image-to-marketplace-by-using-powershell"></a>Adicionar uma imagem de VM no Marketplace usando o PowerShell

Execute os seguintes pré-requisitos do [kit de desenvolvimento](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-remote-desktop) ou de um cliente com Windows externo, se você estiver [conectado por meio de VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn):

1. [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).  

2. Baixe o [ferramentas necessárias para trabalhar com o Azure pilha](azure-stack-powershell-download.md).  

3. Preparar uma imagem de disco rígido virtual do sistema operacional Windows ou Linux no formato VHD (não use o formato VHDX).

   * Para imagens do Windows, para obter instruções sobre como preparar a imagem, consulte [carregar uma imagem de VM do Windows Azure para implantações do Gerenciador de recursos](../virtual-machines/windows/upload-generalized-managed.md).

   * Para imagens do Linux, consulte [máquinas virtuais Linux implantar no Azure pilha](azure-stack-linux.md). Conclua as etapas para preparar a imagem ou usar uma imagem do Linux de pilha do Azure existente, conforme descrito no artigo.    

   Pilha do Azure suporta o formato VHD de disco fixo. O formato fixo estruturas do disco lógico linearmente dentro do arquivo, para que esse deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé no final do blob descreve as propriedades do VHD. Para confirmar se o disco é fixo, use o [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando do PowerShell.  

   > [!IMPORTANT]
   >  A pilha do Azure não oferece suporte a VHDs de disco dinâmico. Redimensionar um disco dinâmico que é anexado a uma VM deixará a VM em um estado de falha. Para atenuar esse problema, exclua a máquina virtual sem excluir o disco da VM, um blob VHD em uma conta de armazenamento. A, converta o VHD de um disco dinâmico em um disco fixo e recriar a máquina virtual.

Para adicionar a imagem para a pilha do Azure Marketplace, conclua as seguintes etapas:

1. Importe os módulos conectar e ComputeAdmin:

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
          -EnvironmentName AzureStackAdmin

        Login-AzureRmAccount `
          -EnvironmentName "AzureStackAdmin" `
          -TenantId $TenantID
        ```

3. Adicionar a imagem VM invocando o `Add-AzsVMImage` cmdlet. No `Add-AzsVMImage` cmdlet, especifique `osType` como Windows ou Linux. Inclua o publicador, oferta, SKU e a versão da imagem de VM. Para obter informações sobre parâmetros permitidos, consulte [parâmetros](#parameters). Os parâmetros são usados pelos modelos do Gerenciador de recursos do Azure para fazer referência à imagem VM. O exemplo a seguir chama o script:

  ```powershell
  Add-AzsVMImage `
    -publisher "Canonical" `
    -offer "UbuntuServer" `
    -sku "14.04.3-LTS" `
    -version "1.0.0" `
    -osType Linux `
    -osDiskLocalPath 'C:\Users\AzureStackAdmin\Desktop\UbuntuServer.vhd' `
  ```

O comando faz o seguinte:

* Autentica para o ambiente de pilha do Azure.
* Carrega o VHD do local para uma conta de armazenamento temporário criado recentemente.
* Adiciona a imagem VM para o repositório de imagens VM.
* Cria um item do Marketplace.

Para verificar se o comando foi executado com êxito, no portal, vá para o Marketplace. Verifique se a imagem da VM está disponível na **máquinas virtuais** categoria.

![Imagem VM adicionada com êxito](./media/azure-stack-add-vm-image/image5.PNG)

## <a name="remove-a-vm-image-by-using-powershell"></a>Remover uma imagem de VM usando o PowerShell

Quando você não precisa mais a imagem de máquina virtual que você carregou, você pode excluí-lo do Marketplace usando o seguinte cmdlet:

```powershell
Remove-AzsVMImage `
  -publisher "Canonical" `
  -offer "UbuntuServer" `
  -sku "14.04.3-LTS" `
  -version "1.0.0" `
```

## <a name="parameters"></a>parâmetros

| Parâmetro | DESCRIÇÃO |
| --- | --- |
| **publisher** |O segmento de nome do publicador da imagem da VM que os usuários a usar ao implantar a imagem. Um exemplo é **Microsoft**. Não inclua um espaço ou outros caracteres especiais neste campo. |
| **offer** |O segmento de nome de oferta da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **WindowsServer**. Não inclua um espaço ou outros caracteres especiais neste campo. |
| **sku** |O segmento de nome do SKU da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **Datacenter2016**. Não inclua um espaço ou outros caracteres especiais neste campo. |
| **version** |A versão da imagem da VM que os usuários a usar ao implantar a imagem VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Não inclua um espaço ou outros caracteres especiais neste campo. |
| **osType** |OsType da imagem deve ser **Windows** ou **Linux**. |
| **osDiskLocalPath** |O caminho local para o disco do sistema operacional VHD que você está carregando como uma imagem VM com a pilha do Azure. |
| **dataDiskLocalPaths** |Uma matriz opcional dos caminhos de local para os discos de dados que podem ser carregados como parte da imagem da VM. |
| **CreateGalleryItem** |Um sinalizador booleano que determina se é necessário criar um item no Marketplace. Por padrão, ele é definido como **true**. |
| **title** |O nome de exibição do item do Marketplace. Por padrão, ele é definido como o `Publisher-Offer-Sku` valor da imagem da VM. |
| **description** |A descrição do item do Marketplace. |
| **local** |O local onde a imagem VM deve ser publicada. Por padrão, esse valor é definido como **local**.|
| **osDiskBlobURI** |(Opcional) Esse script também aceita um URI de armazenamento de Blob para `osDisk`. |
| **dataDiskBlobURIs** |(Opcional) Esse script também aceita uma matriz de armazenamento de Blob URIs para a adição de discos de dados para a imagem. |

## <a name="add-a-vm-image-through-the-portal"></a>Adicionar uma imagem VM por meio do portal

> [!NOTE]
> Com esse método, você deve criar o item do Marketplace separadamente.

Imagens devem ser capazes de ser referenciado por um URI de armazenamento de Blob. Preparar uma imagem do sistema operacional Windows ou Linux no formato VHD não VHDX () e, em seguida, carregá-la para uma conta de armazenamento no Azure ou a pilha do Azure. Se a imagem já está carregada para o armazenamento de Blob no Azure ou a pilha do Azure, você pode ignorar a etapa 1.

1. [Carregar uma imagem de VM do Windows Azure para implantações do Gerenciador de recursos](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, para uma imagem do Linux, siga as instruções descritas em [máquinas virtuais Linux implantar no Azure pilha](azure-stack-linux.md). Antes de carregar a imagem, é importante considerar os seguintes fatores:

   * Pilha do Azure suporta o formato VHD de disco fixo. O formato fixo estruturas do disco lógico linearmente dentro do arquivo, para que esse deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé no final do blob descreve as propriedades do VHD. Para confirmar se o disco é fixo, use o [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando do PowerShell.  

    > [!IMPORTANT]
   >  A pilha do Azure não oferece suporte a VHDs de disco dinâmico. Redimensionar um disco dinâmico que é anexado a uma VM deixará a VM em um estado de falha. Para atenuar esse problema, exclua a máquina virtual sem excluir o disco da VM, um blob VHD em uma conta de armazenamento. A, converta o VHD de um disco dinâmico em um disco fixo e recriar a máquina virtual.

   * É mais eficiente para carregar uma imagem para o armazenamento de BLOBs do Azure pilha que para o armazenamento de BLOBs do Azure, pois leva menos tempo para enviar por push a imagem para o repositório de imagens de pilha do Azure.

   * Quando você carrega o [imagem de VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), certifique-se de substituir o **logon para o Azure** etapa com o [configurar o ambiente do PowerShell do operador Azure pilha](azure-stack-powershell-configure-admin.md) etapa.  

   * Anote o armazenamento de Blob onde você carrega a imagem do URI. O URI de armazenamento de Blob tem o seguinte formato:  *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* . vhd.

   * Para disponibilizar o blob anonimamente, vá para o contêiner de blob da conta de armazenamento onde a imagem VM VHD foi carregada. Selecione **Blob**e, em seguida, selecione **política de acesso**. Opcionalmente, você pode em vez disso, gerar uma assinatura de acesso compartilhado para o contêiner e incluí-lo como parte do URI do blob.

   ![Vá para blobs de conta de armazenamento](./media/azure-stack-add-vm-image/image1.png)

   ![Definir o acesso de blob para public](./media/azure-stack-add-vm-image/image2.png)

2. Entre no Azure pilha como operador. No menu, selecione **mais serviços** > **provedores de recursos**. Em seguida, selecione **de computação** > **imagens da VM** > **adicionar**.

3. Em **adicionar uma imagem de VM**, insira o publicador, oferta, SKU e versão da imagem de máquina virtual. Esses segmentos de nome, consulte a imagem VM em modelos do Gerenciador de recursos. Certifique-se de selecionar o **osType** valor corretamente. Para **URI de Blob de disco do sistema operacional**, insira o URI de Blob onde a imagem foi carregada. Em seguida, selecione **criar** para começar a criar a imagem de VM.

   ![Comece a criar a imagem](./media/azure-stack-add-vm-image/image4.png)

   Quando a imagem é criada com êxito, o status de imagem VM seja **êxito**.

4. Para tornar a imagem de máquina virtual mais prontamente disponível para consumo do usuário na interface de usuário, é uma boa ideia [criar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="next-steps"></a>Próximas etapas

[Provisionar uma máquina virtual](azure-stack-provision-vm.md)
