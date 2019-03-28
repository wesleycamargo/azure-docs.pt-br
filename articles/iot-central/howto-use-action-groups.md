---
title: Executar várias ações de uma regra de Azure IoT Central | Microsoft Docs
description: Executar várias ações de uma única regra de Central da IoT e criar grupos reutilizáveis de ações que podem ser executados de várias regras.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/27/2019
ms.locfileid: "58522874"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Várias ações para executar a partir de uma ou mais regras de grupo

*Este artigo se aplica a desenvolvedores e administradores.*

Azure IoT Central, você cria regras para executar ações quando uma condição for atendida. Regras são baseadas em eventos ou de telemetria do dispositivo. Por exemplo, você pode notificar um operador quando a temperatura em um dispositivo excede um limite. Este artigo descreve como usar [do Azure Monitor](../azure-monitor/overview.md) *grupos de ação* para anexar várias ações em uma regra de IoT Central. Você pode anexar a um grupo de ação para várias regras. Uma [grupo de ação](../azure-monitor/platform/action-groups.md) é uma coleção de preferências de notificação definidos pelo proprietário de uma assinatura do Azure.

## <a name="prerequisites"></a>Pré-requisitos

- Um aplicativo com pagamento conforme o uso
- Uma conta do Azure e uma assinatura para criar e gerenciar grupos de ação do Azure Monitor

## <a name="create-action-groups"></a>Criar grupos de ação

Você pode [criar e gerenciar grupos de ação no portal do Azure](../azure-monitor/platform/action-groups.md) ou com um [modelo do Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Um grupo de ação pode:

- Enviar notificações, como um email, um SMS ou fazer uma voz de chamada.
- Execute uma ação, como chamar um webhook.

Captura de tela a seguir mostra um grupo de ação que envia email e notificações por SMS e que chame um webhook:

![Grupo de ações](media/howto-use-action-groups/actiongroup.png)

Para usar um grupo de ações em uma regra de Central da IoT, o grupo de ação deve estar na mesma assinatura do Azure como o aplicativo de IoT Central.

## <a name="use-an-action-group"></a>Usar um grupo de ação

Para usar um grupo de ação no aplicativo Central da IoT, primeiro crie uma regra de evento ou de telemetria. Quando você adiciona uma ação para a regra, selecione **grupos de ação do Azure Monitor**:

![Escolher ação](media/howto-use-action-groups/chooseaction.png)

Escolha um grupo de ação da sua assinatura do Azure:

![Escolha o grupo de ação](media/howto-use-action-groups/chooseactiongroup.png)

Clique em **Salvar**. O grupo de ação agora aparece na lista de ações a serem executadas quando a regra for disparada:

![Salva o grupo de ação](media/howto-use-action-groups/savedactiongroup.png)

A tabela a seguir resume as informações enviadas para os tipos de ação com suporte:

| Tipo de ação | Formato da saída |
| ----------- | -------------- |
| Email       | Modelo de email padrão Central da IoT |
| sms         | Alerta da Central da IoT do Azure: ${applicationName} - "${ruleName}" disparado em "${deviceName}" em ${triggerDate} ${triggerTime} |
| Voz       | Alerta da Central de I.O.T do Azure: regra "${ruleName}" disparado no dispositivo "${deviceName}" em ${triggerDate} ${triggerTime}, no aplicativo ${applicationName} |
| webhook     | { "schemaId" : "AzureIoTCentralRuleWebhook", "data": {[regular webhook payload](#payload)} } |

O texto a seguir está um exemplo de mensagem SMS de um grupo de ação:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> O JSON a seguir mostra um exemplo de carga de ação de webhook:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Próximas etapas

Agora que você aprendeu como usar grupos de ação com regras, a próxima etapa sugerida é saber como [gerenciar seus dispositivos](howto-manage-devices.md).
