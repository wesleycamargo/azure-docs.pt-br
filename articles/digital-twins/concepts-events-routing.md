---
title: Roteamento de eventos e mensagens com Gêmeos Digitais do Azure | Microsoft Docs
description: Visão geral de roteamento de eventos e mensagens de pontos de extremidade de serviço com os Gêmeos Digitais do Azure
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 9b861f0991b11637c7b27b645d4506e658ea53b3
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323713"
---
# <a name="routing-events-and-messages"></a>Roteamento de eventos e mensagens

Soluções de IoT geralmente unem vários serviços avançados, incluindo armazenamento, análise e muito mais. Este artigo descreve como conectar aplicativos Gêmeos Digitais do Azure a serviços de análise, inteligência artificial e armazenamento do Azure para enriquecê-los com mais insight e funcionalidades.

## <a name="route-types"></a>Tipos de rota

O recurso Gêmeos Digitais oferece duas maneiras de integrar eventos IoT em outros serviços ou aplicativos de negócios do Azure:

* **Roteamento de eventos de Gêmeos Digitais**: eventos do Gêmeos Digitais do Azure podem ser disparados quando um objeto no gráfico espacial muda, quando os dados de telemetria são recebidos ou quando uma função definida pelo usuário cria uma notificação com base em condições predefinidas. Os usuários podem enviar esses eventos para [Hubs de Eventos](https://azure.microsoft.com/services/event-hubs/), [Tópicos do Barramento de Serviço](https://azure.microsoft.com/services/service-bus/) ou [Grades de Eventos](https://azure.microsoft.com/services/event-grid/) para processamento adicional.

* **Telemetria de dispositivo de roteamento**: além dos eventos de roteamentos, o recurso Gêmeos Digitais do Azure também pode rotear mensagens de telemetria brutas do dispositivo para os Hubs de Eventos para obter mais insights e análises. Esses tipos de mensagens não são processados pelo recurso Gêmeos Digitais e só são encaminhados para o **Hub de Eventos**.

Os usuários podem especificar um ou mais pontos de extremidade de saída para enviar eventos ou encaminhar mensagens. Eventos e mensagens serão enviados aos pontos de extremidade de acordo com essas preferências de roteamentos predefinidas. Em outras palavras, os usuários podem especificar determinado um ponto de extremidade para receber eventos de operação de gráfico, outro para receber eventos de telemetria do dispositivo e assim por diante.

![Roteamento de eventos de Gêmeos Digitais][1]

O roteamento para **Hubs de Eventos** manterá a ordem em que as mensagens de telemetria são enviadas para que cheguem ao ponto de extremidade na mesma sequência em que foram recebidas originalmente. A **Grade de Eventos** e o **Barramento de Serviço** não assegura que os pontos de extremidade receberão eventos na mesma ordem em que ocorreram. No entanto, o esquema de eventos inclui um carimbo data/hora que pode ser usado para identificar a ordem após os eventos chegarem ao ponto de extremidade.

## <a name="route-implementation"></a>Implementação de rota

O serviço de Gêmeos Digitais do Azure atualmente tem suporte para os seguintes **EndpointTypes**:

* **EventHub**: é o ponto de extremidade de cadeia de conexão do Hub de Eventos.
* **ServiceBus**: é o ponto de extremidade da cadeia de conexão do Barramento de Serviço.
* **EventGrid**: é o ponto de extremidade da cadeia de conexão da Grade de Eventos.

O recurso de Gêmeos Digitais do Azure atualmente tem suporte para os seguintes **EventTypes** que serão enviados para o ponto de extremidade escolhido:

* **DeviceMessages**: são mensagens de telemetria enviadas dos dispositivos dos usuários e encaminhadas pelo sistema.
* **TopologyOperation**: são operações que alteram o grafo ou os metadados do grafo. Por exemplo, adicionar ou excluir uma entidade, como um espaço.
* **SpaceChange**: são alterações em um valor computado do espaço como resultado de uma mensagem de telemetria do dispositivo.
* **SensorChange**: são alterações em um valor computado do sensor como resultado de uma mensagem de telemetria do dispositivo.
* **UdfCustom**: são notificações personalizadas de uma função definida pelo usuário.

> [!IMPORTANT]
> Nem todos os **EndpointTypes** dão suporte a todos **EventTypes**.
> Confira a tabela abaixo os **EventTypes** permitidos para cada **EndpointType**.

|             | DeviceMessages | TopologyOperation | SpaceChange | SensorChange | UdfCustom |
| ----------- | -------------- | ----------------- | ----------- | ------------ | --------- |
| **EventHub**|     X          |         X         |     X       |      X       |   X       |
| **Barramento de Serviço**|              |         X         |     X       |      X       |   X       |
| **EventGrid**|               |         X         |     X       |      X       |   X       |

>[!NOTE]
>Para obter mais detalhes sobre como criar pontos de extremidade e exemplos de esquema de eventos, confira [Pontos de Extremidade e Egresso](how-to-egress-endpoints.md).

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre os limites de versão prévia pública, leia [Limites de versão prévia do Gêmeos Digitais do Azure](concepts-service-limits.md).

Para testar um exemplo de Gêmeos Digitais do Azure, leia [Início Rápido para encontrar salas disponíveis](quickstart-view-occupancy-dotnet.md).

<!-- Images -->
[1]: media/concepts/digital-twins-events-routing.png