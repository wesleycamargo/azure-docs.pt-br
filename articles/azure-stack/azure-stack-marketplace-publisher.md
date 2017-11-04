---
title: Use o Kit de ferramentas do Marketplace para criar e publicar itens do marketplace | Microsoft Docs
description: "Aprenda a criar rapidamente itens do marketplace com a publicação do Kit de ferramentas"
services: azure-stack
documentationcenter: 
author: HeathL17
manager: ByronR
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 7/14/2017
ms.author: helaw
ms.openlocfilehash: 5b2c04d2cbc06e1572dc2e40712f6cf9d886aa1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
#  <a name="add-marketplace-items-using-publishing-tool"></a>Adicionar itens do marketplace usando a ferramenta de publicação
Adicionando seu conteúdo para o [Azure Marketplace de pilha](azure-stack-marketplace.md) torna as soluções disponíveis para você e seus locatários para implantação.  O Kit de ferramentas do Marketplace cria arquivos de pacotes do Marketplace do Azure (.azpkg) com base em seus modelos de IaaS do Azure Resource Manager ou extensões de VM.  Você também pode usar o Kit de ferramentas do Marketplace para publicar arquivos .azpkg, criada com a ferramenta ou usando [manual](azure-stack-create-and-publish-marketplace-item.md) etapas.  Este tópico orienta você baixar a ferramenta, criando um item do marketplace com base em um modelo de VM e, em seguida, publicando esse item para a pilha do Azure Marketplace.     


## <a name="prerequisites"></a>Pré-requisitos
 - Você deve executar o Kit de ferramentas no host de pilha do Azure ou ter [VPN](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn) conectividade do computador em que você executar a ferramenta.

 - Baixe o [modelos de início rápido do Azure pilha](https://github.com/Azure/AzureStack-QuickStart-Templates/archive/master.zip) e extrair.

 - Baixe o [ferramenta de empacotamento de galeria do Azure](http://aka.ms/azurestackmarketplaceitem) (AzureGalleryPackage.exe). 

 - Publicação para o marketplace requer ícones e um arquivo em miniatura.  Você pode usar seu próprio, ou salvar o [exemplo](azure-stack-marketplace-publisher.md#support-files) arquivos localmente para este exemplo.

## <a name="download-the-tool"></a>Baixe a ferramenta
O Kit de ferramentas do Marketplace pode ser [baixado do repositório Azure pilha ferramentas](azure-stack-powershell-download.md).


##  <a name="create-marketplace-items"></a>Criar itens do marketplace
Nesta seção, você pode usar o Kit de ferramentas do Marketplace para criar um pacote de item do marketplace no formato .azpkg.  

### <a name="provide-marketplace-information-with-wizard"></a>Fornecer informações do marketplace com o Assistente
1. Execute o Kit de ferramentas do Marketplace de uma sessão do PowerShell:
```PowerShell
    .\MarketplaceToolkit.ps1
```

2. Clique o **solução** guia.  Esta tela aceita informações sobre o item do marketplace. Insira informações sobre o item de como você deseja que apareça no marketplace.  Você também pode especificar um [arquivo de parâmetros](azure-stack-marketplace-publisher.md#use-a-parameters-file) para preencher o formulário.  
    
    ![captura de tela da primeira tela do Kit de ferramentas do Marketplace](./media/azure-stack-marketplace-publisher/image7.png)
3. Clique em **procurar** e selecione um arquivo de imagem para cada campo de ícone e captura de tela.  Você pode usar seus próprios ícones ou os ícones de exemplo no [arquivos de suporte](azure-stack-marketplace-publisher.md#support-files) seção.
4. Depois que todos os campos são preenchidos, selecione "Solução de visualização" para visualizar a solução no Marketplace.  Você pode revisar e editar o texto, imagens e captura de tela antes de clicar em **próximo**.  

### <a name="import-template-and-create-package"></a>Importar o modelo e criar pacote
Nesta seção, você importa o modelo e trabalha com a entrada para sua solução.

1.  Clique em **procurar** e selecione o *azuredeploy.json* na pasta 101-simples-Windows-VM os modelos baixados.

    ![captura de tela da segunda tela do Kit de ferramentas do Marketplace](./media/azure-stack-marketplace-publisher/image8.png)
2.  O Assistente de implantação é preenchido com um *básica* etapa e a entrada de itens para cada parâmetro especificada no modelo.  Você pode adicionar etapas adicionais e mover entradas entre etapas.  Por exemplo, convém "Configuração de front-end" e "Configuração de Back-End" etapas para sua solução.
3.  Especifique o caminho para AzureGalleryPackager.exe.  
4.  Clique em **criar** e o Kit de ferramentas do Marketplace pacotes sua solução em um arquivo .azpkg.  Uma vez concluído, o assistente exibe o caminho para o arquivo de solução e oferece a opção para continuar a publicar seu pacote à pilha do Azure.


## <a name="publish-marketplace-items"></a>Publicar itens do marketplace
Nesta seção, você pode publicar o item do marketplace para o Azure Marketplace de pilha.

![captura de tela da primeira tela do Kit de ferramentas do Marketplace](./media/azure-stack-marketplace-publisher/image9.png)

1.  O assistente requer informações para publicar sua solução:
    
    |Campo|Descrição|
    |-----|-----|
    | Nome do administrador de serviço | Conta de administrador de serviço.  Exemplo:ServiceAdmin@mydomain.onmicrosoft.com |
    | Senha | Senha da conta de administrador de serviço. |
    | Ponto de extremidade de API | Ponto de extremidade do Gerenciador de recursos do Azure de pilha do Azure.  Exemplo: management.local.azurestack.external |
2.  Clique em **publicar** e o log de publicação é exibido.
3.  Agora será capaz de implantar o item publicado por meio do portal de pilha do Azure.


## <a name="use-a-parameters-file"></a>Usar um arquivo de parâmetros
Você também pode usar um arquivo de parâmetros para concluir as informações de item do marketplace.  

O Kit de ferramentas do Marketplace inclui um *solution.parameters.ps1* você pode usar para criar seu próprio arquivo de parâmetros.


## <a name="support-files"></a>Arquivos de suporte
| Descrição | Amostra |
| ----- | ----- |
| ícone de PNG 40 x 40 | ![](./media/azure-stack-marketplace-publisher/image1.png) |
| ícone do x 90 a 90. PNG | ![](./media/azure-stack-marketplace-publisher/image2.png) |
| ícone do 115 x 115. PNG | ![](./media/azure-stack-marketplace-publisher/image3.png) |
| ícone do 255 x 115. PNG | ![](./media/azure-stack-marketplace-publisher/image4.png) |
| 533, 324. PNG miniatura | ![](./media/azure-stack-marketplace-publisher/image5.png) |


