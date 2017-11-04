---
title: Publicar um item do marketplace personalizado na pilha do Azure (operador de nuvem) | Microsoft Docs
description: Como um operador de pilha do Azure, saber como publicar um item do marketplace personalizado na pilha do Azure.
services: azure-stack
documentationcenter: 
author: ErikjeMS
manager: byronr
editor: 
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: erikje
ms.openlocfilehash: 7b5f976eb2d51eb86761a2bd0be6adb45ca87681
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/11/2017
---
# <a name="the-azure-stack-marketplace-overview"></a>Visão geral do Azure Marketplace de pilha

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

O Marketplace é uma coleção de serviços, aplicativos e recursos personalizados de pilha do Azure, como máquinas virtuais, redes, armazenamento e assim por diante. Os usuários se aqui para criar novos recursos e implantar novos aplicativos. Pense nisso como um catálogo de compra em que os usuários podem procurar e escolha os itens que desejam usar. Para usar um item do Marketplace, os usuários devem assinar uma oferta que lhe dá acesso ao item.

Como um operador de pilha do Azure, você decide quais itens adicionará (publicação) para o Marketplace. Você pode publicar como bancos de dados, serviços de aplicativos e assim por diante. Isso os torna visível para todos os seus usuários. Você pode publicar itens personalizados que você criar. Você também pode publicar itens de um crescente [lista de itens do Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando você publica um item para o Marketplace, os usuários podem vê-lo dentro de cinco minutos.

Para abrir o Marketplace, clique em **Novo**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Itens do Marketplace
Um item do Marketplace de pilha do Azure é um serviço, aplicativo ou recurso que os usuários podem baixar e usar. Todos os itens do Marketplace de pilha do Azure são visíveis para todos os seus usuários.

Cada item do Marketplace contém:

* Um modelo do Gerenciador de Recursos do Azure para o provisionamento de recursos
* Metadados, como cadeias de caracteres, ícones e outros manuais e acessórios de marketing
* Informações de formatação para exibir o item no portal

Cada item publicado no Marketplace usa um formato chamado Pacote da Galeria do Azure (azpkg). Adicione recursos de implantação ou tempo de execução (como código, arquivos zip com o software ou imagens da máquina virtual) com a pilha do Azure separadamente, não como parte do Item do Marketplace. 

## <a name="next-steps"></a>Próximas etapas
[Criar e publicar um item do marketplace](azure-stack-create-and-publish-marketplace-item.md)

