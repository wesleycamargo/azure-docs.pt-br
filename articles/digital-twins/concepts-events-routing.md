---
title: Roteamento de eventos e mensagens com Gêmeos Digitais do Azure | Microsoft Docs
description: Visão geral de roteamento de eventos e mensagens de pontos de extremidade de serviço com os Gêmeos Digitais do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e16d27314a159b124e35560ffb8cd9685fc5c7a0
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438006"
---
# <a name="routing-events-and-messages"></a>Roteamento de eventos e mensagens

As soluções de IoT geralmente unem vários serviços poderosos que incluem armazenamento, análises e muito mais. Este artigo descreve como conectar os aplicativos Gêmeos Digitais do Azure aos serviços analíticos, AI e de armazenamento do Azure para fornecer insight e funcionalidades mais profundas.

## <a name="route-types"></a>Tipos de rota  

O Gêmeos Digitais do Azure oferece duas maneiras de integrar eventos da IoT em outros serviços ou aplicativos de negócios do Azure:

* **Roteamento de eventos dos Gêmeos Digitais do Azure**: Um objeto no grafo espacial que é alterado, dados telemétricos que são recebidos ou uma função definida pelo usuário que cria uma notificação com base em condições predefinidas pode disparar eventos dos Gêmeos Digitais do Azure. Os usuários podem enviar esses eventos para [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/), [tópicos do Barramento de Serviço do Azure](https://azure.microsoft.com/services/service-bus/) ou [Grade de Eventos do Azure](https://azure.microsoft.com/services/event-grid/) para processamento adicional.

* **Roteamento da telemetria de dispositivo**: Além do roteamento de eventos, os Gêmeos Digitais do Azure também podem encaminhar mensagens de telemetria bruta de dispositivo para os Hubs de Eventos para insights e análise posteriores. Esses tipos de mensagens não são processados pelo Gêmeos Digitais do Azure. E eles só estiverem encaminhados para o hub de eventos.

Os usuários podem especificar um ou mais pontos de extremidade de saída para enviar eventos ou encaminhar mensagens. Eventos e mensagens serão enviados aos pontos de extremidade de acordo com essas preferências de roteamentos predefinidas. Em outras palavras, os usuários podem especificar um determinado ponto de extremidade para receber eventos de operação de gráfico, outro para receber eventos de telemetria de dispositivo e assim por diante.

![Roteamento de eventos do Gêmeos Digitais do Azure][1]

Roteamento para Hubs de Eventos mantém a ordem na qual as mensagens de telemetria são enviadas. Então eles chegam ao ponto final na mesma sequência em que foram originalmente recebidos. A Grade de Eventos e o Barramento de Serviço do Microsoft Azure não garantem que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. No entanto, o esquema do evento inclui um registro de data e hora que pode ser usado para identificar a ordem após os eventos chegarem ao terminal.

## <a name="route-implementation"></a>Implementação de rota

O serviço de Gêmeos Digitais do Azure atualmente tem suporte para os seguintes **EndpointTypes**:

* **Hubs de eventos do Azure** é o ponto de extremidade de cadeia de caracteres de conexão de Hubs de eventos.
* **Barramento de serviço do Azure** é o ponto de extremidade de cadeia de caracteres de conexão do barramento de serviço.
* **Grade de Eventos do Azure** é o ponto de extremidade de cadeia de caracteres de conexão de grade de eventos.

O recurso de Gêmeos Digitais do Azure atualmente tem suporte para os seguintes **EventTypes** que serão enviados para o ponto de extremidade escolhido:

* **DeviceMessages** são mensagens de telemetria enviadas dos dispositivos dos usuários e encaminhadas pelo sistema.
* **TopologyOperation** é uma operação que altera o gráfico ou os metadados do gráfico. Um exemplo é adicionar ou excluir uma entidade, como um espaço.
* **SpaceChange** é uma alteração no valor computado de um espaço que resulta de uma mensagem de telemetria de dispositivo.
* **SensorChange** é uma alteração no valor computado de um sensor que resulta de uma mensagem de telemetria do dispositivo.
* **UdfCustom** é uma notificação personalizada de uma função definida pelo usuário.

> [!IMPORTANT]  
> Nem todos os **EndpointTypes** dão suporte a todos **EventTypes**.
> Consulte a tabela a seguir para o **EventTypes** que são permitidos para cada **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| EventHub|     X          |         X         |     X       |      X       |   X       |
| ServiceBus|              |         X         |     X       |      X       |   X       |
| EventGrid|               |         X         |     X       |      X       |   X       |

>[!NOTE]  
>Para obter mais informações sobre como criar endpoints e exemplos de esquema de eventos, consulte [Egress e endpoints](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Próximas etapas

- Para saber mais sobre os limites de visualização do Gêmeos Digitais do Azure, consulte [Limites do serviço de visualização pública](concepts-service-limits.md).
- Para testar um exemplo de Gêmeos Digitais do Azure, consulte a [início rápido para encontrar salas disponíveis](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png
