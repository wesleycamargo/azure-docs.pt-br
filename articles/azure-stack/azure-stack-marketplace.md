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
ms.date: 09/12/2018
ms.author: sethm
ms.reviewer: jeffgo
ms.openlocfilehash: 12310c088777d65bef211747806f942433857e40
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632341"
---
# <a name="the-azure-stack-marketplace-overview"></a>Visão geral do Azure Stack Marketplace

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

O Marketplace é uma coleção de serviços, aplicativos e recursos personalizados para o Azure Stack. Os recursos incluem máquinas virtuais, redes, armazenamento e assim por diante. Os usuários vem aqui para criar novos recursos e implantar novos aplicativos. Pense nele como um catálogo de compra em que os usuários podem procurar e escolha os itens que deseja usar. Para usar um item do Marketplace, os usuários devem assinar uma oferta que lhes concede acesso ao item.

Como um operador do Azure Stack, você decide quais itens adicionará (publicar) no Marketplace. Você pode publicar coisas como bancos de dados, serviços de aplicativos e assim por diante. Publicação torna visível para todos os seus usuários. Você pode publicar os itens personalizados criados por você. Você também pode publicar itens de uma crescente [lista de itens do Azure Marketplace](azure-stack-marketplace-azure-items.md). Quando você publica um item no Marketplace, os usuários podem vê-lo dentro de cinco minutos.

> [!Caution]  
> Todos os artefatos de item de galeria, conhecidos como imagens e arquivos json são acessíveis sem autenticação depois tornando-os disponíveis no marketplace do Azure Stack. Para obter mais considerações ao publicar itens do marketplace personalizado, consulte [criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md).

Para abrir o Marketplace, no console do administrador, selecione **+ criar um recurso**.

![](media/azure-stack-publish-custom-marketplace-item/image1.png)

## <a name="marketplace-items"></a>Itens do Marketplace
Um item do Marketplace do Azure Stack é um serviço, aplicativo ou recurso que os usuários podem baixar e usar. Todos os itens do Marketplace do Azure Stack são visíveis para todos os seus usuários, incluindo itens administrativos, como planos e ofertas. Esses itens não exigem uma assinatura para o modo de exibição, mas são não funcional para os usuários.

Cada item do Marketplace contém:

* Um modelo do Gerenciador de Recursos do Azure para o provisionamento de recursos
* Metadados, como cadeias de caracteres, ícones e outros manuais e acessórios de marketing
* Informações de formatação para exibir o item no portal

Todos os itens publicados no Marketplace usa o formato de pacote de galeria do Azure (. azpkg). Adicione recursos de implantação ou tempo de execução (como código, arquivos zip com software ou imagens de máquina virtual) para o Azure Stack separadamente, não como parte do item do Marketplace. 

Com a versão 1803 e posterior, o Azure Stack converte imagens em arquivos esparsos, durante o download do Azure ou ao carregar imagens personalizadas. Esse processo adiciona tempo ao adicionar uma imagem, mas economiza espaço e acelera a implantação dessas imagens. Conversão só se aplica a novas imagens.  Imagens existentes não são alteradas. 

## <a name="next-steps"></a>Próximas etapas
[Baixar itens do Marketplace](azure-stack-download-azure-marketplace-item.md)  
[Criar e publicar um item do Marketplace](azure-stack-create-and-publish-marketplace-item.md)

