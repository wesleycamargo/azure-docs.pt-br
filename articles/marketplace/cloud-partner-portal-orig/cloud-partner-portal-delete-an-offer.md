---
title: Excluir uma oferta ou SKU do Azure Marketplace | Microsoft Docs
description: Etapas para excluir uma oferta ou SKU.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: cc172e35e8964fad3b1a1410d1f1f3240c423ab3
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805057"
---
<a name="delete-an-offer-or-sku-from-azure-marketplace"></a>Excluir uma oferta ou SKU do Azure Marketplace
==========================================

Por vários motivos, você pode decidir remover sua oferta do Marketplace. A Remoção de Oferta garante que novos clientes não possam adquirir ou implantar sua oferta, mas não tem impacto sobre clientes existentes.
O Encerramento de Oferta é o processo de encerrar o contrato de licenciamento e/ou serviço entre você e seus clientes existentes. As diretrizes e políticas relacionadas à remoção e ao término da oferta são regidas pelo [Contrato do Editor do Microsoft Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560) (consulte a Seção
7) e as [Políticas de Participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (consulte a Seção 6.2). Este artigo aborda os diferentes cenários de exclusão com suporte e as etapas que você pode seguir para executá-los.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Excluir uma SKU ativa do Azure Marketplace
----------------------------------------

É possível excluir uma SKU ativa do Azure Marketplace usando as seguintes etapas:

1.  Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).

2.  Selecione sua oferta na guia **Todas as ofertas**.

3.  No painel do lado esquerdo da tela, selecione a guia **SKUs**.

4.  Selecione a SKU que você deseja excluir e clique no botão Excluir para essa SKU.

5.  [Republique](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) a oferta do Azure Marketplace.

Depois que a oferta estiver ativa no Azure Marketplace, a SKU será excluída do Azure Marketplace e do Portal do Azure.

<a name="roll-back-to-a-previous-sku-version"></a>Reverter para uma versão anterior da SKU
----------------------------------

Você pode excluir a versão atual de uma SKU ativa do Azure Marketplace seguindo as etapas abaixo. Quando o processo for concluído, a SKU será revertida para a versão anterior.

1.  Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).

2.  Selecione sua oferta na guia **Todas as ofertas**.

3.  No painel do lado esquerdo da tela, selecione a guia **SKUs**.

4.  Exclua a **Versão de Disco** mais recente na da lista de versões de disco publicadas.

5.  [Republique](./cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) a oferta do Azure Marketplace.

Depois que a oferta estiver ativa no Azure Marketplace, a versão atual da SKU listada será excluída do Azure Marketplace e do Portal do Azure.
O SKU será revertido para a versão anterior.

<a name="delete-a-live-offer"></a>Excluir uma oferta ativa
-------------------

Há vários aspectos que precisam ser atendidos no caso de uma solicitação para remover uma oferta online. Siga as etapas abaixo para obter diretrizes da equipe de suporte para remover uma oferta ativa do Azure Marketplace:

1.  Gere um tíquete de suporte usando este [link](https://go.microsoft.com/fwlink/?linkid=844975) ou clicando em Suporte no canto superior direito do Portal do Cloud Partner.

2.  Selecione o tipo de oferta específico na lista **Tipo de problema** e **Remover uma oferta publicada** na lista **Categoria**.

3.  Envie a solicitação.

A equipe de suporte orientará você pelo processo de exclusão da oferta.

>[!NOTE]
>A exclusão de uma SKU/oferta não afetará as compras atuais da SKU/oferta. Essas SKUs/ofertas continuarão funcionando como antes. No entanto, não estarão disponíveis para novas compras futuras.
