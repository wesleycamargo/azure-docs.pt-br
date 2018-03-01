---
title: "Barramento de Serviço do Azure para a visão geral da integração da Grade de Eventos | Microsoft Docs"
description: "Descrição do sistema de mensagens do Barramento de Serviço e integração da Grade de Eventos"
services: service-bus-messaging
documentationcenter: .net
author: ChristianWolf42
manager: timlt
editor: 
ms.assetid: f99766cb-8f4b-4baf-b061-4b1e2ae570e4
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 02/15/2018
ms.author: chwolf
ms.openlocfilehash: bf771428505081cb60ca4417f87a4f6c2afbd25d
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/24/2018
---
# <a name="azure-service-bus-to-azure-event-grid-integration-overview"></a>Barramento de Serviço do Azure para a visão geral da integração da Grade de Eventos do Azure

O Barramento de Serviço do Azure iniciou uma nova integração à Grade de Eventos do Azure. O cenário-chave que esse recurso permite é que filas ou assinaturas do Barramento de Serviço que têm um baixo volume de mensagens não precisa ter um receptor de sondagem para mensagens em todos os momentos. Agora o Barramento de Serviço pode emitir eventos à Grade de Eventos do Azure quando há mensagens em uma fila ou assinatura quando nenhum receptor estiver presente. É possível criar assinaturas da Grade de Eventos do Azure para seus namespaces do Barramento de Serviço, escutar esses eventos e reagir aos eventos iniciando um receptor. Com esse recurso, o Barramento de Serviço pode ser usado em modelos de programação reativos.

Para habilitar o recurso, você precisa do seguinte:

* Um namespace Premium de Barramento de Serviço do Azure com pelo menos uma fila do Barramento de Serviço ou um tópico do Barramento de Serviço com pelo menos uma assinatura.
* Acesso de colaborador ao Namespace do Barramento de Serviço do Azure.
* Além disso, você precisa de uma assinatura da Grade de Eventos do Azure para o Namespace de Barramento de Serviço. Essa assinatura está recebendo a notificação da Grade de Eventos do Azure sobre haver mensagens a serem coletadas. Assinantes típicos podem ser Aplicativos Lógicos, Azure Functions ou um Gancho da Web em contato com um Aplicativo Web, o qual, então, processa as mensagens. 

![19][]

### <a name="verify-that-you-have-contributor-access"></a>Verifique se você tem acesso de colaborador

Navegue até seu Namespace do Barramento de Serviço e selecione "Controle de acesso (IAM)", conforme mostrado abaixo:

![1][]

### <a name="events-and-event-schemas"></a>Eventos e esquemas de evento

Hoje o Barramento de Serviço do Azure envia eventos para dois cenários.

* [ActiveMessagesWithNoListenersAvailable](#active-messages-available-event)
* [DeadletterMessagesAvailable](#dead-lettered-messages-available-event)

Além disso, ele usa a Segurança padrão da Grade de Eventos do Azure e [mecanismos de autenticação](https://docs.microsoft.com/en-us/azure/event-grid/security-authentication).

Para obter mais detalhes sobre Esquemas de Evento da Grade de Eventos, siga [este](https://docs.microsoft.com/en-us/azure/event-grid/event-schema) link.

#### <a name="active-messages-available-event"></a>Evento disponível de mensagens ativas

Esse evento é gerado se você tiver mensagens ativas em uma fila ou assinatura e nenhum receptor escutando.

O esquema desse evento é este:

```JSON
{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.ActiveMessagesAvailableWithNoListeners",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}
```

#### <a name="dead-lettered-messages-available-event"></a>Evento disponível de mensagens mortas

Você tem pelo menos um evento por fila de mensagens mortas, que tem mensagens e receptores não ativos.

O esquema desse evento é este:

```JSON
[{
  "topic": "/subscriptions/<subscription id>/resourcegroups/DemoGroup/providers/Microsoft.ServiceBus/namespaces/<YOUR SERVICE BUS NAMESPACE WILL SHOW HERE>",
  "subject": "topics/<service bus topic>/subscriptions/<service bus subscription>",
  "eventType": "Microsoft.ServiceBus.DeadletterMessagesAvailableWithNoListener",
  "eventTime": "2018-02-14T05:12:53.4133526Z",
  "id": "dede87b0-3656-419c-acaf-70c95ddc60f5",
  "data": {
    "namespaceName": "YOUR SERVICE BUS NAMESPACE WILL SHOW HERE",
    "requestUri": "https://YOUR-SERVICE-BUS-NAMESPACE-WILL-SHOW-HERE.servicebus.windows.net/TOPIC-NAME/subscriptions/SUBSCRIPTIONNAME/$deadletterqueue/messages/head",
    "entityType": "subscriber",
    "queueName": "QUEUE NAME IF QUEUE",
    "topicName": "TOPIC NAME IF TOPIC",
    "subscriptionName": "SUBSCRIPTION NAME"
  },
  "dataVersion": "1",
  "metadataVersion": "1"
}]
```

### <a name="how-often-and-how-many-events-are-emitted"></a>Quantos eventos são emitidos e com que frequência?

Caso tenha várias filas e tópicos/assinaturas no namespace, você obtém pelo menos um evento por fila e um por assinatura. Os eventos são emitidos imediatamente se não houver nenhuma mensagem na entidade de Barramento de Serviço, e uma nova mensagem chega ou a cada dois minutos, a menos que o Barramento de Serviço do Azure detecte um receptor ativo. A procura de mensagens não interrompe os eventos.

Por padrão, o Barramento de Serviço do Azure emite eventos para todas as entidades no namespace. Caso queira obter eventos somente para entidades específicas, consulte a seção de filtragem a seguir.

### <a name="filtering-limiting-from-where-you-get-events"></a>Filtrando, limitando de onde obter eventos

Caso queira obter apenas eventos, como uma fila ou uma assinatura em seu namespace, é possível usar filtros do tipo "Começa com" ou "Termina com" fornecidos pela Grade de Eventos do Azure. Em algumas interfaces, os filtros são chamados de filtros "Pré" e "Sufixo". Caso deseje obter eventos de várias, mas nem todas as filas e assinaturas, é possível criar várias assinaturas diferentes da Grade de Eventos do Azure e fornecer um filtro para cada uma.

## <a name="how-to-create-azure-event-grid-subscriptions-for-service-bus-namespaces"></a>Como criar assinaturas da Grade de Eventos do Azure para Namespaces de Barramento de Serviço

Há três maneiras de criar assinaturas da Grade de Eventos para Namespaces de Barramento de Serviço.

* [O portal do Azure](#portal-instructions)
* [CLI do Azure](#azure-cli-instructions)
* [PowerShell](#powershell-instructions)

## <a name="portal-instructions"></a>Instruções do portal

Para criar uma nova assinatura da Grade de Eventos do Azure, navegue até o namespace no portal do Azure e selecione a folha da Grade de Eventos. Clique em "+ Assinatura do Evento". Abaixo, é mostrado um namespace que já tem algumas assinaturas da Grade de Eventos.

![20][]

A captura de tela a seguir mostra um exemplo de como assinar um Azure Function ou um Gancho da Web sem qualquer filtragem específica:

![21][]

## <a name="azure-cli-instructions"></a>Instruções da CLI do Azure

Primeiro, verifique se você tem pelo menos a CLI do Azure versão 2.0 instalada. É possível baixar o instalador aqui. Em seguida, pressione "Windows + X" e abra um novo console do PowerShell com permissões de Administrador. Como alternativa, também é possível usar um shell de comando dentro do portal do Azure.

Execute o seguinte código:

```PowerShell
Az login

Aa account set -s “THE SUBSCRIPTION YOU WANT TO USE”

$namespaceid=(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name “<yourNamespace>“--resource-group “<Your Resource Group Name>” --query id --output tsv)

az eventgrid event-subscription create --resource-id $namespaceid --name “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” --endpoint “<your_function_url>” --subject-ends-with “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

## <a name="powershell-instructions"></a>Instruções do PowerShell

Verifique se o Azure PowerShell está instalado. Você pode encontrá-lo aqui. Em seguida, pressione "Windows + X" e abra um novo console do PowerShell com permissões de Administrador. Como alternativa, também é possível usar um shell de comando dentro do portal do Azure.

```PowerShell
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionName "<YOUR SUBSCRIPTION NAME>"

# This might be installed already
Install-Module AzureRM.ServiceBus

$NSID = (Get-AzureRmServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -Na
mespaceName "<YOUR NAMESPACE NAME>").Id 

New-AzureRmEVentGridSubscription -EventSubscriptionName “<YOUR EVENT GRID SUBSCRIPTION NAME (CAN BE ANY NOT EXISTING)>” -ResourceId $NSID -Endpoint "<YOUR FUNCTION URL>” -SubjectEndsWith “<YOUR SERVICE BUS SUBSCRIPTION NAME>”
```

A partir daqui, é possível explorar as outras opções de instalação ou [testar os eventos que estão fluindo](#test-that-events-are-flowing).

## <a name="next-steps"></a>Próximas etapas

* [Exemplos](service-bus-to-event-grid-integration-example.md) de Barramento de Serviço e da Grade de Eventos.
* Saiba mais sobre a [Grade de Eventos do Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Saiba mais sobre o [Azure Functions](https://docs.microsoft.com/en-us/azure/azure-functions/).
* Saiba mais sobre [Aplicativos Lógicos do Azure](https://docs.microsoft.com/en-us/azure/logic-apps/).
* Saiba mais sobre o [Barramento de Serviço do Azure](https://docs.microsoft.com/en-us/azure/azure-functions/).

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png