---
title: Publicar um item do marketplace personalizado na pilha do Azure (operador de nuvem) | Microsoft Docs
description: Como um operador de pilha do Azure, saber como publicar um item do marketplace personalizado na pilha do Azure.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 60871cbb-eed2-433c-a76d-d605c7aec06c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2018
ms.author: brenduns
ms.reviewer: jeffgo
ms.openlocfilehash: 4ea23ed01e6432f24024d7e8cc07c2dfe42ac639
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/01/2018
ms.locfileid: "34605562"
---
# <a name="the-azure-stack-marketplace-overview"></a>Visão geral do Azure Marketplace de pilha

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

O Marketplace é uma coleção de serviços, aplicativos e recursos personalizados para a pilha do Azure. Recursos incluem máquinas virtuais, redes, armazenamento e assim por diante. Os usuários se aqui para criar novos recursos e implantar novos aplicativos. Pense nisso como um catálogo de compra em que os usuários podem procurar e escolha os itens que desejam usar. Para usar um item do Marketplace, os usuários devem assinar uma oferta que lhe dá acesso ao item.

Como um operador de pilha do Azure, você decide quais itens adicionará (publicação) para o Marketplace. Você pode publicar como bancos de dados, serviços de aplicativos e assim por diante. Publicação torna visível para todos os seus usuários. Você pode publicar itens personalizados que você criar. Você também pode publicar itens de um crescente [lista de itens do Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando você publica um item para o Marketplace, os usuários podem vê-lo dentro de cinco minutos.

> [!Caution]  
> Todos os artefatos de item de galeria, conhecidos como imagens e arquivos json são acessíveis sem autenticação após tornando-os disponíveis no mercado de pilha do Azure. Para obter mais considerações ao publicar itens do marketplace personalizado, consulte [criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Para abrir o Marketplace, no console do administrador, selecione **novo**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Itens do Marketplace
Um item do Marketplace de pilha do Azure é um serviço, aplicativo ou recurso que os usuários podem baixar e usar. Todos os itens do Marketplace de pilha do Azure são visíveis para todos os seus usuários, incluindo itens administrativos como planos e ofertas. Esses itens não exigem uma assinatura para o modo de exibição, mas são não funcionais aos usuários.

Cada item do Marketplace contém:

* Um modelo do Gerenciador de Recursos do Azure para o provisionamento de recursos
* Metadados, como cadeias de caracteres, ícones e outros manuais e acessórios de marketing
* Informações de formatação para exibir o item no portal

Cada item publicado Marketplace usa o formato de pacote de galeria do Azure (.azpkg). Adicione recursos de implantação ou tempo de execução (como código, arquivos zip com o software ou imagens da máquina virtual) com a pilha do Azure separadamente, não como parte do item do Marketplace. 

Na versão 1803 e posterior, pilha de Azure converte imagens arquivos esparsos durante o download do Azure ou quando você carregar imagens personalizadas. Esse processo adiciona tempo ao adicionar uma imagem, mas economiza espaço e acelerar a implantação dessas imagens. Conversão só se aplica a novas imagens.  Imagens existentes não são alteradas. 

## <a name="next-steps"></a>Próximas etapas
[Fazer o download de itens do Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)

