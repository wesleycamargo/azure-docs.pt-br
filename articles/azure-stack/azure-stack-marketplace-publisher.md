---
title: Use o Kit de ferramentas do Marketplace para criar e publicar itens do marketplace | Microsoft Docs
description: Aprenda a criar rapidamente os itens do marketplace com a publicação do Kit de ferramentas
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/24/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 98a7ae31aeeae29b81a1053b38992d3bc4e0e03f
ms.sourcegitcommit: 5de9de61a6ba33236caabb7d61bee69d57799142
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/25/2018
ms.locfileid: "50085302"
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Adicionar itens do marketplace usando a ferramenta de publicação

Adicionar seu conteúdo para o [Marketplace do Azure Stack](azure-stack-marketplace.md) torna suas soluções disponíveis para você e seus locatários para implantação. O Kit de ferramentas do Marketplace cria arquivos de pacote (. azpkg) do Azure Marketplace com base em seus modelos de IaaS do Azure Resource Manager ou extensões de VM. Você também pode usar o Kit de ferramentas do Marketplace para publicar arquivos. azpkg, criado com a ferramenta ou usando [manual](azure-stack-create-and-publish-marketplace-item.md) etapas. Este tópico descreve a baixar a ferramenta, criando um item do marketplace com base em um modelo de VM e, em seguida, publicar esse item para o Azure Stack Marketplace.     

## <a name="prerequisites"></a>Pré-requisitos

 - Você deve executar o Kit de ferramentas no host do Azure Stack ou ter [VPN](.\asdk\asdk-connect.md#connect-with-vpn) conectividade com o host ASDK do computador no qual você executa a ferramenta.

 - Baixe o [modelos de início rápido do Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) e extraia.

 - Baixe o [ferramenta de empacotamento da Galeria do Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - A publicação no Marketplace requer ícones e um arquivo de miniatura. Você pode usar seu próprio ou salvar o [amostra](azure-stack-marketplace-publisher.md#support-files) arquivos localmente para este exemplo.

## <a name="download-the-tool"></a>Baixar a ferramenta

Você pode baixar o Kit de ferramentas do Marketplace [do repositório de ferramentas do Azure Stack](azure-stack-powershell-download.md).

##  <a name="create-marketplace-items"></a>Criar itens do marketplace

Nesta seção, você pode usar o Kit de ferramentas do Marketplace para criar um pacote de item do marketplace no formato. azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Forneça as informações do marketplace com o Assistente

1. Execute o Kit de ferramentas do Marketplace em uma sessão do PowerShell:
   ```PowerShell
   .\MarketplaceToolkit.ps1
   ```

2. Clique o **solução** guia. Esta tela aceita informações sobre o item do marketplace. Insira informações sobre o item que você deseja que ele apareça no marketplace. Você também pode especificar uma [arquivo de parâmetros](azure-stack-marketplace-publisher.md#use-a-parameters-file) para preencher previamente o formulário.  
    
    ![captura de tela da primeira tela do Kit de ferramentas do Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Clique em **procurar** e selecione um arquivo de imagem para cada campo de ícone e captura de tela. Você pode usar seus próprios ícones ou os ícones de exemplo na [dão suporte a arquivos](azure-stack-marketplace-publisher.md#support-files) seção.
4. Depois que todos os campos são preenchidos, selecione "Solução de visualização" para visualizar a solução no Marketplace. Você pode revisar e editar o texto, imagens e captura de tela antes de clicar em **próxima**.  

### <a name="import-template-and-create-package"></a>Importar o modelo e criar pacote

Nesta seção, você importa o modelo e trabalhar com a entrada para sua solução.

1.  Clique em **navegue** e selecione o *azuredeploy. JSON* da pasta 101-Simple-Windows-VM nos modelos baixados.

    ![captura de tela da segunda tela do Kit de ferramentas do Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  O Assistente de implantação é populado com um *básica* itens de entrada e de etapa para cada parâmetro especificado no modelo. Você pode adicionar etapas adicionais e mover as entradas entre as etapas. Por exemplo, convém "Configuração de front-end" e "Configuração de Back-End" etapas para sua solução.
3.  Especifique o caminho para AzureGalleryPackager.exe.  
4.  Clique em **Criar**. O Kit de ferramentas do Marketplace empacota sua solução em um arquivo. azpkg. Ao concluir, o assistente exibe o caminho para o arquivo da solução e fornecer a você a opção de continuar publicando o seu pacote para o Azure Stack.

## <a name="publish-marketplace-items"></a>Publicar itens do marketplace

Nesta seção, você pode publicar o item do marketplace para sua pilha do Azure Marketplace.

![captura de tela da primeira tela do Kit de ferramentas do Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  O assistente requer informações para publicar sua solução:
    
    |Campo|DESCRIÇÃO|
    |-----|-----|
    | Nome do administrador de serviço | Conta de administrador de serviço.  Exemplo: ServiceAdmin@mydomain.onmicrosoft.com |
    | Senha | Senha da conta de administrador de serviço. |
    | Ponto de extremidade de API | Ponto de extremidade de pilha do Azure Resource Manager do Azure. Exemplo: management.local.azurestack.external |
2.  Clique em **publicar** e o log de publicação é exibido.
3.  Você agora é capazes de implantar seu item publicado por meio do portal do Azure Stack.

## <a name="use-a-parameters-file"></a>Usar um arquivo de parâmetros

Você também pode usar um arquivo de parâmetros para concluir as informações de item do marketplace.  

O Kit de ferramentas do Marketplace inclui um *solution.parameters.ps1* você pode usar para criar seu próprio arquivo de parâmetros.

## <a name="support-files"></a>Arquivos de suporte

| DESCRIÇÃO | Amostra |
| ----- | ----- |
| ícone do 40 x 40. PNG | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| ícone do 90 x 90. PNG | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| ícone do 115 x 115. PNG | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| ícone do 255 x 115. PNG | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| miniatura do 533, 324. PNG | ![](./media/azure-stack-marketplace-publisher/image5.png) |

## <a name="next-steps"></a>Próximas etapas

[Baixar itens do Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)