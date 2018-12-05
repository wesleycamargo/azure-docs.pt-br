---
title: Guia de Publicação de Oferta de Modelo de Solução dos Aplicativos do Azure
description: Este artigo descreve os requisitos para publicar um modelo de solução no Azure Marketplace.
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/15/2018
ms.author: ellacroi
ms.openlocfilehash: d955f5c11121e8d42bc4b02b75427ab07298e74b
ms.sourcegitcommit: fa758779501c8a11d98f8cacb15a3cc76e9d38ae
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/20/2018
ms.locfileid: "52264175"
---
# <a name="azure-applications-solution-template-offer-publishing-guide"></a>Aplicativos do Azure: Guia de Publicação de Oferta de Modelo de Solução

Os modelos de solução são uma das principais formas de publicar uma solução no Marketplace. Use este guia para compreender os requisitos dessa oferta. 

Use o Azure App: oferta de modelo de solução quando a solução exigir mais automação de implantação e configuração do que um VM simples. Você pode automatizar o provisionamento de uma ou mais VMs usando Aplicativos do Azure: modelos de solução. Você também pode provisionar recursos de rede e armazenamento. O tipo de oferta Aplicativos do Azure: modelos de solução oferece benefícios de automação para VMs únicas e soluções inteiras baseadas em IaaS.

Esses modelos de solução são ofertas de transação, que são implantados e faturados através do Marketplace. A chamada à ação que um usuário vê é "Obtenha agora".


## <a name="requirements-for-solution-templates"></a>Requisitos dos modelos de solução

| **Requisitos** | **Detalhes**  |
| ---------------  | -----------  |
|Cobrança e medição    |  Os recursos serão provisionados na assinatura do Azure do cliente. Máquinas virtuais pagas conforme o uso (PAYGO) serão negociadas com o cliente via Microsoft e cobradas por meio da assinatura do Azure do cliente (PAYGO).  <br/> No caso do modelo traga sua própria licença (BYOL), enquanto a Microsoft cobrará os custos de infraestrutura incorridos na assinatura do cliente, você negociará os valores referentes ao licenciamento de software diretamente com o cliente.   |
|VHD (disco rígido virtual) compatível com Azure  |   As VMs devem ser criadas em Windows ou Linux.  Para obter mais informações, [confira Criar um VHD compatível com o Azure](./cloud-partner-portal/virtual-machine/cpp-create-vhd.md). |
| Atribuição de uso do cliente | Habilitar a atribuição de uso do cliente é necessário em todos os modelos de solução publicados no Azure Marketplace. Para obter mais informações sobre atribuição de uso do cliente e como habilitá-lo, consulte [atribuição de uso do cliente de parceiro do Azure](./azure-partner-customer-usage-attribution.md).  |
|  |  |


## <a name="next-steps"></a>Próximas etapas
Se você ainda não fez isso, [registre](https://azuremarketplace.microsoft.com/sell) no Marketplace.

Se você estiver registrado e estiver criando uma nova oferta ou trabalhando em um existente, entre no [Portal do Cloud Partner](https://cloudpartner.azure.com) para criar ou concluir sua oferta.
