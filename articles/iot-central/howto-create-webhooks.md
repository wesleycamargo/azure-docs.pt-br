---
title: Criar webhooks em regras no Microsoft Azure IoT Central | Microsoft Docs
description: Crie webhooks no Microsoft Azure IoT Central para notificar automaticamente outros aplicativos ao acionar regras.
author: viv-liu
ms.author: viviali
ms.date: 09/17/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 896d4e9c775fa0b0c8eb062d11d141901daa7242
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/19/2018
ms.locfileid: "46295954"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Criar ações de webhook em regras no Microsoft Azure IoT Central

*Este tópico se aplica a construtores e administradores.*

Webhooks permitem que você conecte o aplicativo IoT Central a outros aplicativos e serviços de monitoramento remoto e notificações. Webhooks notificam automaticamente os outros aplicativos e serviços conectados sempre que uma regra é acionada no aplicativo IoT Central. O aplicativo d IoT Central enviará uma solicitação POST ao ponto de extremidade HTTP do outro aplicativo sempre que uma regra for disparada. A carga conterá detalhes do dispositivo e detalhes do gatilho de regra. 

## <a name="how-to-set-up-the-webhook"></a>Como configurar o webhook
Neste exemplo, você conectará o RequestBin para ser notificado quando as regras disparam usando webhooks. 

1. Abra [RequestBin](http://requestbin.net/). 
1. Crie um novo RequestBin e copie a **URL do Compartimento**. 
1. Crie uma [regra de telemetria](howto-create-telemetry-rules.md) ou uma [regra de evento](howto-create-event-rules.md). Salve a regra e adicione uma nova ação.
![Tela de criação do webhook](media/howto-create-webhooks/webhookcreate.PNG)
1. Escolha a ação do webhook, forneça um nome de exibição e cole a URL do Compartimento como URL de Retorno de Chamada. 
1. Salve a regra.

Agora, quando a regra é acionada, você deve ver uma nova solicitação aparecer no RequestBin.

## <a name="payload"></a>Carga útil
Quando uma regra é acionada, uma solicitação HTTP POST é feita para a URL de retorno de chamada que contém uma carga json com as medidas, dispositivo, regra e detalhes do aplicativo. Para uma regra de telemetria, a carga pode ser semelhante à seguinte:

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Limitações conhecidas
Atualmente não há nenhuma maneira programática de assinar/cancelar a assinatura desses webhooks por meio de uma API.

Se você tiver algumas ideias para melhorar esse recurso, poste suas sugestões em nosso [fórum Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu a configurar e usar webhooks, a próxima etapa sugerida é explorar [Compilar fluxos de trabalho no Microsoft Flow](howto-add-microsoft-flow.md).
