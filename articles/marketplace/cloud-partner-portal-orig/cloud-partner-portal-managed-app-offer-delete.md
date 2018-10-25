---
title: Excluir uma oferta/SKU do Azure Marketplace
description: Excluir uma oferta/SKU do Azure Marketplace
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: qianw211
manager: pbutlerm
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 02b4ad8aea3904ba0e99a5ea4d4d035f970e171a
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48805300"
---
<a name="delete-an-offersku-from-azure-marketplace"></a>Excluir uma oferta/SKU do Azure Marketplace 
==========================================

Por vários motivos, você pode decidir remover sua oferta do Marketplace. Novos clientes não podem mais comprar ou implantar a oferta, mas os clientes antigos não serão afetados.
O Encerramento de Oferta é o processo de encerrar o contrato de licenciamento e/ou serviço entre você e seus clientes existentes. As diretrizes e políticas relacionadas à remoção e ao término da oferta são regidas pelo [Contrato do Editor do Microsoft Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560) (consulte a Seção
7) e as [Políticas de Participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (consulte a Seção 6.2). Os diferentes cenários de exclusão com suporte e as etapas que você pode seguir serão abordados.

<a name="delete-a-live-sku-from-azure-marketplace"></a>Excluir uma SKU ativa do Azure Marketplace 
----------------------------------------

É possível excluir uma SKU ativa do Azure Marketplace seguindo as etapas abaixo:

1.  Entre no [Portal de Parceiros de Nuvem](http://cloudpartner.azure.com).
2.  Selecione a oferta na guia Todas as ofertas.
3.  No painel esquerdo da tela, selecione a guia SKUs.
4.  Selecione a SKU que você quer excluir e clique no botão Excluir para essa SKU.
5.  Republique a oferta no Azure Marketplace.

Depois que a oferta estiver ativa no Azure Marketplace, a SKU será excluída do Azure Marketplace e do Portal do Azure.

<a name="roll-back-to-a-previous-sku-version"></a>Reverter para uma versão anterior da SKU 
----------------------------------

Você pode excluir a versão atual de uma SKU ativa do Azure Marketplace seguindo as etapas abaixo. Quando o processo for concluído, a SKU será revertida para a versão anterior.

1.  Entre no [Portal de Parceiros de Nuvem](http://cloudpartner.azure.com).
2.  Selecione a oferta na guia Todas as ofertas.
3.  No painel esquerdo da tela, selecione a guia SKUs.
4.  Exclua a última Versão do Pacote da lista de pacotes publicados.
5.  Republique a oferta no Azure Marketplace.

Depois que a oferta estiver ativa no Azure Marketplace, a versão atual da SKU listada será excluída do Azure Marketplace e do Portal do Azure.
O SKU será revertido para a versão anterior.

<a name="delete-a-live-offer"></a>Excluir uma oferta ativa 
-------------------

Siga as etapas abaixo para obter diretrizes da equipe de suporte para remover uma oferta ativa do Azure Marketplace:

1.  Gere um tíquete de suporte usando este [link](https://go.microsoft.com/fwlink/?linkid=844975)
2.  Selecione Gerenciar ofertas na lista Tipo de Problema, Modificar uma oferta e/ou SKU já em produção na lista Categoria.
3.  Envie a solicitação.

A equipe de suporte orientará você pelo processo de exclusão da oferta.

A exclusão de uma SKU/oferta não afetará as compras atuais da SKU/oferta já realizada. Ou seja, continuarão funcionando como antes, mas não estarão disponíveis para novas compras no futuro.
