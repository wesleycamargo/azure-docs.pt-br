---
title: Baixar itens do marketplace do Azure | Microsoft Docs
description: O operador de nuvem pode baixar itens do marketplace do Azure para minha implantação do Azure Stack.
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
ms.date: 09/12/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: ddb1fcd91ff0c0018bcab9988a5ab063b882cf36
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714649"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Baixar itens do marketplace do Azure para o Azure Stack

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Como um operador de nuvem, baixar itens do Azure Marketplace e torná-los disponíveis no Azure Stack. São os itens que você pode escolher de uma lista estruturada de itens do Marketplace do Azure previamente testada e com suporte para trabalhar com o Azure Stack. Itens adicionais com frequência são adicionados a essa lista, então, continue verificar novo conteúdo. 

Há dois cenários para conectar-se no Azure Marketplace: 

- **Um cenário conectado** -que requer que o seu ambiente do Azure Stack para ser conectado à internet. Você pode usar o portal do Azure Stack para localizar e baixar itens. 
- **Um cenário parcialmente conectado ou desconectado** -que exige que você acessar a Internet usando a ferramenta de sindicalização do marketplace para baixar itens do marketplace. Em seguida, você deve transferir seus downloads à instalação do Azure Stack desconectada. Esse cenário usa o PowerShell.

Ver [itens do Azure Marketplace para o Azure Stack](azure-stack-marketplace-azure-items.md) para obter uma lista dos itens de marketplace, você pode baixar.


## <a name="connected-scenario"></a>Cenário conectado
Se a pilha do Azure se conecta à internet, você pode usar o portal de administração para baixar itens do marketplace.

### <a name="prerequisites"></a>Pré-requisitos
Sua implantação do Azure Stack deve ter conectividade com a internet e ser [registrado com o Azure](azure-stack-register.md).

### <a name="use-the-portal-to-download-marketplace-items"></a>Usar o portal para baixar itens do marketplace  
1. Entrar no portal do administrador do Azure Stack.

2.  Examine o espaço de armazenamento disponível antes de baixar itens do marketplace. Posteriormente, quando você seleciona itens para download, você pode comparar o tamanho do download e sua capacidade de armazenamento disponível. Se a capacidade é limitada, considere as opções para [Gerenciando o espaço disponível](azure-stack-manage-storage-shares.md#manage-available-space). 

    Para examinar o espaço disponível, em **gerenciamento de região** selecione a região que você deseja explorar e, em seguida, vá para **provedores de recursos** > **armazenamento**.

    ![Examine o espaço de armazenamento](media/azure-stack-download-azure-marketplace-item/storage.png)  

    
3. Abra o Azure Stack Marketplace e se conectar ao Azure. Para fazer isso, selecione **gerenciamento do Marketplace**e, em seguida, selecione **Add do Azure**.

    ![Adicionar a partir do Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    O portal exibe a lista de itens disponíveis para download do Azure Marketplace. Você pode clicar em cada item para exibir sua descrição e informações adicionais sobre ele, incluindo o tamanho do download. 

    ![Lista de Marketplace](media/azure-stack-download-azure-marketplace-item/image03.png)

4. Selecione o item que você deseja e, em seguida, selecione **baixar**. Tempos de download variam de acordo.

    ![Baixar mensagem](media/azure-stack-download-azure-marketplace-item/image04.png)

    Após a conclusão do download, você pode implantar o novo item do marketplace como um usuário ou operador do Azure Stack.

5. Para implantar o item baixado, selecione **+ criar um recurso**e, em seguida, pesquise entre as categorias para o novo item do marketplace. Em seguida, selecione o item para iniciar o processo de implantação. O processo varia para itens do marketplace diferentes. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Desconectado ou um cenário parcialmente conectado

Se o Azure Stack está em um modo desconectado e sem conectividade com a internet, você usar o PowerShell e o *ferramenta de distribuição de mercado* para baixar os itens do marketplace para um computador com acesso à internet. Você, em seguida, transfere os itens para seu ambiente do Azure Stack. Em um ambiente desconectado, você não pode baixar itens do marketplace usando o portal do Azure Stack. 

A ferramenta de sindicalização do marketplace também pode ser usada em um cenário conectado. 

Há duas partes que compõem esse cenário:
- **Parte 1:** baixar do Marketplace do Azure. No computador com acesso à internet você configurar o PowerShell, baixe a ferramenta de distribuição e, em seguida, baixe o formulário de itens no Azure Marketplace.  
- **Parte 2:** carregar e publicar no Azure Stack Marketplace. Mover os arquivos baixados para seu ambiente do Azure Stack, importá-los para o Azure Stack e, em seguida, publicá-los no Azure Stack Marketplace.  


### <a name="prerequisites"></a>Pré-requisitos
- Deve ser a sua implantação do Azure Stack [registrado com o Azure](azure-stack-register.md).  

- O computador que tenha conectividade com a internet deve ter **módulo do Azure Stack PowerShell versão 1.2.11** ou superior. Se ainda não estiver presente [instalar módulos do PowerShell específicos do Azure Stack](azure-stack-powershell-install.md).  

- Habilitar Importação de um item do marketplace baixado, a [ambiente do PowerShell para o operador do Azure Stack](azure-stack-powershell-configure-admin.md) deve ser configurado.  

- Você deve ter uma [conta de armazenamento](azure-stack-manage-storage-accounts.md) no Azure Stack que tem um contêiner publicamente acessível (que é um blob de armazenamento). Você pode usar o contêiner como armazenamento temporário para os arquivos de galeria de itens do marketplace. Se você não estiver familiarizado com contêineres e contas de armazenamento, consulte [trabalhar com blobs - portal do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) na documentação do Azure.

- A ferramenta de sindicalização do marketplace é baixada durante o primeiro procedimento. 

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Use a ferramenta de sindicalização do marketplace para baixar itens do marketplace

1. Em um computador com uma conexão de Internet, abra um console do PowerShell como administrador.

2. Adicione a conta do Azure que você usou para registrar o Azure Stack. Para adicionar a conta, no PowerShell execute `Add-AzureRmAccount` sem parâmetros. Você for solicitado a inserir suas credenciais de conta do Azure e você talvez precise usar a autenticação de fator de 2, com base na configuração da sua conta.

3. Se você tiver várias assinaturas, execute o seguinte comando para selecionar aquela que você usou para o registro:  

   ```PowerShell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Baixe a versão mais recente da ferramenta de sindicalização do marketplace usando o script a seguir:  

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

5. Importe o módulo de distribuição e, em seguida, inicie a ferramenta, executando o script a seguir. Substitua os *caminho da pasta de destino* com um local para armazenar os arquivos baixados do Azure Marketplace.   

   ```PowerShell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Sync-AzSOfflineMarketplaceItem `
     -destination "Destination folder path" `
     -AzureTenantID $AzureContext.Tenant.TenantId `
     -AzureSubscriptionId $AzureContext.Subscription.Id  
   ```

6. Quando a ferramenta é executada, você precisará inserir suas credenciais de conta do Azure. Entrar para a conta do Azure que você usou para registrar o Azure Stack. Depois que o logon for bem-sucedido, você verá uma tela semelhante à imagem a seguir, com a lista de itens do marketplace disponíveis.  

   ![Pop-up itens do Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png)

7. Selecione o item que você deseja baixar e anote o *versão*. (Você pode manter o *Ctrl* tecla para selecionar várias imagens.) Você poderá fazer referência a *versão* quando você importa o item no próximo procedimento. 
   
   Você também pode filtrar a lista de imagens usando o **adicionar critérios** opção.

8. Selecione **Okey**e, em seguida, revise e aceite os termos legais. 

9. A hora em que o download leva depende do tamanho do item. Após a conclusão do download, o item está disponível na pasta que você especificou no script. O download inclui um arquivo VHD (para máquinas virtuais) ou um. Arquivo ZIP (para extensões de máquina virtual). Ele também inclui um pacote da galeria na *. azpkg* formato. (Um *. azpkg* pacote é um *. zip* arquivo.)
 

### <a name="import-the-download-and-publish-to-azure-stack-marketplace"></a>O download de importar e publicar no Marketplace do Azure Stack
1. Os arquivos para imagens de máquinas virtuais ou modelos de solução que você tenha [baixados anteriormente](#use-the-marketplace-syndication-tool-to-download-marketplace-items) devem ser disponibilizados localmente para seu ambiente do Azure Stack.  

2. Use o portal de administração para carregar o pacote de item do marketplace (o arquivo. azpkg) para o armazenamento de BLOBs do Azure Stack. Upload do pacote torna disponível para o Azure Stack para que mais tarde, você pode publicar o item a pilha do Azure Marketplace.

   Upload exige que você tiver uma conta de armazenamento com um contêiner publicamente acessível (consulte os pré-requisitos para esse cenário)   
   1. No portal de administração do Azure Stack, acesse **todos os serviços** e, em seguida, sob o **dados + armazenamento** categoria, selecione **contas de armazenamento**.  
   
   2. Selecione uma conta de armazenamento de sua assinatura e, em seguida, em **serviço BLOB**, selecione **contêineres**.  
      ![Serviço BLOB](media/azure-stack-download-azure-marketplace-item/blob-service.png)  
   
   3. Selecione o contêiner que você deseja usar e, em seguida, selecione **carregue** para abrir o **carregar blob** painel.  
      ![Contêiner](media/azure-stack-download-azure-marketplace-item/container.png)  
   
   4. No painel de Upload de blob, navegue até os arquivos que você deseja carregar no armazenamento e, em seguida, selecione **carregar**.  
      ![upload](media/azure-stack-download-azure-marketplace-item/upload.png)  

   5. Os arquivos carregados são exibidos no painel de contêiner. Selecione um arquivo e, em seguida, copie a URL dos **propriedades do Blob** painel. Você usará essa URL na próxima etapa, quando você importa o item do marketplace para o Azure Stack.  Na imagem a seguir, o contêiner estiver *test-blob-storage* e o arquivo estiver *Microsoft.WindowsServer2016DatacenterServerCore ARM.1.0.801.azpkg*.  O arquivo é a URL *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  
      ![Propriedades de blob](media/azure-stack-download-azure-marketplace-item/blob-storage.png)  

3. Importar a imagem VHD para o Azure Stack usando o **AzsPlatformimage adicionar** cmdlet. Quando você usa esse cmdlet, substitua os *publisher*, *oferecem*e outros valores de parâmetro com os valores da imagem que você está importando. 

   Você pode obter o *publisher*, *oferecem*, e *sku* valores da imagem do arquivo de texto que baixa com o arquivo AZPKG. O arquivo de texto é armazenado no local de destino. O *versão* valor é a versão observada durante o download do item do Azure no procedimento anterior. 
 
   No script de exemplo a seguir, são usados valores para o Windows Server 2016 Datacenter - máquina virtual de Server Core. Substitua *URI_path* com o caminho para o local de armazenamento de BLOBs para o item.

   ```PowerShell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "URI_path"  
   ```
   **Sobre modelos de solução:** alguns modelos podem incluir um pequeno 3 MB. Arquivo VHD com o nome **fixed3.vhd**. Você não precisa importar esse arquivo para o Azure Stack. Fixed3.vhd.  Esse arquivo é incluído com alguns modelos de solução para atender aos requisitos de publicação para o Azure Marketplace.

   Examine a descrição de modelos e baixar e importar os requisitos adicionais, como VHDs que são necessárias para trabalhar com o modelo de solução.  
   
   **Sobre extensões:** quando você trabalha com extensões de imagem de máquina virtual, use os seguintes parâmetros:
   - *Publicador*
   - *Tipo*
   - *Versão*  

   Você não usar *oferecem* para extensões.   


4.  Usar o PowerShell para publicar o item do marketplace do Azure Stack, usando o **AzsGalleryItem adicionar** cmdlet. Por exemplo:   
    ```PowerShell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     –Verbose
    ```
5. Depois de publicar um item da galeria, ele acessando **todos os serviços**. Em seguida, sob o **gerais** categoria, selecione **Marketplace**.  Se o download está um modelo de solução, verifique se que você adicionar qualquer imagem VHD dependente desse modelo de solução.  
  ![Marketplace de modo de exibição](media/azure-stack-download-azure-marketplace-item/view-marketplace.png)  

> [!NOTE]
> Com o lançamento do Azure Stack PowerShell 1.3.0, agora você pode adicionar extensões de máquina Virtual.

Por exemplo: 

````PowerShell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
````

## <a name="next-steps"></a>Próximas etapas
[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)