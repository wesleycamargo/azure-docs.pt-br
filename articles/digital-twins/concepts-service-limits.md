---
title: Limites de serviço de versão prévia dos Gêmeos Digitais do Azure | Microsoft Docs
description: Reconhecimento dos limites de serviço de versão prévia públicos dos Gêmeos Digitais do Azure
author: dwalthermsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: dwalthermsft
ms.openlocfilehash: aa5f6053bf1c98d2b84c02617da30f5d856ed3fc
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/15/2018
ms.locfileid: "49323657"
---
# <a name="public-preview-service-limits"></a>Limites do serviço de visualização pública

Durante a Visualização Pública, os Gêmeos Digitais do Azure terão assinatura temporária, a instância e limites de taxa que são descritos abaixo.

Essas restrições existem para ajudar a simplificar o aprendizado sobre o novo serviço e seus diversos recursos.

> [!NOTE]
> Esses limites serão aumentados e/ou removidos pela GA (disponibilidade geral).

## <a name="per-subscription-limits"></a>Limites de Pré-Assinatura

Durante a Visualização Pública, cada assinatura do Azure pode criar ou ter executado exatamente uma instância de Gêmeos Digitais do Azure de uma vez.

> [!TIP]
> Excluir sua instância permitirá que você crie um novo.

## <a name="per-instance-limits"></a>Limites por instância

Por sua vez, cada instância de Gêmeos Digitais do Azure podem ter:

- Um `IoTHub` Recurso
- Um `EventHub` ponto de extremidade para o tipo de evento DeviceMessage
- Até três `EventHub`, `ServiceBus`, ou `EventGrid` pontos de extremidade do tipo de evento `SensorChange`, `SpaceChange`, `TopologyOperation`, ou `UdfCustom`

## <a name="management-api-limits"></a>Limites da API de Gerenciamento

Os limites de taxa de solicitação para a API de gerenciamento são:

- 100 solicitações por segundo à API de Gerenciamento
- Uma única consulta de API de gerenciamento pode retornar até 1000 objetos

> [!IMPORTANT]
> Se você exceder o limite de 1000 objetos, você receberá um erro e será necessário simplificar sua consulta.

## <a name="udf-rate-limits"></a>Limites de taxa UDF

Os limites a seguir definem o número total de todas as chamadas de função definida pelo usuário feitas à sua instância de Gêmeos Digitais do Azure:

- 400 chamadas de biblioteca de cliente por segundo
- 100 chamadas SendNotification por segundo

> [!NOTE]
> As seguintes ações podem causar limites de taxa adicional a ser aplicado temporariamente:
> - Edições de metadados de objeto de topologia
> - Atualizações de definição UDF
> - Dispositivos que enviam telemetria pela primeira vez

## <a name="device-telemetry-limits"></a>Limites de telemetria do dispositivo

Os limites abaixo da tampado número total de todas as mensagens que seus dispositivos podem enviar para a sua instância dos Gêmeos Digitais do Azure:

- 100 mensagens por segundo

## <a name="next-steps"></a>Próximas etapas

Para testar um exemplo de Gêmeos Digitais do Azure,vá para [Início Rápido para encontrar salas disponíveis](./quickstart-view-occupancy-dotnet.md).