---
title: Excluir uma oferta do Azure Marketplace | Microsoft Docs
description: Este artigo fornece detalhes sobre como excluir uma oferta do Azure Marketplace
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: 233d62b176303ca384aae9453c2e125c0c70fad2
ms.openlocfilehash: f09be39b75bd0f39094a2ec08609c0c7ff03bc8e
ms.lasthandoff: 04/12/2017


---
# <a name="delete-an-offersku-from-azure-marketplace"></a>Excluir uma oferta/SKU do Azure Marketplace

Por vários motivos, você pode decidir remover sua oferta do Marketplace.  A Remoção de Oferta garante que novos clientes não possam adquirir ou implantar sua oferta, mas não tem impacto sobre clientes existentes. O Encerramento de Oferta é o processo de encerrar o contrato de licenciamento e/ou serviço entre você e seus clientes existentes.  Diretrizes e políticas relacionadas à remoção e ao encerramento de ofertas são governadas pelo [Contrato do Distribuidor do Microsoft Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560) (consulte a Seção 7) e as [Políticas de Participação](https://azure.microsoft.com/support/legal/marketplace/participation-policies/) (consulte a Seção 6.2).   Este artigo aborda os diferentes cenários de exclusão com suporte e as etapas que você pode seguir para executá-los.

## <a name="delete-a-live-sku-from-azure-marketplace"></a>Excluir uma SKU ativa do Azure Marketplace

É possível excluir uma SKU ativa do Azure Marketplace seguindo as etapas abaixo:

1.  Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).

2.  Selecione sua oferta na guia **Todas as ofertas**.

3.  No painel do lado esquerdo da tela, selecione a guia **SKUs**.

4.  Selecione a SKU que você deseja excluir e clique no botão Excluir para essa SKU.

5.  [Republique](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) a oferta do Azure Marketplace.

Depois que a oferta estiver ativa no Azure Marketplace, a SKU será excluída do Azure Marketplace e do Portal do Azure.

## <a name="rollback-to-a-previous-sku-version"></a>Reversão para uma versão anterior da SKU

Você pode excluir a versão atual de uma SKU ativa do Azure Marketplace seguindo as etapas abaixo. Quando o processo for concluído, a SKU será revertida para a versão anterior.

1.  Entre no [Portal de Parceiros de Nuvem](https://cloudpartner.azure.com/).

2.  Selecione sua oferta na guia **Todas as ofertas**.

3.  No painel do lado esquerdo da tela, selecione a guia **SKUs**.

4.  Exclua a **Versão de Disco** mais recente na da lista de versões de disco publicadas.

5.  [Republique](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md) a oferta do Azure Marketplace.

Depois que a oferta estiver ativa no Azure Marketplace, a versão atual da SKU listada será excluída do Azure Marketplace e do Portal do Azure. O SKU será revertido para a versão anterior.

## <a name="delete-a-live-offer"></a>Excluir uma oferta ativa

Há vários aspectos que precisam ser atendidos no caso de uma solicitação para remover uma oferta online. Siga as etapas abaixo para obter diretrizes da equipe de suporte para remover uma oferta ativa do Azure Marketplace:

1.  Gere um tíquete de suporte usando este [link](https://go.microsoft.com/fwlink/?linkid=844975)

2.  Selecione **Gerenciando ofertas** na lista **Tipo de problema** e **Modificando uma oferta e/ou SKU em produção** na lista **Categoria**.

3.  Enviar a solicitação

A equipe de suporte orientará você pelo processo de exclusão da oferta.

Observe que a exclusão de uma oferta/SKU não afetará as compras da SKU/oferta já realizadas. Elas continuarão funcionando como antes. Elas não estarão disponíveis para novas compras no futuro.  
