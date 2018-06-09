---
title: Visão geral do Azure de plano, oferta, cotas e assinatura pilha | Microsoft Docs
description: Como um operador de nuvem, quero entender os planos de pilha do Azure, ofertas, cotas e assinaturas.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: d8aef778807d3a8a61cf9eedaae24abce84a19ab
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 06/08/2018
ms.locfileid: "35248751"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Visão geral de plano, oferta, cotas e assinatura

*Aplica-se a: Azure pilha integrado sistemas e o Kit de desenvolvimento de pilha do Azure*

[A pilha do Azure](azure-stack-poc.md) permite que você fornecer uma ampla variedade de serviços, como máquinas virtuais, bancos de dados, SharePoint, Exchange e até mesmo [itens do Marketplace do Azure](azure-stack-marketplace-azure-items.md). Como um operador de pilha do Azure, configure e fornecem serviços na pilha do Azure usando planos, ofertas e cotas.

As ofertas que contêm um ou mais planos, e cada plano inclui um ou mais serviços. Criando planos e combiná-las em diferentes ofertas, você pode gerenciar:

- Quais serviços e recursos que os usuários podem acessar.
- A quantidade de recursos que os usuários podem consumir.
- Quais regiões têm acesso aos recursos.

Ao fornecer um serviço, siga estas etapas de alto nível:

1. Adicione um serviço que você deseja fornecer aos usuários.
2. Crie um plano que tem um ou mais serviços. Ao criar um plano, selecione ou crie cotas que definem os limites de recursos de cada serviço no plano.
3. Crie uma oferta que contém um ou mais planos. A oferta pode incluir planos básico e planos de complemento opcional.

Depois de criar a oferta, os usuários possam assiná-lo para acessar os serviços e recursos que fornece a oferta. Os usuários podem assinar a oferta de quantos desejar. O diagrama a seguir mostra um exemplo simples de um usuário que tenha se inscrito para duas ofertas. Cada oferta tem um plano ou dois, e cada plano oferece acesso a serviços.

![Assinatura de locatário com ofertas e planos](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Planos

Os planos são agrupamentos de um ou mais serviços. Como um operador de pilha do Azure, você [criar planos de](azure-stack-create-plan.md) para oferecer a seus usuários. Por sua vez, os usuários assinar suas ofertas para usar os planos e serviços que elas incluem. Ao criar planos, certifique-se de definir cotas de, definir planos de base e considere a inclusão de planos de complemento opcional.

### <a name="quotas"></a>Cotas

Para ajudá-lo a gerenciar a sua capacidade de nuvem, você pode usar cotas pré-configuradas ou criar uma nova cota para cada serviço em um plano. As cotas de definem os limites de recurso superior que uma assinatura de usuário pode provisionar ou consumir. Por exemplo, uma cota pode permitir que um usuário crie até cinco (máquinas virtuais). Você definir cotas adicionais nas máquinas virtuais, como RAM e CPU núcleos.

Você pode configurar as cotas por região. Por exemplo, um plano que fornece serviços de computação para região A poderia ter uma cota de duas VMs com 4 GB de RAM e 8 núcleos de CPU.

>[!NOTE]
>No Kit de desenvolvimento de pilha do Azure, apenas uma região (denominado *local*) está disponível.

Saiba mais sobre [tipos de cota na pilha do Azure](azure-stack-quota-types.md).

### <a name="base-plan"></a>Plano de base

Ao criar uma oferta, o administrador de serviço pode incluir um plano de base. Esses planos de base são incluídos por padrão quando um usuário se inscreve para essa oferta. Quando um usuário assina, eles têm acesso a todos os provedores de recursos especificado nesses planos de base (com as cotas correspondentes.)

### <a name="add-on-plans"></a>Planos de complemento

Planos de complemento são planos opcionais que você adicionar a uma oferta. Planos de complemento não são incluídos por padrão na assinatura. Planos de complemento são planos adicionais (com as cotas) disponíveis em uma oferta que pode adicionar um assinante para suas assinaturas. Por exemplo, você pode oferecer um plano de base com recursos limitados para uma avaliação e um plano de complemento com recursos mais significativas para os clientes que optar por adotar o serviço.

## <a name="offers"></a>Ofertas

Ofertas são grupos de um ou mais planos que você criar para que os usuários podem assiná-los. Por exemplo, oferecer Alpha pode conter um plano, que fornece um conjunto de serviços de computação e o plano B, que fornece um conjunto de serviços de armazenamento e rede.

Quando você [criar uma oferta](azure-stack-create-offer.md), você deve incluir pelo menos um plano de base, mas você também pode criar planos de complemento que os usuários podem adicionar à sua assinatura.

## <a name="subscriptions"></a>Assinaturas

Uma assinatura é como os usuários acessam suas ofertas. Se você for um operador de pilha do Azure para um provedor de serviço, os usuários (locatários) compram seus serviços assinando suas ofertas. Se você for um operador de pilha do Azure em uma organização, os usuários (funcionários) podem assinar os serviços que oferecem sem pagar.

Cada combinação de um usuário com uma oferta é uma assinatura exclusiva. Um usuário pode ter assinaturas várias ofertas, mas cada assinatura só se aplica a uma oferta. Planos, ofertas e cotas só se aplicam a uma assinatura exclusiva – eles não podem ser compartilhados entre assinaturas. Cada recurso que cria um usuário está associado uma assinatura.

### <a name="default-provider-subscription"></a>Assinatura do provedor de padrão

A assinatura de provedor padrão é criada automaticamente quando você implanta o Kit de desenvolvimento de pilha do Azure. Esta assinatura pode ser usada para gerenciar a pilha do Azure, implante provedores de recursos adicionais e criar planos e ofertas para os usuários. Por razões de licenciamento e de segurança, ele não deve ser usado para executar aplicativos e cargas de trabalho do cliente.

## <a name="next-steps"></a>Próximas etapas

[Criar um plano](azure-stack-create-plan.md)
