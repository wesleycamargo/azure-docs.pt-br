---
title: Oferta de serviços no Azure Stack | Microsoft Docs
description: Como um operador de nuvem, você pode oferecer serviços a seus usuários.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/17/2018
ms.author: jeffgilb
ms.reviewer: unknown
ms.openlocfilehash: 754a0002aca52462910abe50825cff0254a7068f
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/12/2019
ms.locfileid: "54244147"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Visão geral da oferta de serviços no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[O Microsoft Azure Stack](azure-stack-poc.md) é uma plataforma de nuvem híbrida que possibilita entregar serviços do seu datacenter. Como um provedor de serviços, você pode oferecer serviços a seus locatários. Em uma empresa ou uma agência do governo, você pode oferecer serviços locais para seus funcionários. 

Você pode oferecer [infraestrutura como serviço](https://azure.microsoft.com/overview/what-is-iaas/) serviços (IaaS) que permitem aos usuários criar uma infraestrutura de computação sob demanda, provisionados e gerenciados a partir do portal do usuário do Azure Stack.

Você também pode implantar [plataforma como serviço](https://azure.microsoft.com/overview/what-is-paas/) serviços (PaaS) para o Azure Stack da Microsoft e outros provedores de terceiros 3ª. Os serviços que você pode fornecer incluem, mas não se limitam a:

- [Adicionar um provedor de recursos do serviço de aplicativo ao Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-app-service-overview)

- [Adicionar um provedor de recursos do SQL Server para o Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-sql-resource-provider-deploy)

- [Adicionar um provedor de recursos do servidor MySQL ao Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-mysql-resource-provider-deploy)


Você pode até combinar serviços para integrar e desenvolver soluções complexas para usuários diferentes.

Para fornecer esses serviços para seus usuários, você deve criar [planos, ofertas e cotas](azure-stack-plan-offer-quota-overview.md). Os usuários, em seguida, podem assinar suas ofertas para usar os serviços.

## <a name="plan-your-service-offers"></a>Planejar suas ofertas de serviço

Quando você estiver planejando suas ofertas, tenha os seguintes pontos em mente:

**Ofertas de avaliação**: Você pode usar as ofertas de avaliação para atrair novos usuários, que podem, em seguida, atualizar para serviços adicionais. Para criar uma oferta de avaliação, crie um pequeno [plano base](azure-stack-plan-offer-quota-overview.md#base-plan) com um plano de complemento maior opcional.

**Planejamento de capacidade**: Você pode estar preocupado com usuários em coletar grandes quantidades de recursos e a sobrecarga do sistema para todos os usuários. Para ajudar o desempenho, você pode [configurar seus planos com cotas](azure-stack-plan-offer-quota-overview.md#plans) ao limite de uso.

**Provedores de delegado**: Você pode conceder a outras pessoas que oferece a capacidade de criar em seu ambiente. Por exemplo, se você for um provedor de serviços, você pode [delegar](azure-stack-delegated-provider.md) essa capacidade de seus revendedores. Ou, se você for uma organização, você pode delegar a outras divisões/subsidiárias.

## <a name="next-steps"></a>Próximas etapas

[Criar uma oferta no Azure Stack](azure-stack-create-offer.md)
