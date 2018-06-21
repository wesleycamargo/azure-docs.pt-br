---
title: Adicionar e remover uma imagem de VM com a pilha do Azure | Microsoft Docs
description: Adicionar ou Remover imagem personalizada do Windows ou a VM do Linux da sua organização para locatários para usar.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: e5a4236b-1b32-4ee6-9aaa-fcde297a020f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 05/24/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 714afa1da5d2c8c5695dfe33edd0257f69af149d
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/20/2018
ms.locfileid: "36287697"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Disponibilizar uma imagem de máquina virtual na pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Na pilha do Azure, você pode disponibilizar imagens da máquina virtual para os usuários. Essas imagens podem ser referenciadas por modelos do Azure Resource Manager, ou você pode adicioná-los para a interface do usuário do Azure Marketplace como um item do Marketplace. Você pode usar qualquer forma uma imagem global do Azure Marketplace ou adicionar sua própria imagem personalizada. Você pode adicionar uma VM usando o portal ou o Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Adicionar uma imagem VM por meio do portal

> [!NOTE]
> Com esse método, você deve criar o item do Marketplace separadamente.

Imagens devem ser capazes de ser referenciado por um URI de armazenamento de blob. Preparar uma imagem do sistema operacional Windows ou Linux no formato VHD não VHDX () e, em seguida, carregá-la para uma conta de armazenamento no Azure ou a pilha do Azure. Se a imagem já está carregada para o armazenamento de blob no Azure ou a pilha do Azure, você pode ignorar a etapa 1.

1. [Carregar uma imagem de VM do Windows Azure para implantações do Gerenciador de recursos](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, para uma imagem do Linux, siga as instruções descritas em [máquinas virtuais Linux implantar no Azure pilha](azure-stack-linux.md). Antes de carregar a imagem, é importante considerar os seguintes fatores:

   - Pilha do Azure suporta o formato VHD de disco fixo. O formato fixo estruturas do disco lógico linearmente dentro do arquivo, para que esse deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé no final do blob descreve as propriedades do VHD. Para confirmar se o disco é fixo, use o [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando do PowerShell.  

    > [!IMPORTANT]
    >  A pilha do Azure não oferece suporte a VHDs de disco dinâmico. Redimensionar um disco dinâmico que é anexado a uma VM deixará a VM em um estado de falha. Para atenuar esse problema, exclua a máquina virtual sem excluir o disco da VM, um blob VHD em uma conta de armazenamento. A, converta o VHD de um disco dinâmico em um disco fixo e recriar a máquina virtual.

   * É mais eficiente para carregar uma imagem para o armazenamento de BLOBs do Azure pilha que o Azure armazenamento de blob porque leva menos tempo para enviar por push a imagem para o repositório de imagens de pilha do Azure.

   * Quando você carrega o [imagem de VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), certifique-se de substituir o **logon para o Azure** etapa com o [configurar o ambiente do PowerShell do operador Azure pilha](azure-stack-powershell-configure-admin.md) etapa.  

   * Anote o armazenamento de blob onde você carrega a imagem do URI. O URI de armazenamento de blob tem o seguinte formato: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*. vhd.

   * Para disponibilizar o blob anonimamente, vá para o contêiner de blob da conta de armazenamento onde a imagem VM VHD foi carregada. Selecione **Blob**e, em seguida, selecione **política de acesso**. Opcionalmente, você pode em vez disso, gerar uma assinatura de acesso compartilhado para o contêiner e incluí-lo como parte do URI do blob.

   ![Vá para blobs de conta de armazenamento](./media/azure-stack-add-vm-image/image1.png)

   ![Definir o acesso de blob para public](./media/azure-stack-add-vm-image/image2.png)

2. Entre no Azure pilha como operador. No menu, selecione **mais serviços** > **provedores de recursos**. Em seguida, selecione **de computação** > **imagens da VM** > **adicionar**.

3. Em **adicionar uma imagem de VM**, insira o publicador, oferta, SKU e versão da imagem de máquina virtual. Esses segmentos de nome, consulte a imagem VM em modelos do Gerenciador de recursos. Certifique-se de selecionar o **osType** valor corretamente. Para **URI de Blob de disco do sistema operacional**, insira o URI de Blob onde a imagem foi carregada. Em seguida, selecione **criar** para começar a criar a imagem de VM.

   ![Comece a criar a imagem](./media/azure-stack-add-vm-image/image4.png)

   Quando a imagem é criada com êxito, o status de imagem VM seja **êxito**.

4. Para tornar a imagem de máquina virtual mais prontamente disponível para consumo do usuário na interface de usuário, é uma boa ideia [criar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Remover uma imagem de VM por meio do portal

1. Abra o portal de administração em [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Selecione **gerenciamento Marketplace**e, em seguida, selecione a VM que você deseja excluir.

3. Clique em **Excluir**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Adicionar uma imagem de VM para o Marketplace usando o PowerShell

1. [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).  

2. Entre pilha do Azure como um operador. Para obter instruções, consulte [entrar pilha do Azure como um operador](azure-stack-powershell-configure-admin.md).

3. Abra o PowerShell com um prompt com privilégios elevados e execute:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  O **AzsPlatformimage adicionar** cmdlet especifica valores usados pelos modelos de Gerenciador de recursos do Azure para fazer referência à imagem VM. Os valores incluem:
  - **publisher**  
    Por exemplo: `Canonical`  
    O segmento de nome do publicador da imagem da VM que os usuários a usar ao implantar a imagem. Um exemplo é **Microsoft**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **offer**  
    Por exemplo: `UbuntuServer`  
    O segmento de nome de oferta da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **WindowsServer**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **sku**  
    Por exemplo: `14.04.3-LTS`  
    O segmento de nome do SKU da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **Datacenter2016**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **version**  
    Por exemplo: `1.0.0`  
    A versão da imagem da VM que os usuários a usar ao implantar a imagem VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **osType**  
    Por exemplo: `Linux`  
    OsType da imagem deve ser **Windows** ou **Linux**.  
  - **OSUri**  
    Por exemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Você pode especificar um URI de armazenamento de blob para um `osDisk`.  

    Para obter mais informações, consulte a referência do PowerShell para o [adicionar AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet e o [DataDiskObject novo](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Adicionar uma imagem VM personalizada para o Marketplace usando o PowerShell

1. [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).

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

3. Entre pilha do Azure como um operador. Para obter instruções, consulte [entrar pilha do Azure como um operador](azure-stack-powershell-configure-admin.md).

4. Crie uma conta de armazenamento no Azure ou Azure pilha global para armazenar a imagem VM personalizada. Para obter instruções, consulte [início rápido: listar blobs usando o portal do Azure, carregamento e download](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Preparar uma imagem do sistema operacional Windows ou Linux no formato VHD (não VHDX), carregar a imagem à sua conta de armazenamento e obter o URI de onde a imagem VM pode ser recuperada pelo PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Opcionalmente) Você pode carregar uma matriz de discos de dados como parte da imagem da VM. Crie os discos de dados usando o cmdlet New-DataDiskObject. Abra o PowerShell em um prompt com privilégios elevados e execute:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Abra o PowerShell com um prompt com privilégios elevados e execute:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Para obter mais informações sobre o cmdlet Add-AzsPlatformimage e o cmdlet New-DataDiskObject, consulte o Microsoft PowerShell [documentação do módulo Azure pilha operador](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Remover uma imagem de VM usando o PowerShell

Quando você não precisa mais a imagem de máquina virtual que você carregou, você pode excluí-lo do Marketplace usando o seguinte cmdlet:

1. [Instale o PowerShell para Azure pilha](azure-stack-powershell-install.md).

2. Entre pilha do Azure como um operador.

3. Abra o PowerShell com um prompt com privilégios elevados e execute:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  O **AzsPlatformImage remover** cmdlet especifica valores usados pelos modelos de Gerenciador de recursos do Azure para fazer referência à imagem VM. Os valores incluem:
  - **publisher**  
    Por exemplo: `Canonical`  
    O segmento de nome do publicador da imagem da VM que os usuários a usar ao implantar a imagem. Um exemplo é **Microsoft**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **offer**  
    Por exemplo: `UbuntuServer`  
    O segmento de nome de oferta da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **WindowsServer**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **sku**  
    Por exemplo: `14.04.3-LTS`  
    O segmento de nome do SKU da imagem da VM que os usuários a usar ao implantar a imagem VM. Um exemplo é **Datacenter2016**. Não inclua um espaço ou outros caracteres especiais neste campo.  
  - **version**  
    Por exemplo: `1.0.0`  
    A versão da imagem da VM que os usuários a usar ao implantar a imagem VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Não inclua um espaço ou outros caracteres especiais neste campo.  
    
    Para obter mais informações sobre o cmdlet Remove-AzsPlatformImage, consulte o Microsoft PowerShell [documentação do módulo Azure pilha operador](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Próximas etapas

[Provisionar uma máquina virtual](azure-stack-provision-vm.md)
