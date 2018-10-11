---
title: Visão geral do Azure de plano, oferta, cotas e assinatura pilha | Microsoft Docs
description: Como um operador de nuvem, eu quero entender os planos, ofertas, cotas e as assinaturas do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/07/2018
ms.author: sethm
ms.reviewer: ''
ms.openlocfilehash: 70ed5d45701133434c708ad80aaafc58645297e8
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077106"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Visão geral de plano, oferta, cotas e assinatura

*Aplica-se a: integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

[O Azure Stack](azure-stack-poc.md) permite que você fornecer uma ampla variedade de serviços, como máquinas virtuais, bancos de dados SQL, SharePoint, Exchange e até mesmo [itens do Azure Marketplace](azure-stack-marketplace-azure-items.md). Como um operador do Azure Stack, configure e fornecem serviços no Azure Stack por meio de planos, ofertas e cotas.

Ofertas contêm um ou mais planos, e cada plano inclui um ou mais serviços. Ao criar planos e combiná-los em ofertas diferentes, você pode gerenciar:

- Quais serviços e recursos que os usuários podem acessar.
- A quantidade de recursos que os usuários podem consumir.
- Quais regiões têm acesso aos recursos.

Quando você fornece um serviço, siga estas etapas de alto nível:

1. Adicione um serviço que você deseja fornecer aos seus usuários.
2. Crie um plano que tem um ou mais serviços. Ao criar um plano, selecione ou crie as cotas que definem os limites de recursos de cada serviço no plano.
3. Crie uma oferta que contém um ou mais planos. A oferta pode incluir planos de base e planos de complemento opcional.

Depois de criar a oferta, os usuários podem assiná-la para acessar os serviços e recursos que fornece a oferta. Os usuários podem assinar ofertas quantos desejar. O diagrama a seguir mostra um exemplo simples de um usuário que tenha se inscrito para duas ofertas. Cada oferta tem um plano ou dois, e cada plano oferece acesso a serviços.

![Assinaturas de locatários com ofertas e planos](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Planos

Os planos são agrupamentos de um ou mais serviços. Como um operador do Azure Stack, você [criar planos de](azure-stack-create-plan.md) para oferecer a seus usuários. Por sua vez, os seus usuários assinam suas ofertas para usar os planos e serviços incluídos nelas. Durante a criação de planos, certifique-se de definir suas cotas, definir seus planos de base e considere a inclusão de planos de complemento opcional.

### <a name="quotas"></a>Cotas

Para ajudar você a gerenciar a capacidade de nuvem, você pode usar cotas pré-configurados ou crie uma nova cota para cada serviço em um plano. As cotas de definem os limites de recurso superior que uma assinatura de usuário pode provisionar ou consumir. Por exemplo, uma cota pode permitir que um usuário crie até cinco VMs (máquinas virtuais). Você pode definir cotas adicionais nas máquinas virtuais, como núcleos de CPU e RAM.

É possível configurar cotas por região. Por exemplo, um plano que fornece serviços de computação para a região A poderia ter uma cota de duas VMs com 4 GB de RAM e 8 núcleos de CPU.

>[!NOTE]
>No Kit de desenvolvimento do Azure Stack, apenas uma região (denominada *local*) está disponível.

Saiba mais sobre [tipos de cotas no Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Plano de base

Ao criar uma oferta, o administrador de serviços pode incluir um plano de base. Esses planos base são incluídos por padrão, quando um usuário assina essa oferta. Quando um usuário assina, eles têm acesso a todos os provedores de recursos especificados nesses planos base (com as cotas correspondentes.)

### <a name="add-on-plans"></a>Planos de complemento

Planos de complemento são planos opcionais que você adicionar uma oferta. Planos de complemento não são incluídos por padrão na assinatura. Planos de complemento são planos adicionais (com cotas) disponíveis em uma oferta que um assinante pode adicionar a suas assinaturas. Por exemplo, você pode oferecer um plano de base com recursos limitados para uma versão de avaliação e um plano de complemento com recursos mais substanciais para os clientes que decida adotar o serviço.

## <a name="offers"></a>Ofertas

As ofertas são grupos de um ou mais planos criados por você, de modo que os usuários podem se inscrever neles. Por exemplo, a oferta Alpha pode conter um plano, que fornece um conjunto de serviços de computação e o plano B, que fornece um conjunto de serviços de armazenamento e rede.

Quando você [criar uma oferta](azure-stack-create-offer.md), você deve incluir pelo menos um plano de base, mas você também pode criar planos de complemento que os usuários podem adicionar à sua assinatura.

## <a name="subscriptions"></a>Assinaturas

Uma assinatura é como os usuários acessam suas ofertas. Se você for um operador do Azure Stack para um provedor de serviços, seus usuários (locatários) compram os serviços ao assinar suas ofertas. Se você for um operador do Azure Stack em uma organização, os usuários (funcionários) podem assinar os serviços que oferecem sem pagar.

Cada combinação de um usuário com uma oferta é uma assinatura exclusiva. Um usuário pode ter assinaturas de várias ofertas, mas cada assinatura só se aplica a uma oferta. Planos, ofertas e cotas se aplicam somente a uma assinatura exclusiva – eles não podem ser compartilhados entre assinaturas. Cada recurso que cria um usuário está associado uma assinatura.

### <a name="default-provider-subscription"></a>Assinatura do provedor padrão

A assinatura de provedor padrão é criada automaticamente quando você implanta o Kit de desenvolvimento do Azure Stack. Essa assinatura pode ser usada para gerenciar o Azure Stack, implante provedores de recursos adicionais e criar planos e ofertas para os usuários. Por motivos de licenciamento e de segurança, ele não deve ser usado para executar aplicativos e cargas de trabalho do cliente.

## <a name="next-steps"></a>Próximas etapas

[Criar um plano](azure-stack-create-plan.md)
