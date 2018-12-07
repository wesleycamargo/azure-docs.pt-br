---
title: Assinaturas no Gerenciamento de API do Azure|Microsoft Docs
description: Aprenda sobre o conceito de Assinaturas no Gerenciamento de API do Azure.
services: api-management
documentationcenter: ''
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/14/2018
ms.author: apimpm
ms.openlocfilehash: 45a65c7a94f3e6917b2882f27c9ad7d764ef97d7
ms.sourcegitcommit: eba6841a8b8c3cb78c94afe703d4f83bf0dcab13
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2018
ms.locfileid: "52621677"
---
# <a name="subscriptions-in-azure-api-management"></a>Assinaturas no Gerenciamento de API do Azure

Assinaturas é um conceito importante no Gerenciamento de API do Azure (APIM). É a maneira mais comum de os consumidores de API obterem acesso a APIs publicadas por meio de uma instância APIM. Este artigo fornece uma visão geral do conceito.

## <a name="what-is-subscriptions"></a>O que são Assinaturas

Ao publicar APIs por meio do APIM, a maneira mais fácil e mais comum de proteger o acesso a essas APIs é usando as Chaves de Assinatura. Em outras palavras, os desenvolvedores que precisam consumir as APIs publicadas devem incluir uma Chave de assinatura válida em solicitações HTTP ao fazer chamadas para essas APIs. Caso contrário, as chamadas serão rejeitadas imediatamente pelo gateway APIM e não serão encaminhadas aos serviços de back-end.

Para obter uma Chave de Assinatura para acessar APIs, é necessária uma Assinatura. A assinatura é essencialmente um contêiner nomeado para um par de chaves de assinatura. As assinaturas podem ser obtidas por desenvolvedores que precisam consumir as APIs publicadas, com ou sem a aprovação dos editores da API. Os editores de API também podem criar assinaturas diretamente, em nome dos consumidores da API.

> [!TIP]
> O APIM também suporta outros mecanismos para proteger o acesso a APIs, incluindo [OAuth2.0](api-management-howto-protect-backend-with-aad.md), [certificados de cliente](api-management-howto-mutual-certificates-for-clients.md) e [lista de permissões de IP](https://docs.microsoft.com/azure/api-management/api-management-access-restriction-policies#RestrictCallerIPs)

## <a name="scope-of-subscriptions"></a>Escopo de assinaturas

As assinaturas podem ser associadas a vários escopos: produto, todas as APIs ou uma API individual.

### <a name="subscriptions-for-a-product"></a>Assinaturas para um produto

Tradicionalmente, as Assinaturas no APIM sempre foram associadas a um único escopo do [produto da API](api-management-terminology.md). Os desenvolvedores localizariam a lista de produtos no Portal do desenvolvedor e enviam Solicitações de assinatura para os produtos que gostariam de usar. Quando uma Solicitação de Assinatura for aprovada (automaticamente ou por editores de API), o desenvolvedor poderá usar as chaves para acessar todas as APIs no produto.

![Assinaturas de produto](./media/api-management-subscriptions/product-subscription.png)

> [!TIP]
> Em determinados cenários, os editores de API podem querer publicar um produto de API para o público sem o requisito de Assinaturas. Eles podem desmarcar a opção **Exigir assinatura** na página **Configurações** do produto no portal do Azure. Como resultado, todas as APIs do produto podem ser acessadas sem uma chave de API.

### <a name="subscriptions-for-all-apis-or-an-individual-api"></a>Assinaturas para todas as APIs ou uma API individual

> [!NOTE]
> Atualmente, esse recurso está disponível apenas na camada de Consumo de Gerenciamento de API.

Quando introduzimos o nível de [Consumo](https://aka.ms/apimconsumptionblog) do APIM, fizemos algumas alterações para otimizar o gerenciamento de chaves. Primeiro, adicionamos mais dois escopos de assinatura - todas as APIs e uma única API. O escopo das Assinaturas não está mais limitado a um produto da API. Agora é possível criar chaves que concedam acesso a uma API (ou a todas as APIs em uma instância APIM), sem precisar criar um produto e adicionar as APIs a ele primeiro. Além disso, cada instância APIM agora vem com uma Assinatura de todas as APIs imutável, o que torna mais fácil e mais simples testar e depurar APIs no console de teste.

Em segundo lugar, o APIM agora permite assinaturas "independentes". Não é mais necessário que as assinaturas sejam associadas a uma conta de desenvolvedor. Isso é útil em cenários como quando uma assinatura é compartilhada por vários desenvolvedores ou equipes.

Por fim, os editores de API agora podem [criar Assinaturas](api-management-howto-create-subscriptions.md) diretamente no Portal do Azure:

![Assinaturas flexíveis](./media/api-management-subscriptions/flexible-subscription.png)

## <a name="next-steps"></a>Próximas etapas
Para obter mais informações, consulte Gerenciamento de API:

+ Conheça outros [conceitos](api-management-terminology.md) no Gerenciamento de API
+ Siga nossos [tutoriais](import-and-publish.md) para saber mais sobre o Gerenciamento de API
+ Verifique nossa [página de perguntas Frequentes](api-management-faq.md) para perguntas comuns