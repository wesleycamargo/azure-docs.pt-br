---
title: Adicionar e remover uma imagem de VM para o Azure Stack | Microsoft Docs
description: Adicionar ou Remover imagem personalizada do Windows ou Linux VM da sua organização para locatários usem.
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
ms.date: 1/07/2018
ms.author: mabrigg
ms.reviewer: kivenkat
ms.openlocfilehash: 9f5f3c4ce08cde81c883bfdac87012dcfd80b8a4
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/09/2019
ms.locfileid: "54119723"
---
# <a name="make-a-virtual-machine-image-available-in-azure-stack"></a>Disponibilizar uma imagem de máquina virtual no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

No Azure Stack, você pode disponibilizar imagens de máquinas virtuais para seus usuários. Essas imagens podem ser referenciadas por modelos do Azure Resource Manager, ou você pode adicioná-los na interface do usuário do Azure Marketplace como um item do Marketplace. Você pode usar qualquer uma forma de imagem no Azure Marketplace global ou adicionar sua própria imagem personalizada. Você pode adicionar uma VM usando o portal ou o Windows PowerShell.

## <a name="add-a-vm-image-through-the-portal"></a>Adicionar uma imagem de VM por meio do portal

> [!NOTE]  
> Com esse método, você deve criar o item do Marketplace separadamente.

Imagens devem ser capazes de ser referenciado por um URI de armazenamento de blob. Preparar uma imagem de sistema operacional Windows ou Linux no formato VHD (não VHDX) e, em seguida, carregar a imagem para uma conta de armazenamento no Azure ou Azure Stack. Se sua imagem já está carregada no armazenamento de blob no Azure ou Azure Stack, você pode ignorar a etapa 1.

1. [Carregar uma imagem de VM do Windows Azure para implantações do Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) ou, para uma imagem do Linux, siga as instruções descritas na [máquinas virtuais Linux implantar no Azure Stack](azure-stack-linux.md). Antes de carregar a imagem, é importante considerar os seguintes fatores:

   - O Azure Stack oferece suporte somente a geração de formato de um (1) a VM no VHD de disco fixo. O formato fixo estruturas do disco lógico linearmente dentro do arquivo, de forma esse deslocamento X do disco é armazenado no deslocamento X do blob. Um pequeno rodapé ao final do blob descreve as propriedades do VHD. Para confirmar se o disco é fixa, use o [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) comando do PowerShell.  

    > [!IMPORTANT]  
    >  O Azure Stack não dá suporte para VHDs de disco dinâmico. Redimensionar um disco dinâmico que é anexado a uma VM deixará a VM em um estado com falha. Para atenuar esse problema, exclua a VM sem excluir o disco da VM, um blob VHD em uma conta de armazenamento. A, converta o VHD de um disco dinâmico para um disco fixo e crie novamente a máquina virtual.

   * Ele é mais eficiente para carregar uma imagem no armazenamento de BLOBs do Azure Stack que para o Azure armazenamento de BLOBs porque leva menos tempo para enviar a imagem ao repositório de imagens do Azure Stack.

   * Quando você carrega o [imagem de VM do Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), certifique-se de substituir o **fazer logon no Azure** passo a passo com o [configurar o ambiente do PowerShell do operador do Azure Stack](azure-stack-powershell-configure-admin.md) etapa.  

   * Anote o URI no qual carregar a imagem do armazenamento de BLOBs. O URI do armazenamento de blob tem o seguinte formato: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*. vhd.

   * Para tornar o blob pode ser acessado anonimamente, vá para o contêiner de blob da conta de armazenamento onde o VHD da imagem VM foi carregado. Selecione **Blob**e, em seguida, selecione **política de acesso**. Opcionalmente, você pode gerar uma assinatura de acesso compartilhado para o contêiner e incluí-lo como parte do URI do blob. Essa etapa garante que o blob está disponível para ser usado para adicionar isso como uma imagem. Se o blob não estiver acessível anonimamente, será criada para a imagem de VM em um estado com falha.

   ![Vá para blobs de conta de armazenamento](./media/azure-stack-add-vm-image/image1.png)

   ![Definir o acesso de blob para o público](./media/azure-stack-add-vm-image/image2.png)

2. Entre no Azure Stack como operador. No menu, selecione **todos os serviços**. Em seguida, na **ADMINISTRATION** categoria, selecione **de computação** > **imagens de VM** > **adicionar**.

3. Sob **adicionar uma imagem de VM**, insira o Editor, oferta, SKU e versão da imagem de máquina virtual. Esses segmentos de nome se referir à imagem VM em modelos do Resource Manager. Certifique-se de selecionar o **osType** valor corretamente. Para **URI de Blob de disco do sistema operacional**, insira o URI de Blob em que a imagem foi carregada. Em seguida, selecione **criar** para começar a criar a imagem da VM.

   ![Comece a criar a imagem](./media/azure-stack-add-vm-image/image4.png)

   Quando a imagem é criada com êxito, o status de imagem VM muda para **bem-sucedido**.

4. Para tornar a imagem de máquina virtual mais prontamente disponível para consumo do usuário na interface do usuário, é uma boa ideia [criar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Remover uma imagem de VM por meio do portal

1. Abra o portal de administração em [ https://adminportal.local.azurestack.external ](https://adminportal.local.azurestack.external).

2. Selecione **gerenciamento de Marketplace**e, em seguida, selecione a VM que você deseja excluir.

3. Clique em **Excluir**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Adicionar uma imagem de VM no Marketplace usando o PowerShell

> [!Note]  
> Quando você adiciona uma imagem só estará disponível para o Gerenciador de recursos do Azure com base em modelos e implantações do PowerShell. Para disponibilizar uma imagem para um usuários como um item do marketplace, publicar o item do marketplace usando as etapas neste artigo, [criar e publicar um item do Marketplace](https://docs.microsoft.com/azure/azure-stack/azure-stack-create-and-publish-marketplace-item)

1. [Instale o PowerShell para o Azure Stack](azure-stack-powershell-install.md).  

2. Entre no Azure Stack como um operador. Para obter instruções, consulte [entrar no Azure Stack como um operador](azure-stack-powershell-configure-admin.md).

3. Abra o PowerShell com um prompt com privilégios elevados e execute:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>” `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
  ````

  O **AzsPlatformimage adicionar** cmdlet especifica os valores usados pelos modelos do Azure Resource Manager para fazer referência a imagem da VM. Os valores incluem:
  - **publisher**  
    Por exemplo: `Canonical`  
    O segmento de nome do publicador da imagem da VM que os usuários usam ao implantar a imagem. Um exemplo é **Microsoft**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
  - **offer**  
    Por exemplo: `UbuntuServer`  
    O segmento de nome de oferta da imagem da VM que os usuários usam ao implantar a imagem da VM. Um exemplo é **WindowsServer**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
  - **sku**  
    Por exemplo: `14.04.3-LTS`  
    O segmento de nome SKU da imagem da VM que os usuários usam ao implantar a imagem da VM. Um exemplo é **Datacenter2016**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
  - **version**  
    Por exemplo: `1.0.0`  
    A versão da imagem da VM que os usuários usam ao implantar a imagem da VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
  - **osType**  
    Por exemplo: `Linux`  
    O osType da imagem deve ser **Windows** ou **Linux**.  
  - **OSUri**  
    Por exemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
    Você pode especificar um URI de armazenamento de blob para um `osDisk`.  

    Para obter mais informações, consulte a referência do PowerShell para o [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) cmdlet e a [New DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject) cmdlet.

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Adicionar uma imagem VM personalizada no Marketplace usando o PowerShell
 
1. [Instale o PowerShell para o Azure Stack](azure-stack-powershell-install.md).

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

2. Se usando **serviços de Federação do Active Directory**, use o seguinte cmdlet:

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

3. Entre no Azure Stack como um operador. Para obter instruções, consulte [entrar no Azure Stack como um operador](azure-stack-powershell-configure-admin.md).

4. Crie uma conta de armazenamento no Azure ou Azure Stack global para armazenar a imagem VM personalizada. Para obter instruções, consulte [guia de início rápido: Carregar, baixar e listar blobs usando o portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Preparar uma imagem de sistema operacional Windows ou Linux no formato VHD (não VHDX), carregue a imagem para sua conta de armazenamento e obter o URI de onde a imagem de VM pode ser recuperada pelo PowerShell.  

  ````PowerShell  
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
  ````

6. (Opcionalmente) Você pode carregar uma matriz de discos de dados como parte da imagem da VM. Crie os discos de dados usando o cmdlet New-DataDiskObject. Abra o PowerShell em um prompt elevado e execute:

  ````PowerShell  
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
  ````

7. Abra o PowerShell com um prompt com privilégios elevados e execute:

  ````PowerShell  
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>” -OSType "<ostype>" -OSUri "<osuri>"
  ````

    Para obter mais informações sobre o cmdlet Add-AzsPlatformimage e o cmdlet New-DataDiskObject, consulte o Microsoft PowerShell [documentação do módulo do Azure Stack operador](https://docs.microsoft.com/powershell/module/).

## <a name="remove-a-vm-image-by-using-powershell"></a>Remover uma imagem de VM usando PowerShell

Quando você não precisa mais da imagem de máquina virtual que você carregou, você pode excluí-la do Marketplace usando o seguinte cmdlet:

1. [Instale o PowerShell para o Azure Stack](azure-stack-powershell-install.md).

2. Entre no Azure Stack como um operador.

3. Abra o PowerShell com um prompt com privilégios elevados e execute:

  ````PowerShell  
  Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
  ````
  O **AzsPlatformImage remover** cmdlet especifica os valores usados pelos modelos do Azure Resource Manager para fazer referência a imagem da VM. Os valores incluem:
  - **publisher**  
    Por exemplo: `Canonical`  
    O segmento de nome do publicador da imagem da VM que os usuários usam ao implantar a imagem. Um exemplo é **Microsoft**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
  - **offer**  
    Por exemplo: `UbuntuServer`  
    O segmento de nome de oferta da imagem da VM que os usuários usam ao implantar a imagem da VM. Um exemplo é **WindowsServer**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
  - **sku**  
    Por exemplo: `14.04.3-LTS`  
    O segmento de nome SKU da imagem da VM que os usuários usam ao implantar a imagem da VM. Um exemplo é **Datacenter2016**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
  - **version**  
    Por exemplo: `1.0.0`  
    A versão da imagem da VM que os usuários usam ao implantar a imagem da VM. Esta versão está no formato  *\#.\#. \#*. Um exemplo é **1.0.0**. Não inclua um espaço ou outros caracteres especiais nesse campo.  
    
    Para obter mais informações sobre o cmdlet Remove-AzsPlatformImage, consulte o Microsoft PowerShell [documentação do módulo do Azure Stack operador](https://docs.microsoft.com/powershell/module/).

## <a name="next-steps"></a>Próximas etapas

[Provisionar uma máquina virtual](azure-stack-provision-vm.md)
