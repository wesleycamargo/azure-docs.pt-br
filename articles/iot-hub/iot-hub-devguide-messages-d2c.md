---
title: Entender o roteamento de mensagens do Hub IoT do Azure | Microsoft Docs
description: Guia do desenvolvedor – como usar o roteamento de mensagens para enviar mensagens de dispositivo para nuvem. Inclui informações de como enviar dados telemétricos e dados que não são de telemetria.
author: ash2017
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.openlocfilehash: 7c36ab2f0d4d3e5c772f8ef62c13161a2649362f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/24/2018
ms.locfileid: "46966734"
---
# <a name="use-message-routing-to-send-device-to-cloud-messages-to-different-endpoints"></a>Usar o roteamento de mensagens para enviar mensagens de dispositivo para nuvem a diferentes pontos de extremidade

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Roteamento de mensagens permite que você envie mensagens de dispositivos para serviços de nuvem de maneira automatizada, escalonável e confiável. O roteamento de mensagens pode ser usado para: 

* **Enviar mensagens de telemetria e eventos do dispositivo**, ou seja, eventos de ciclo de vida do dispositivo e eventos de alteração de dispositivo gêmeo para o ponto de extremidade interno e para os pontos de extremidade personalizados. Saiba mais sobre [pontos de extremidade de roteamentos](##routing-endpoints).

* **Filtrar dados antes de roteá-los para vários pontos de extremidade** aplicando consultas avançadas. O roteamento de mensagens permite consultar as propriedades da mensagem e o corpo da mensagem, bem como as marcas do dispositivo gêmeo e as propriedades do dispositivo gêmeo. Saiba mais sobre como usar [consultas no roteamento de mensagens](../iot-hub/iot-hub-devguide-routing-query-syntax.md).

O Hub IoT precisa de acesso de gravação a esses pontos de extremidade para que o direcionamento de mensagens funcione. Se você configurar os pontos de extremidade por meio do Portal do Azure, as permissões necessárias serão adicionadas para você. Certifique-se de configurar os serviços para dar suporte à taxa de transferência esperada. Quando você configurar pela primeira vez sua solução IoT, talvez seja necessário monitorar seus pontos de extremidade adicionais ao configurar e fazer eventuais ajustes necessários para a carga real.

O Hub IoT define um [formato comum](../iot-hub/iot-hub-devguide-messages-construct.md) para todas as mensagens de dispositivo para nuvem que permite a interoperabilidade entre protocolos. Se uma mensagem corresponder a várias rotas e todas apontarem para o mesmo ponto de extremidade, o Hub IoT entregará mensagens a esse ponto apenas uma vez. Portanto, você não precisa configurar a eliminação de duplicação nem no tópico nem na fila do Barramento de Serviço. Em filas particionados, a afinidade de partição garante a ordenação das mensagens. Use este tutorial para aprender a [configurar o roteamento de mensagem] (https://docs.microsoft.com/azure/iot-hub/tutorial-routing).

## <a name="routing-endpoints"></a>Pontos de extremidade de roteamento

Um Hub IoT tem um padrão de ponto de extremidade interno (**mensagens/eventos**) que é compatível com Hubs de Eventos. Você pode criar [pontos de extremidade personalizados](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints) para encaminhar mensagens vinculando outros serviços em sua assinatura ao Hub IoT. No momento, o Hub IoT dá suporte aos seguintes serviços como pontos de extremidade personalizados:

### <a name="built-in-endpoint"></a>Ponto de extremidade interno
Você pode usar [SDKs e integração padrão dos Hubs de Eventos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin) para receber mensagens de dispositivo para nuvem do ponto de extremidade interno (**mensagens/eventos**). Observe que depois que uma rota é criada, os dados param de fluir para o ponto de extremidade interno, a menos que uma rota seja criada para esse ponto de extremidade.

### <a name="azure-blob-storage"></a>Armazenamento do Blobs do Azure
O Hub IoT dá suporte somente à gravação de dados no Armazenamento de Blobs do Azure no formato [Apache Avro](http://avro.apache.org/). O Hub IoT envia lotes de mensagens e grava dados em um blob sempre que o lote atinge um determinado tamanho ou após um determinado período de tempo decorrido.

O Hub IoT segue a seguinte convenção de nomenclatura de arquivo padrão:
```
{iothub}/{partition}/{YYYY}/{MM}/{DD}/{HH}/{mm}
```

Você pode usar qualquer convenção de nomenclatura de arquivo, no entanto, é necessário usar todos os tokens listados. O Hub IoT gravará em um blob vazio se não houver nenhum dado para gravação.

### <a name="service-bus-queues-and-service-bus-topics"></a>Filas do Barramento de Serviço e Tópicos do Barramento de Serviço
As filas e os tópicos do Barramento de Serviço utilizados como pontos de extremidade do Hub IoT não devem ter **Sessões** nem **Detecção Duplicada** habilitadas. Se qualquer uma dessas opções estiver habilitada, o ponto de extremidade aparecerá como **Inacessível** no Portal do Azure.

### <a name="event-hubs"></a>Hubs de Eventos
Além do ponto de extremidade compatível com os Hubs de Eventos internos, você também pode encaminhar dados para pontos de extremidade personalizados do tipo Hubs de Eventos. 

Quando você usa pontos de extremidade personalizados, as mensagens só são entregues ao ponto de extremidade interno se elas não corresponderem a todas as regras. Para entregar mensagens ao ponto de extremidade interno, bem como a um ponto de extremidade personalizado, adicione uma rota que envie mensagens ao ponto de extremidade de eventos.

## <a name="reading-data-that-has-been-routed"></a>Lendo dados que foram encaminhados
Configure uma rota seguindo este [tutorial](https://docs.microsoft.com/azure/iot-hub/tutorial-routing).

Use os tutoriais a seguir para saber como ler a mensagem de um ponto de extremidade.

* Lendo do [ponto de extremidade interno](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node)
* Lendo do [Armazenamento de Blobs](https://docs.microsoft.com/azure/storage/blobs/storage-blob-event-quickstart)
* Lendo dos [Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)
* Lendo das [Filas do Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-get-started-with-queues)
* Ler dos [Tópicos do Barramento de Serviço](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-dotnet-how-to-use-topics-subscriptions)

## <a name="fallback-route"></a>Rota de fallback
A rota de fallback envia todas as mensagens que não atendem às condições de consulta em nenhuma das rotas existentes para os Hubs de Eventos internos em (**mensagens/eventos**), compatíveis com [Hubs de Eventos](https://docs.microsoft.com/azure/event-hubs/). Se o roteamento de mensagens estiver habilitado, você poderá habilitar a funcionalidade de rota de fallback. Observe que depois que uma rota é criada, os dados param de fluir para o ponto de extremidade interno, a menos que uma rota seja criada para esse ponto de extremidade. Se não houver nenhuma rota para o ponto de extremidade interno e houver uma rota de fallback habilitada, somente as mensagens que não corresponderem a nenhuma condição de consulta nas rotas serão enviadas ao ponto de extremidade interno. Além disso, se todas as rotas existentes forem excluídas, a rota de fallback precisará ser habilitada para receber todos os dados no ponto de extremidade interno. 

Você pode habilitar/desabilitar a rota de fallback na folha Portal do Azure -> Roteamento de Mensagens. Você também pode usar o Azure Resource Manager para [FallbackRouteProperties](https://docs.microsoft.com/rest/api/iothub/iothubresource/createorupdate#fallbackrouteproperties) para usar um ponto de extremidade personalizado para a rota de fallback.

## <a name="non-telemetry-events"></a>Eventos que não são de telemetria
Além da telemetria do dispositivo, o roteamento de mensagens também permite enviar eventos de alteração de dispositivo gêmeo e eventos de ciclo de vida do dispositivo. Por exemplo, se uma rota é criada com a fonte de dados definida como **eventos de alteração de dispositivo gêmeo**, o Hub IoT envia mensagens para o ponto de extremidade que contém a alteração no dispositivo gêmeo. Da mesma forma, se uma rota for criada com a fonte de dados definida como **eventos de ciclo de vida do dispositivo**, o Hub IoT enviará uma mensagem indicando se o dispositivo foi excluído ou criado. 
[O Hub IoT também se integra à Grade de Eventos do Azure](iot-hub-event-grid.md) para publicar eventos de dispositivo para dar suporte a integrações em tempo real e à automação de fluxos de trabalho com base nesses eventos. Confira as principais [diferenças entre o roteamento de mensagens e Grade de Eventos](iot-hub-event-grid-routing-comparison.md) para saber o que funciona melhor para seu cenário.

## <a name="testing-routes"></a>Testando rotas
Ao criar uma rota ou editar uma rota existente, você deve testar a consulta de rota com uma mensagem de exemplo. Você pode testar rotas individuais ou todas as rotas de uma vez. Não será roteada nenhuma mensagem aos pontos de extremidade durante o teste. O portal do Azure, o Azure Resource Manager, o Azure PowerShell e a CLI do Azure podem ser usados para o teste. Os resultados ajudam a identificar se a mensagem de exemplo correspondeu à consulta, se a mensagem não correspondeu à consulta ou se o teste não foi executado porque a sintaxe de mensagem ou de consulta de exemplo está incorreta. Para saber mais, confira [Testar Rota](https://docs.microsoft.com/rest/api/iothub/iothubresource/testroute) e [Testar todas as rotas](https://docs.microsoft.com/rest/api/iothub/iothubresource/testallroutes).

## <a name="latency"></a>Latency
Ao rotear mensagens de telemetria do dispositivo para nuvem usando pontos de extremidade internos, haverá um pequeno aumento na latência de ponta a ponta após a criação da primeira rota.

Na maioria dos casos, o aumento médio na latência é menor que 500 ms. Você pode monitorar a latência usando a métrica do Hub IoT **Roteamento: latência de mensagem para mensagens/eventos** ou **d2c.endpoints.latency.builtIn.events**. Criar ou excluir qualquer rota após a primeira não afeta a latência de ponta a ponta.

## <a name="monitoring-and-troubleshooting"></a>Monitoramento e solução de problemas
O Hub IoT fornece várias métricas relacionadas a roteamento e a ponto de extremidade que fornecem uma visão geral da integridade do hub e o número de mensagens enviadas. Você pode combinar informações de várias métricas para identificar a causa raiz de problemas. Por exemplo, use a métrica **Roteamento: mensagens de telemetria removidas** ou **d2c.telemetry.egress.dropped** para identificar o número de mensagens que foram removidas quando não corresponderam às consultas em nenhuma das rotas e a rota de fallback foi desabilitada. [Métricas do Hub IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-metrics) lista todas as métricas que são habilitadas por padrão para o Hub IoT.

Usando os logs de diagnóstico de **rotas** nas [configurações de diagnóstico](https://docs.microsoft.com/azure/iot-hub/iot-hub-monitor-resource-health) do Azure Monitor, você pode rastrear erros que ocorrem durante a avaliação de uma consulta de roteamento e integridade e de ponto de extremidade, como visto pelo Hub IoT, por exemplo, quando um ponto de extremidade está inativo. Esses logs de diagnóstico podem ser enviados ao Log Analytics, aos Hubs de Eventos ou ao Armazenamento do Azure para processamento personalizado.

## <a name="next-steps"></a>Próximas etapas
* Para aprender como criar Rotas de Mensagens, confira o tutorial [Processar mensagens de dispositivo para nuvem do Hub IoT usando rotas](../iot-hub/tutorial-routing.md).
* Os [Inícios Rápidos](https://docs.microsoft.com/azure/iot-hub/quickstart-send-telemetry-node) mostram como enviar mensagens de dispositivo para nuvem usando dispositivos simulados.
* Para saber mais sobre os SDKs que você pode usar para enviar mensagens de dispositivo para nuvem, confira [SDKs de IoT do Azure](../iot-hub/iot-hub-devguide-sdks.md).
