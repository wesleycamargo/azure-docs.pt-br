---
title: Domínios de Eventos na Grade de Eventos do Azure
description: Descreve como os Domínios de Eventos são usados para gerenciar tópicos na Grade de Eventos do Azure.
services: event-grid
author: banisadr
ms.service: event-grid
ms.author: babanisa
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: fe66ca8b8f5b4474290e302f73b35868dce68caa
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2018
ms.locfileid: "50669323"
---
# <a name="understand-event-domains-for-managing-event-grid-topics"></a>Entender os Domínios de Eventos para gerenciar os tópicos da Grade de Eventos

Este artigo descreve como usar os Domínios de Eventos para gerenciar o fluxo de eventos personalizados em várias organizações empresariais, clientes ou aplicativos. Use os Domínios de Eventos para:

* Gerenciar arquiteturas de eventos multilocatário em escala.
* Gerenciar a autorização e a autenticação.
* Particionar tópicos sem gerenciar cada um individualmente.
* Evitar publicar individualmente em cada um dos pontos de extremidade do tópico.

[!INCLUDE [event-grid-preview-feature-note.md](../../includes/event-grid-preview-feature-note.md)]


## <a name="event-domains-overview"></a>Visão geral dos Domínios de Eventos

Um Domínio de Eventos é uma ferramenta de gerenciamento para grandes quantidades de Tópicos da Grade de Eventos relacionados com o mesmo aplicativo. Pode ser considerado um metatópico que pode conter milhares de tópicos individuais.

Os Domínios de Eventos criam a arquitetura que serviços do Azure como o Armazenamento e o Hub IoT usam para publicar os eventos disponíveis para você usar no seu próprio sistema. Com eles, é possível publicar eventos em milhares de tópicos. Os Domínios também permitem que você controle a autorização e a autenticação de cada tópico, para que seja possível particionar seus locatários.

### <a name="example-use-case"></a>Caso de uso de exemplo

É mais fácil explicar os Domínios de Eventos com um exemplo. Digamos que você execute o Maquinário de Construção Contoso na fabricação de tratores, equipamentos de escavação e outras máquinas pesadas. Como parte do gerenciamento empresarial, você envia por push informações em tempo real para os clientes sobre a manutenção dos equipamentos, a integridade do sistema, as atualizações de contrato etc. Todas essas informações são enviadas para vários pontos de extremidade, incluindo seu aplicativo, os pontos de extremidade do cliente e outras infraestruturas configuradas pelo cliente.

Os Domínios de Eventos permitem modelar o Maquinário de Construção Contoso como uma entidade de eventos exclusiva. Cada cliente é representado como um Tópico no Domínio. A autenticação e a autorização são manipuladas por meio do Azure Active Directory. Cada cliente pode assinar o Tópico e receber os eventos. O acesso gerenciado por meio dos Domínios de Eventos garante que eles só acessem o respectivo tópico.

Além disso, também fornece um ponto de extremidade exclusivo, em que todos os eventos de cliente podem ser publicados. A Grade de Eventos garantirá que cada Tópico esteja relacionado somente com os eventos do escopo do locatário.

![Exemplo de construção Contoso](./media/event-domains/contoso-construction-example.png)

## <a name="access-management"></a>gerenciamento de acesso

 Com um Domínio, você obtém um controle refinado de autorização e autenticação de cada tópico por meio do RBAC (Controle de Acesso Baseado em Função) do Azure. É possível usar essas funções para restringir cada locatário no aplicativo somente aos tópicos que você deseja permitir acesso.

O RBAC (Controle de Acesso Baseado em Função) nos Domínios de Eventos funciona da mesma maneira que o [controle de acesso gerenciado](https://docs.microsoft.com/azure/event-grid/security-authentication#management-access-control) na Grade de Eventos e no Azure. Use o RBAC para criar e reforçar definições de função personalizada nos Domínios de Eventos.

### <a name="built-in-roles"></a>Funções internas

A Grade de Eventos tem duas definições de função interna para facilitar o RBAC:

#### <a name="eventgrid-eventsubscription-contributor-preview"></a>EventGrid EventSubscription Contributor (versão prévia)

```json
[
  {
    "Description": "Lets you manage EventGrid event subscription operations.",
    "IsBuiltIn": true,
    "Id": "428e0ff05e574d9ca2212c70d0e0a443",
    "Name": "EventGrid EventSubscription Contributor (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/*",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Insights/alertRules/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/subscriptions/resourceGroups/read",
          "Microsoft.Support/*"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "Condition": null
      }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

#### <a name="eventgrid-eventsubscription-reader-preview"></a>EventGrid EventSubscription Reader (versão prévia)

```json
[
  {
    "Description": "Lets you read EventGrid event subscriptions.",
    "IsBuiltIn": true,
    "Id": "2414bbcf64974faf8c65045460748405",
    "Name": "EventGrid EventSubscription Reader (Preview)",
    "IsServiceRole": false,
    "Permissions": [
      {
        "Actions": [
          "Microsoft.Authorization/*/read",
          "Microsoft.EventGrid/eventSubscriptions/read",
          "Microsoft.EventGrid/topicTypes/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/eventSubscriptions/read",
          "Microsoft.EventGrid/locations/topicTypes/eventSubscriptions/read",
          "Microsoft.Resources/subscriptions/resourceGroups/read"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": []
       }
    ],
    "Scopes": [
      "/"
    ]
  }
]
```

## <a name="subscribing-to-topics"></a>Assinar tópicos

Assinar eventos em um tópico em um Domínio de Eventos é o mesmo que [criar uma assinatura de evento em um tópico personalizado](./custom-event-quickstart.md) ou assinar qualquer uma das ofertas internas de editores de evento do Azure.

### <a name="domain-scope-subscriptions"></a>Assinaturas de escopo de domínio

Os Domínios de Eventos também permitem assinaturas de escopo de domínio. Uma assinatura de evento em um Domínio de Eventos receberá todos os eventos enviados ao Domínio, independentemente do tópico ao qual os eventos são enviados. As assinaturas de escopo de domínio podem ser úteis para fins de gerenciamento e auditoria.

## <a name="publishing-to-an-event-domain"></a>Publicar em um Domínio de Eventos

Ao criar um Domínio de Eventos, você recebe um ponto de extremidade de publicação, como se você tivesse criado um tópico na Grade de Eventos. 

Para publicar eventos em qualquer tópico em um Domínio de Eventos, envie por push os eventos para o ponto de extremidade do Domínio, [da mesma maneira que faria para um tópico personalizado](./post-to-custom-topic.md). A única diferença é que você deve especificar o tópico para o qual você gostaria que o evento seja entregue.

Por exemplo, publicar a matriz de eventos a seguir enviaria o evento com `"id": "1111"` para o tópico `foo`, enquanto o evento com `"id": "2222"` seria enviado para o tópico `bar`:

```json
[{
  "topic": "foo",
  "id": "1111",
  "eventType": "maintenanceRequested",
  "subject": "myapp/vehicles/diggers",
  "eventTime": "2018-10-30T21:03:07+00:00",
  "data": {
    "make": "Contoso",
    "model": "Small Digger"
  },
  "dataVersion": "1.0"
},
{
  "topic": "bar",
  "id": "2222",
  "eventType": "maintenanceCompleted",
  "subject": "myapp/vehicles/tractors",
  "eventTime": "2018-10-30T21:04:12+00:00",
  "data": {
    "make": "Contoso",
    "model": "Big Tractor"
  },
  "dataVersion": "1.0"
}]
```

Os Domínios de Eventos cuidam da publicação nos tópicos para você. Em vez de publicar eventos em cada tópico gerenciado individualmente, é possível publicar todos os eventos no ponto de extremidade do Domínio. A Grade de Eventos garante que cada evento seja enviado para o tópico correto.

## <a name="limits-and-quotas"></a>Limites e cotas

### <a name="control-plane"></a>Painel de controle

Durante a versão prévia, os Domínios de Eventos serão limitados a 1.000 tópicos por domínio e 50 assinaturas de evento por tópico de um Domínio. As assinaturas de escopo do Domínio de Eventos também serão limitadas a 50.

### <a name="data-plane"></a>Plano de dados

Durante a versão prévia, a taxa de transferência de eventos para um Domínio de Eventos será limitada à mesma taxa de ingestão de 5.000 eventos por segundo a que os tópicos personalizados são limitados.

## <a name="pricing"></a>Preços

Durante a versão prévia, os Domínios de Eventos usarão os mesmos [preços de operação](https://azure.microsoft.com/pricing/details/event-grid/) de todos os outros recursos da Grade de Eventos.

As operações funcionam da mesma maneira nos Domínios de Eventos e nos tópicos personalizados. Cada entrada de um evento em um Domínio de Eventos e cada tentativa de entrega de um evento é uma operação.

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre como configurar os Domínios de Eventos, criar tópicos, criar assinaturas de eventos e publicar eventos, confira [Gerenciar os Domínios de Eventos](./how-to-event-domains.md).