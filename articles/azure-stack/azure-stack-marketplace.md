---
title: Publicar um item personalizado do marketplace no Azure Stack (operador de nuvem) | Microsoft Docs
description: Como um operador do Azure Stack, saiba como publicar um item personalizado do marketplace no Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 6c1750e6a523828400b3d06d4e1c22fb34f8273f
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247860"
---
# <a name="azure-stack-marketplace-overview"></a>Visão geral do Azure Stack Marketplace

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Marketplace do Azure Stack é uma coleção de serviços, aplicativos e recursos personalizados para o Azure Stack. Os recursos incluem redes, máquinas virtuais, armazenamento e muito mais. Use o Marketplace para criar novos recursos e implantar novos aplicativos; ou procure e escolha os itens que você deseja usar. Para usar um item do Marketplace, os usuários devem assinar uma oferta que lhes concede acesso ao item.

Como um operador do Azure Stack, você decide quais itens adicionará (publicar) no Marketplace. Você pode publicar itens como bancos de dados, serviços de aplicativos e assim por diante. Publicação torna visível para todos os seus usuários. Você pode publicar itens personalizados criados por você, ou você pode publicar itens de uma crescente [lista de itens do Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando você publica um item no Marketplace, os usuários podem vê-lo dentro de cinco minutos.

> [!CAUTION]  
> Todos os artefatos de item de galeria, incluindo imagens e arquivos JSON, são acessíveis sem autenticação depois tornando-os disponíveis no Azure Stack Marketplace. Para obter mais considerações ao publicar itens do marketplace personalizado, consulte [criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Para abrir o Marketplace no portal, selecione administrador **+ criar um recurso**.

![Marketplace](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Itens do Marketplace

Um item do Marketplace do Azure Stack é um serviço, aplicativo ou recurso que os usuários podem baixar e usar. Todos os itens do Marketplace do Azure Stack são visíveis para todos os seus usuários, incluindo itens administrativos como planos e ofertas. Esses itens não exigem uma assinatura para o modo de exibição, mas são não funcional para os usuários.

Cada item do Marketplace contém:

* Um modelo do Resource Manager para o provisionamento de recursos.
* Metadados, como cadeias de caracteres, ícones e outros materiais de marketing.
* Informações de formatação para exibir o item no portal.

Todos os itens publicados no Marketplace usa o formato de pacote de galeria do Azure (. azpkg). Adicione recursos de implantação ou tempo de execução (código, arquivos zip com software ou imagens de máquina virtual) para o Azure Stack separadamente, não como parte do item do Marketplace.

Com a versão 1803 e posterior, o Azure Stack converte imagens em arquivos esparsos, durante o download do Azure ou ao carregar imagens personalizadas. Esse processo adiciona tempo ao adicionar uma imagem, mas economiza espaço e acelera a implantação dessas imagens. Conversão só se aplica a novas imagens. Imagens existentes não são alteradas.

## <a name="next-steps"></a>Próximas etapas

* [Baixar itens do Marketplace](azure-stack-download-azure-marketplace-item.md)  
* [Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)
