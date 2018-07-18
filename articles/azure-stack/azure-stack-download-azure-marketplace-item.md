---
title: Fazer o download de itens do marketplace do Azure | Microsoft Docs
description: O operador de nuvem pode baixar itens do marketplace do Azure para minha implantação da pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 5d403f7c1d0fff466f6c0fb9942ec777ab820eab
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34604525"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Fazer o download de itens do marketplace do Azure para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como um operador de nuvem, fazer o download de itens do Marketplace do Azure e disponibilizá-las na pilha do Azure. São os itens que você pode escolher de uma lista de curadoria de itens do Azure Marketplace previamente testados e suportados para trabalhar com a pilha do Azure. Itens adicionais com frequência são adicionados a essa lista, para continuar a verificar para o novo conteúdo. 

Há dois cenários para conectar-se ao Azure Marketplace: 

- **Um cenário conectado** -que requer que seu ambiente de pilha do Azure a ser conectado à internet. Você pode usar o portal do Azure pilha para localizar e baixar itens. 
- **Um cenário parcialmente conectado ou desconectado** -que requer acesso à internet usando a ferramenta de distribuição do marketplace para baixar itens do marketplace. Em seguida, transferir os downloads para a instalação desconectada de pilha do Azure. Esse cenário usa o PowerShell.

Consulte [itens do Azure Marketplace para pilha Azure](azure-stack-marketplace-azure-items.md) para obter uma lista dos itens do marketplace, você pode baixar.


## <a name="connected-scenario"></a>Cenário conectado
Se a pilha do Azure se conecta à internet, você pode usar o portal de administração para baixar itens do marketplace.

### <a name="prerequisites"></a>Pré-requisitos
A implantação do Azure pilha deve ter conectividade com a internet e ser [registrado com o Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Use o portal para baixar itens do marketplace  
1. Entre no portal de administrador a pilha do Azure.

2.  Reveja o espaço de armazenamento disponível antes de baixar itens do marketplace. Posteriormente, quando você seleciona itens para download, você pode comparar o tamanho do download e a capacidade de armazenamento disponível. Se a capacidade é limitada, considere as opções de [Gerenciando o espaço disponível](azure-stack-manage-storage-shares.md#manage-available-space). 

    Para revisar o espaço disponível, em **gerenciamento região** selecionar a região que você deseja explorar e, em seguida, vá para **provedores de recursos** > **armazenamento**.

    ![Examine o espaço de armazenamento](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Abra a pilha do Azure Marketplace e se conectar ao Azure. Para fazer isso, selecione **gerenciamento Marketplace**e, em seguida, selecione **adicionar do Azure**.

    ![Adicionar a partir do Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    O portal exibe a lista de itens disponíveis para download do Azure Marketplace. Você pode clicar em cada item para exibir sua descrição e informações adicionais sobre ele, incluindo o tamanho do download. 

    ![Lista do Marketplace](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Selecione o item desejado e, em seguida, selecione **baixar**. Tempos de download variam.

    ![Baixar a mensagem](media/azure-stack-download-azure-marketplace-item/image04.png)

    Após a conclusão do download, você pode implantar o novo item do marketplace como um usuário ou o operador de pilha do Azure.

5. Para implantar o item baixado, selecione **+ novo**e, em seguida, procure entre as categorias para o novo item do marketplace. Em seguida, selecione o item para iniciar o processo de implantação. O processo varia para itens do marketplace diferentes. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Desconectado ou um cenário parcialmente conectado

Se a pilha do Azure está em modo desconectado e sem conectividade com a internet, você usar o PowerShell e o *ferramenta de distribuição do marketplace* para baixar os itens do marketplace para um computador com conexão à internet. Você, em seguida, transferir os itens para o seu ambiente de pilha do Azure. Em um ambiente desconectado, você não pode baixar os itens do marketplace usando o portal de pilha do Azure. 

A ferramenta de distribuição do marketplace também pode ser usada em um cenário conectado. 

Há duas partes que compõem esse cenário:
- **Parte 1:** baixar do Marketplace do Azure. No computador com acesso à internet configurar o PowerShell, baixe a ferramenta de distribuição e, em seguida, baixe o formulário de itens do Azure Marketplace.  
- **Parte 2:** carregar e publicar no Marketplace de pilha do Azure. Mova os arquivos baixados para seu ambiente de pilha do Azure, importá-los para a pilha do Azure e, em seguida, publicá-los para a pilha do Azure Marketplace.  


### <a name="prerequisites"></a>Pré-requisitos
- A implantação do Azure pilha deve ser [registrado com o Azure](azure-stack-register.md).  

- O computador que tenha conectividade com a internet deve ter **módulo PowerShell da pilha do Azure versão 1.2.11** ou superior. Se não estiver presente, [instalar módulos do PowerShell específicos do Azure pilha](azure-stack-powershell-install.md).  

- Para habilitar a importação de um item do marketplace baixado, o [ambiente do PowerShell para o operador de pilha do Azure](azure-stack-powershell-configure-admin.md) devem ser configurados.  

- Você deve ter uma [conta de armazenamento](azure-stack-manage-storage-accounts.md) na pilha do Azure que tem um contêiner publicamente acessível (que é um blob de armazenamento). Você pode usar o contêiner como armazenamento temporário para os arquivos de galeria de itens do marketplace. Se você não estiver familiarizado com as contas de armazenamento e contêineres, consulte [trabalhar com blobs - portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) na documentação do Azure.

- A ferramenta de distribuição do marketplace é baixada durante o primeiro procedimento. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Use a ferramenta de distribuição do marketplace para baixar itens do marketplace

1. Em um computador com uma conexão de Internet, abra um console do PowerShell como administrador.

2. Adicione a conta do Azure que você usou para registrar a pilha do Azure. Para adicionar a conta de execução PowerShell `Add-AzureRmAccount` sem parâmetros. Você for solicitado a digitar suas credenciais de conta do Azure e talvez você precise usar a autenticação de 2 fatores com base na configuração da sua conta.

3. Se você tiver várias assinaturas, execute o seguinte comando para selecionar o que você usou para registro:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Baixe a versão mais recente da ferramenta de distribuição do marketplace usando o script a seguir:  

   ```PowerShell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importe o módulo de distribuição e, em seguida, inicie a ferramenta executando o script a seguir. Substitua o *caminho de pasta de destino* com um local para armazenar os arquivos de download do Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Quando a ferramenta é executada, você precisará inserir suas credenciais de conta do Azure. Entrar para a conta do Azure que você usou para registrar a pilha do Azure. Depois que o logon for bem-sucedido, você verá uma tela de imagem a seguir, a lista de itens do marketplace disponíveis.  

   ![Pop-up itens do Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Selecione o item que você deseja baixar e anote o *versão*. (Você pode selecionar várias imagens, mantendo o *Ctrl* chave.) Você irá referenciar o *versão* quando você importa o item no próximo procedimento. 
   
   Você também pode filtrar a lista de imagens usando o **adicionar critérios** opção.

8. Selecione **Okey**e, em seguida, leia e aceite os termos legais. 

9. O tempo que leva o download depende do tamanho do item. Após a conclusão do download, o item está disponível na pasta que você especificou no script. O download inclui um arquivo VHD (para máquinas virtuais) ou um. Arquivo ZIP (para extensões de máquina virtual). Ele também inclui um pacote de galeria no *.azpkg* formato. (Um *.azpkg* pacote é um *. zip* arquivo.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>Importar o download e publicar no Azure Marketplace de pilha
1. Os arquivos de imagens de máquinas virtuais ou modelos de solução que você tem [baixados anteriormente](#use-the-marketplace-syndication-tool-to-download-marketplace-items) devem ser disponibilizados localmente para seu ambiente de pilha do Azure.  

2. Importe. Arquivos VHD para a pilha do Azure. Para importar com êxito uma imagem de máquina virtual (VM), você deve ter as seguintes informações sobre a máquina virtual:
   - O *versão*, conforme observado na etapa 7 do procedimento anterior.
   - Os valores para as VMs *publicador*, *oferecem*, e *sku*. Para obter esses valores, renomeie uma cópia do **.azpkg** arquivo para alterar sua extensão de arquivo para **. zip**. Você pode usar um editor de texto para abrir **DeploymentTemplates\CreateUiDefinition.json**. No arquivo. JSON, localize o *imageReference* seção, que contém os valores para o item do marketplace. O exemplo a seguir demonstra como essas informações são exibidas:

     ```json  
     "imageReference": {  
        "publisher": "MicrosoftWindowsServer",  
        "offer": "WindowsServer",  
        "sku": "2016-Datacenter-Server-Core"  
      }
     ```  

   Importar a imagem de pilha do Azure usando o **AzsPlatformimage adicionar** cmdlet. Ao usar esse cmdlet, certifique-se de substituir o *publicador*, *oferecem*e outros valores de parâmetros com os valores da imagem que você está importando. Você pode obter o *publicador*, *oferecem*, e *sku* valores da imagem do arquivo de texto que é baixado junto com o arquivo AZPKG e armazenado no local de destino . 

   No script de exemplo a seguir, os valores para o Datacenter do Windows Server 2016 - máquina virtual de Server Core são usados. 

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsDiskLocalPath "C:\AzureStack-Tools-master\Syndication\Windows-Server-2016-DatacenterCore-20171215-en.us-127GB.vhd" `
   ```
   **Sobre modelos de solução:** alguns modelos podem incluir um pequeno 3 MB. Arquivo VHD com o nome **fixed3.vhd**. Você não precisa importar esse arquivo para a pilha do Azure. Fixed3.vhd.  Esse arquivo é incluído com alguns modelos de solução para atender aos requisitos de publicação para o Azure Marketplace.

   Revise a descrição de modelos e baixar e importar requisitos adicionais como VHDs que são necessárias para trabalhar com o modelo de solução.

3. Use o portal de administração para carregar o pacote de item do marketplace (o arquivo .azpkg) para o armazenamento de BLOBs do Azure pilha. Carregamento do pacote disponibiliza a pilha do Azure para que mais tarde, você pode publicar o item a pilha do Azure Marketplace.

   Carregamento requer que você tenha uma conta de armazenamento com um contêiner publicamente acessível (consulte os pré-requisitos para esse cenário)   
   1. No portal de administração de pilha do Azure, vá para **mais serviços** > **contas de armazenamento**.  
   
   2. Selecione uma conta de armazenamento de sua assinatura e, em seguida, em **serviço BLOB**, selecione **contêineres**.  
      ![Serviço BLOB](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Selecione o contêiner que deseja usar e, em seguida, selecione **carregar** para abrir o **carregar blob** painel.  
      ![Contêiner](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. No painel de blob de carregamento, navegue até os arquivos que você deseja carregar no armazenamento e, em seguida, selecione **carregar**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Arquivos carregados aparecem no painel de contêiner. Selecione um arquivo e, em seguida, copie a URL do **propriedades de Blob** painel. Você usará essa URL na próxima etapa, quando você importa o item do marketplace a pilha do Azure.  Na imagem a seguir, o contêiner é *armazenamento de blob teste* e o arquivo é *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  O arquivo de URL é *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Propriedades de blob](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

4.  Use o PowerShell para publicar o item do marketplace a pilha do Azure usando o **AzsGalleryItem adicionar** cmdlet. Por exemplo:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Depois de publicar um item da galeria, você pode exibi-lo de **mais serviços** > **Marketplace**.  Se o seu download é um modelo de solução, verifique se que você adicionar qualquer imagem VHD dependente desse modelo de solução.  
  ![Marketplace do modo de exibição](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Com o lançamento do Azure PowerShell de pilha 1.3.0, agora você pode adicionar extensões de máquina Virtual.

Por exemplo: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Próximas etapas
[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)