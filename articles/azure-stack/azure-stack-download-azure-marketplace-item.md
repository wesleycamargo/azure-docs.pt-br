---
title: Fazer o download de itens do marketplace do Azure | Microsoft Docs
description: "Posso baixar itens do marketplace do Azure para minha implantação da pilha do Azure."
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/03/2017
ms.author: erikje
ms.openlocfilehash: 4d7c335a3c68cc9bb8cb0c823883716a3dd6620a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Fazer o download de itens do marketplace do Azure para a pilha do Azure

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

Como decidir qual conteúdo para incluir no seu mercado de pilha do Azure, você deve considerar o conteúdo disponível no Azure marketplace. Você pode baixar em uma lista de curadoria de itens do marketplace do Azure que foram previamente testados para executar na pilha do Azure. Com frequência, novos itens são adicionados a essa lista, portanto certifique-se de seleção para o novo conteúdo.

Para baixar itens do marketplace, você deve primeiro [registrar pilha do Azure com o Azure](azure-stack-register.md). 

## <a name="download"></a>Baixar
1. Entrar no portal do administrador do Azure pilha (https://portal.local.azurestack.external).
2. Alguns itens do marketplace podem ser muito grandes.  Verifique se há espaço suficiente em seu sistema clicando **provedores de recursos** > **armazenamento**.

    ![](media/azure-stack-download-azure-marketplace-item/image01.png)

3. Clique em **mais serviços** > **Marketplace gerenciamento**.

    ![](media/azure-stack-download-azure-marketplace-item/image02.png)

4. Clique em **adicionar do Azure** para ver uma lista de itens disponíveis para download. Você pode clicar em cada item na lista para exibir sua descrição e tamanho do download.

    ![](media/azure-stack-download-azure-marketplace-item/image03.png)

5. Selecione o item desejado na lista e, em seguida, clique em **baixar**. Isso inicia o download da imagem VM para o item selecionado. Tempos de download variam.

    ![](media/azure-stack-download-azure-marketplace-item/image04.png)

6. Após a conclusão do download, você pode implantar o novo item do marketplace como um usuário ou o operador de pilha do Azure. Clique em **+ novo**entre as categorias para o novo item do marketplace de pesquisa e, em seguida, selecione o item.
7. Clique em **criar** para abrir a experiência de criação do item baixado recentemente. Siga as instruções passo a passo para implantar o item.

## <a name="next-steps"></a>Próximas etapas

[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)
