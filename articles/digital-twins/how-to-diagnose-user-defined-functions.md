---
title: Como depurar UDFs nos Gêmeos Digitais do Azure | Microsoft Docs
description: Diretrizes sobre como depurar UDFs nos Gêmeos Digitais do Azure
author: stefanmsft
manager: deshner
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: stefanmsft
ms.openlocfilehash: 9476db888a4bfae2d43ae4eec340972d4c2eb714
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/14/2018
ms.locfileid: "53413006"
---
# <a name="how-to-debug-issues-with-user-defined-functions-in-azure-digital-twins"></a>Como depurar problemas com funções definidas pelo usuário em Gêmeos Digitais do Azure

Este artigo resume como diagnosticar funções definidas pelo usuário. Em seguida, ele identifica alguns dos cenários mais comuns encontrados ao trabalhar com eles.

## <a name="debug-issues"></a>Problemas de depuração

Saber como diagnosticar problemas que surgem em sua instância de Gêmeos Digitais do Azure ajudará você a identificar com eficiência o problema, a causa do problema e uma solução.

### <a name="enable-log-analytics-for-your-instance"></a>Habilitar o log analytics para sua instância

Os logs e métricas para sua instância de Gêmeos Digitais do Azure são expostos por meio do Azure Monitor. A documentação a seguir pressupõe que você tenha criado um espaço de trabalho do [Azure Log Analytics](../azure-monitor/log-query/log-query-overview.md) por meio do [Portal do Azure](../azure-monitor/learn/quick-create-workspace.md), por meio da [CLI do Azure](../azure-monitor/learn/quick-create-workspace-cli.md) ou por meio do [ PowerShell](../azure-monitor/learn/quick-create-workspace-posh.md).

> [!NOTE]
> Pode haver um atraso de 5 minutos ao enviar eventos para o **Log Analytics** pela primeira vez.

Leia o artigo ["Coletar e consumir dados de log dos recursos do Azure"](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para habilitar as configurações de diagnóstico para sua instância de Gêmeos Digitais do Azure por meio do Portal, da CLI do Azure ou do PowerShell. Selecione todas as categorias de log, métricas e seu espaço de trabalho do Azure Log Analytics.

### <a name="trace-sensor-telemetry"></a>Sensor de telemetria de rastreamento

Certifique-se de que as configurações de diagnóstico estejam habilitadas em sua instância de Gêmeos Digitais do Azure, que todas as categorias de log estejam selecionadas e que os logs estejam sendo enviados para o Azure Log Analytics.

Para corresponder a uma mensagem de telemetria do sensor para seus respectivos logs, você pode especificar uma ID de Correlação nos dados de evento que estão sendo enviados. Para fazer isso, defina a propriedade `x-ms-client-request-id` para um GUID.

Depois de enviar a telemetria, abra o Azure Log Analytics para consultar os logs usando a ID de Correlação definida:

```Kusto
AzureDiagnostics
| where CorrelationId == 'YOUR_CORRELATION_IDENTIFIER'
```

| Valor da consulta | Substitua por |
| --- | --- |
| YOUR_CORRELATION_IDENTIFIER | A ID de Correlação que foi especificada nos dados de evento |

Se você registrar em log a sua função definida pelo usuário, esses logs serão exibidos na sua instância do Azure Log Analytics com a categoria `UserDefinedFunction`. Para recuperá-los, insira a seguinte condição de consulta no Azure Log Analytics:

```Kusto
AzureDiagnostics
| where Category == 'UserDefinedFunction'
```

Para obter mais informações sobre as operações de consulta avançada, confira [Introdução às consultas](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries).

## <a name="identify-common-issues"></a>Identificar problemas comuns

O diagnóstico e a identificação de problemas comuns são importantes durante a solução de problemas em sua solução. Vários problemas comuns encontrados ao desenvolver funções definidas pelo usuário são resumidos abaixo.

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

### <a name="ensure-a-role-assignment-was-created"></a>Verificar se uma atribuição de função foi criada

Sem uma atribuição de função criada na API de Gerenciamento, a função definida pelo usuário não terá acesso para executar quaisquer ações como enviar notificações, recuperar metadados e definir valores computadores na topologia.

Verifique se existe uma atribuição de função para sua função definida pelo usuário por meio de sua API de Gerenciamento:

```plaintext
GET YOUR_MANAGEMENT_API_URL/roleassignments?path=/&traverse=Down&objectId=YOUR_USER_DEFINED_FUNCTION_ID
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_USER_DEFINED_FUNCTION_ID* | A ID da função definida pelo usuário para a qual serão recuperadas as atribuições de função|

Se nenhuma atribuição de função for recuperada, siga este artigo sobre [Como criar uma atribuição de função para sua função definida pelo usuário](./how-to-user-defined-functions.md).

### <a name="check-if-the-matcher-will-work-for-a-sensors-telemetry"></a>Verifique se nenhum correspondente funcionará para telemetria de um sensor

Com a chamada a seguir à API de Gerenciamento das instâncias dos Gêmeos Digitais do Azure, você poderá determinar se algum correspondente se aplica ao sensor determinado.

```plaintext
GET YOUR_MANAGEMENT_API_URL/matchers/YOUR_MATCHER_IDENTIFIER/evaluate/YOUR_SENSOR_IDENTIFIER?enableLogging=true
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_MATCHER_IDENTIFIER* | A ID do correspondente que você deseja avaliar |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor que você deseja avaliar |

Resposta:

```JavaScript
{
    "success": true,
    "logs": [
        "$.dataType: \"Motion\" Equals \"Motion\" => True"
    ]
}
```

### <a name="check-what-a-sensor-will-trigger"></a>Verificar o que um sensor irá disparar

Com a chamada a seguir à API de Gerenciamento das instâncias de Gêmeos Digitais do Azure, você será capaz de determinar os identificadores de suas funções definidas pelo usuário que serão disparadas pela telemetria de entrada do sensor determinado:

```plaintext
GET YOUR_MANAGEMENT_API_URL/sensors/YOUR_SENSOR_IDENTIFIER/matchers?includes=UserDefinedFunctions
```

| Parâmetro | Substitua por |
| --- | --- |
| *YOUR_SENSOR_IDENTIFIER* | A ID do sensor que enviará telemetria |

Resposta:

```JavaScript
[
    {
        "id": "48a64768-797e-4832-86dd-de625f5f3fd9",
        "name": "MotionMatcher",
        "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
        "conditions": [
            {
                "id": "024a067a-414f-415b-8424-7df61392541e",
                "target": "Sensor",
                "path": "$.dataType",
                "value": "\"Motion\"",
                "comparison": "Equals"
            }
        ],
        "userDefinedFunctions": [
            {
                "id": "373d03c5-d567-4e24-a7dc-f993460120fc",
                "spaceId": "2117b3e1-b6ce-42c1-9b97-0158bef59eb7",
                "name": "Motion User-Defined Function",
                "disabled": false
            }
        ]
    }
]
```

### <a name="issue-with-receiving-notifications"></a>Problema com o recebimento de notificações

Quando não estiver recebendo notificações de dentro da função definida pelo usuário disparada, verifique se o parâmetro de tipo de objeto de topologia corresponde ao tipo de identificador que está sendo usado.

Exemplo **Incorreto**:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will not work'
};

sendNotification(telemetry.SensorId, "Space", JSON.stringify(customNotification));
```

Este cenário surge porque o identificador usado se refere a um sensor enquanto o tipo de objeto de topologia especificado é 'Espaço'.

Exemplo **Correto**:

```JavaScript
var customNotification = {
    Message: 'Custom notification that will work'
};

sendNotification(telemetry.SensorId, "Sensor", JSON.stringify(customNotification));
```

A maneira mais fácil de não ter esse problema é usar o método `Notify` no objeto de metadados.

Exemplo:

```JavaScript
function process(telemetry, executionContext) {
    var sensorMetadata = getSensorMetadata(telemetry.SensorId);

    var customNotification = {
        Message: 'Custom notification'
    };

    // Short-hand for above methods where object type is known from metadata.
    sensorMetadata.Notify(JSON.stringify(customNotification));
}
```

## <a name="common-diagnostic-exceptions"></a>Exceções de diagnóstico comuns

Se habilitar as configurações de diagnóstico, você poderá encontrar estas exceções comuns:

1. **Limitação**: se sua função definida pelo usuário exceder os limites da taxa de execução descritos no artigo [Limites de serviço](./concepts-service-limits.md), ela será limitada. A limitação não envolve nenhuma outra operação em execução com êxito até que os limites expirem.

1. **Dados Não Encontrados**: se sua função definida pelo usuário tenta acessar metadados que não existem, a operação falhará.

1. **Não Autorizado**: se sua função definida pelo usuário não tiver uma atribuição de função definida ou não tiver permissões suficientes para acessar alguns metadados da topologia, a operação falhará.

## <a name="next-steps"></a>Próximas etapas

Saiba como habilitar [logs e monitoramento](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) nos Gêmeos Digitais do Azure.
