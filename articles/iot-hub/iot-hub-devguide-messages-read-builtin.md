---
title: Entenda o ponto de extremidade interno Azure Hub IoT | Microsoft Docs
description: Guia do Desenvolvedor – descreve como usar o ponto de extremidade compatível com hub de eventos interno para ler mensagens de dispositivo para nuvem.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: dobett
ms.openlocfilehash: e43d00fde0f76efa4398865757c44d94592b8291
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/01/2018
ms.locfileid: "47585502"
---
# <a name="read-device-to-cloud-messages-from-the-built-in-endpoint"></a>Ler mensagens de dispositivo para a nuvem do ponto de extremidade interno

Por padrão, as mensagens são roteadas para o ponto de extremidade voltado para o serviço interno (**mensagens/eventos**) compatíveis com [Hubs de Eventos](http://azure.microsoft.com/documentation/services/event-hubs/
). Esse ponto de extremidade é atualmente apenas exposto usando o protocolo [AMQP](https://www.amqp.org/) na porta 5671. Um Hub IoT expõe as propriedades a seguir para permitir que você controle as **mensagens/eventos** do ponto de extremidade do sistema de mensagens interno compatível com o Hub de Eventos.

| Propriedade            | DESCRIÇÃO |
| ------------------- | ----------- |
| **Contagem de partição** | Defina essa propriedade no momento da criação para determinar o número de [partições](../event-hubs/event-hubs-features.md#partitions) para inclusão do evento de dispositivo para nuvem. |
| **Período de retenção**  | Esta propriedade especifica por quanto tempo, em dias, as mensagens são retidas pelo Hub IoT. O padrão é de um dia, mas pode ser aumentado para sete dias. |

O Hub IoT também permite que você gerencie grupos de consumidores no ponto de extremidade de recebimento do dispositivo para a nuvem interno.

Se você estiver usando [roteamento de mensagens](iot-hub-devguide-messages-d2c.md) e a [rota de fallback](iot-hub-devguide-messages-d2c.md#fallback-route) estiver habilitada, todas as mensagens que não corresponderem a uma consulta em qualquer rota serão gravadas para o ponto de extremidade interno. Se você desabilitar esta rota de fallback, as mensagens que não corresponderem a nenhuma consulta serão descartadas.

Você pode modificar o tempo de retenção, seja de maneira programática usando as [APIs REST do provedor de recursos do Hub IoT](/rest/api/iothub/iothubresource) ou com o [portal do Azure](https://portal.azure.com).

O Hub IoT expõe um ponto de extremidade interno de **mensagens/eventos** para os serviços de back-end lerem as mensagens de dispositivo para nuvem recebidas por seu hub. Este ponto de extremidade é compatível com o Hub de Eventos, o que permite que você use qualquer um dos mecanismos para os quais o serviço Hubs de Eventos dá suporte para ler as mensagens.

## <a name="read-from-the-built-in-endpoint"></a>Leitura do ponto de extremidade interno

Ao usar o [SDK do Barramento de Serviço do Azure para .NET](https://www.nuget.org/packages/WindowsAzure.ServiceBus) ou [Hubs de Eventos – Host Processador de Evento](..//event-hubs/event-hubs-dotnet-standard-getstarted-receive-eph.md), você poderá usar quaisquer cadeias de conexão do Hub IoT com as permissões corretas. Em seguida, use **mensagens/eventos** como o nome do Hub de Eventos.

Ao usar os SDKs (ou integrações de produtos) que não reconhecem o Hub IoT, será necessário recuperar um ponto de extremidade compatível com o Hub de Eventos e o nome compatível com o Hub de Eventos:

1. Entre no [Portal do Azure](https://portal.azure.com) e navegue até o Hub IoT.

2. Clique em **Pontos de extremidade internos**.

3. A seção **Eventos** contém os seguintes valores: **Ponto de extremidade compatível com Hub de Eventos**, **Nome compatível com o Hub de Eventos**, **Partições**, **Ponto de retenção** e **Grupos de consumidores**.

    ![Configurações de dispositivo para a nuvem](./media/iot-hub-devguide-messages-read-builtin/eventhubcompatible.png)

O SDK do Hub IoT requer o nome de ponto de extremidade do Hub IoT, que é **mensagens/eventos** conforme mostrado em **Pontos de Extremidade**.

Se o SDK sendo usado exigir um valor de **Nome do host** ou **Namespace**, remova o esquema do **Ponto de extremidade compatível com o Hub de Eventos**. Por exemplo, se o ponto de extremidade compatível com o Hub de Eventos fosse **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, o **Nome do Host** seria **iothub-ns-myiothub-1234.servicebus.windows.net**. O **Namespace** seria **iothub-ns-myiothub-1234**.

Dessa forma, você poderá usar qualquer política de acesso compartilhado com permissões **ServiceConnect** para se conectar ao Hub de Eventos especificado.

Caso você precise criar uma cadeia de conexão do Hub de Eventos usando as informações anteriores, use o seguinte padrão:

`Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}`

Os SDKs e integrações que você pode usar com os pontos de extremidade compatíveis com o Hub de Eventos expostos pelo Hub IoT incluem os itens a seguir na lista:

* [Cliente Java dos Hubs de Eventos](https://github.com/Azure/azure-event-hubs-java).
* [Spout do Apache Storm](../hdinsight/storm/apache-storm-develop-csharp-event-hub-topology.md). Você pode exibir a [fonte do spout](https://github.com/apache/storm/tree/master/external/storm-eventhubs) no GitHub.
* [Integração do Apache Spark](../hdinsight/spark/apache-spark-eventhub-streaming.md).

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre pontos de extremidade do Hub IoT, confira [Pontos de extremidade do Hub IoT](iot-hub-devguide-endpoints.md).

* Os [Inícios Rápidos](quickstart-send-telemetry-node.md) mostram como enviar mensagens de dispositivo para nuvem de dispositivos simulados e ler as mensagens do ponto de extremidade interno. 

Para saber mais, confira o tutorial [Processar as mensagens de dispositivo para nuvem do Hub IoT usando rotas](tutorial-routing.md).

* Se você quiser encaminhar as mensagens de dispositivo para nuvem para pontos de extremidade personalizados, confira [Usar rotas de mensagens e pontos de extremidade personalizados para mensagens de dispositivo para nuvem](iot-hub-devguide-messages-read-custom.md).