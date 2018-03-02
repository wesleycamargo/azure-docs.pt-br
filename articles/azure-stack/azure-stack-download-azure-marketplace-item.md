---
title: Fazer o download de itens do marketplace do Azure | Microsoft Docs
description: "Posso baixar itens do marketplace do Azure para minha implantação da pilha do Azure."
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/22/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 3437bc9f164cbdc6c923498b978291ced6278744
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/28/2018
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Fazer o download de itens do marketplace do Azure para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*


Como decidir qual conteúdo para incluir no seu mercado de pilha do Azure, você deve considerar o conteúdo disponível no Azure marketplace. Você pode baixar em uma lista de curadoria de itens do marketplace do Azure que foram previamente testados para executar na pilha do Azure. Com frequência, novos itens são adicionados a essa lista, portanto certifique-se de verificar para o novo conteúdo.

## <a name="download-marketplace-items-in-a-connected-scenario-with-internet-connectivity"></a>Fazer o download de itens do marketplace em um cenário conectado (com conectividade com a internet)

1. Para baixar itens do marketplace, você deve primeiro [registrar pilha do Azure com o Azure](azure-stack-register.md).
2. Entrar no portal do administrador do Azure pilha (https://portal.local.azurestack.external).
3. Alguns itens do marketplace podem ser grandes. Verifique se há espaço suficiente em seu sistema clicando **provedores de recursos** > **armazenamento**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

4. Clique em **mais serviços** > **Marketplace gerenciamento**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Clique em **adicionar do Azure** para ver uma lista de itens disponíveis para download. Você pode clicar em cada item na lista para exibir sua descrição e tamanho do download.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Selecione o item desejado na lista e, em seguida, clique em **baixar**. A imagem VM para o item selecionado começa a fazer o download. Tempos de download variam.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Após a conclusão do download, você pode implantar o novo item do marketplace como um usuário ou o operador de pilha do Azure. Clique em **+ novo**entre as categorias para o novo item do marketplace de pesquisa e, em seguida, selecione o item.
7. Clique em **criar** para abrir a experiência de criação do item baixado recentemente. Siga as instruções passo a passo para implantar o item.

## <a name="download-marketplace-items-in-a-disconnected-or-a-partially-connected-scenario-with-limited-internet-connectivity"></a>Download de itens do marketplace em um desconectado ou um cenário parcialmente conectado (com conectividade de internet limitada)

Quando você implanta a pilha do Azure em modo desconectado (sem qualquer conectividade com a internet), não é possível fazer o download de itens do marketplace usando o portal de pilha do Azure. No entanto, você pode usar a ferramenta de distribuição do marketplace para baixar os itens do marketplace para um computador que tenha conectividade com a internet e, em seguida, transferi-los para seu ambiente de pilha do Azure.

### <a name="prerequisites"></a>Pré-requisitos
Antes de usar a ferramenta de distribuição do marketplace, certifique-se de que você tenha [registrado pilha do Azure com sua assinatura do Azure](azure-stack-register.md).  

Da máquina que tenha conectividade com a internet, use as etapas a seguir para baixar os itens do marketplace necessária:

1. Abra um console do PowerShell como administrador e [instalar módulos do PowerShell específicos do Azure pilha](azure-stack-powershell-install.md). Certifique-se de que você instale **PowerShell versão 1.2.11 ou superior**.  

2. Adicione a conta do Azure que você usou para registrar a pilha do Azure. Para adicionar a conta, execute o **AzureRmAccount adicionar** cmdlet sem parâmetros. Você for solicitado a digitar suas credenciais de conta do Azure e talvez você precise usar a autenticação de 2 fatores com base na configuração da sua conta.  

3. Se você tiver várias assinaturas, execute o seguinte comando para selecionar o que você usou para registro:  

   ```powershell
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Baixe a versão mais recente da ferramenta de distribuição do marketplace usando o script a seguir:  

   ```PowerShell
   # Download the tools archive.
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd \AzureStack-Tools-master

   ```

5. Importe o módulo de distribuição e iniciar a ferramenta executando os seguintes comandos:  

   ```powershell
   Import-Module .\ Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "<Destination folder path>" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Quando a ferramenta é executada, você precisará inserir suas credenciais de conta do Azure. Entrar para a conta do Azure que você usou para registrar a pilha do Azure. Após o logon for bem-sucedido, você deverá ver a tela a seguir com a lista de itens do marketplace disponíveis.  

   ![Pop-up itens do Azure Marketplace](./media/azure-stack-download-azure-marketplace-item/image05.png)

7. Selecione a imagem que você deseja baixar e anote a versão da imagem. Você pode selecionar várias imagens, mantendo a tecla Ctrl. Você pode usar a versão da imagem para importar a imagem na próxima seção.  Em seguida, clique em **Okey**e, em seguida, aceite os termos legais clicando em **Sim**. Você também pode filtrar a lista de imagens usando o **adicionar critérios** opção. 

   O download demora algum tempo dependendo do tamanho da imagem. Uma vez os downloads de imagem, ele está disponível no caminho de destino que você forneceu anteriormente. O download contém os itens de arquivo e a Galeria VHD no formato Azpkg.

### <a name="import-the-image-and-publish-it-to-azure-stack-marketplace"></a>Importar a imagem e publicá-lo no marketplace de pilha do Azure

1. Depois de baixar o pacote de imagem e galeria, salvá-los e o conteúdo na pasta de ferramentas AzureStack-mestre para uma unidade de disco removível e copiá-lo para o ambiente de pilha do Azure (você pode copiá-lo localmente em qualquer local, como: "C:\MarketplaceImages").     

2. Antes de importar a imagem, você deve conectar-se ao ambiente do operador de pilha do Azure usando as etapas descritas em [configurar o ambiente do PowerShell do operador da pilha do Azure](azure-stack-powershell-configure-admin.md).  

3. Importe a imagem para a pilha do Azure usando o cmdlet Add-AzsVMImage. Ao usar esse cmdlet, certifique-se de substituir o *publicador*, *oferecem*e outros valores de parâmetros com os valores da imagem que você está importando. Você pode obter o *publicador*, *oferecem*, e *sku* valores da imagem do objeto imageReference do arquivo Azpkg que você baixou anteriormente e o  *versão* valor da etapa 6 na seção anterior.

   ```json
   "imageReference": {
      "publisher": "MicrosoftWindowsServer",
      "offer": "WindowsServer",
      "sku": "2016-Datacenter-Server-Core"
    }
   ```

   Substitua os valores de parâmetro e execute o seguinte comando:

   ```powershell
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   Add-AzsVMImage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
    -CreateGalleryItem $False `
    -Location Local
   ```

4. Portal de uso para carregar o item do Marketplace (. Azpkg) para o armazenamento de BLOBs do Azure pilha. Você pode carregar no armazenamento do Azure pilha local ou carregar no armazenamento do Azure. (É um local temporário para o pacote.) Certifique-se de que o blob é acessível publicamente e observe o URI.  

5. Publicar o item do marketplace a pilha do Azure usando o **AzsGalleryItem adicionar**. Por exemplo: 

   ```powershell
   Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.2.azpkg" `
     –Verbose
   ```

6. Depois que o item de galeria é publicado, você pode exibi-lo no **novo** > **Marketplace** painel.  

   ![Marketplace](./media/azure-stack-download-azure-marketplace-item/image06.png)

## <a name="next-steps"></a>Próximas etapas

[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)
