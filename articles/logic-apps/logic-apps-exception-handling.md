---
title: "Tratamento de erros e exceções para Aplicativos Lógicos no Azure | Microsoft Docs"
description: "Padrões para tratamento de erros e exceções em Aplicativos Lógicos."
services: logic-apps
documentationcenter: 
author: dereklee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: 
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: logic-apps
ms.date: 01/31/2018
ms.author: deli; LADocs
ms.openlocfilehash: 91819d0fba30dd2ada981435fa13b8ae0a7fcc45
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/05/2018
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Tratar erros e exceções em Aplicativos Lógicos

A administração adequada do tempo de inatividade ou problemas de sistemas dependentes podem representar um desafio para qualquer arquitetura de integração. Para criar integrações robustas que sejam resilientes a falhas e problemas, os Aplicativos Lógicos oferecem uma experiência de primeira classe para tratar de erros e exceções. 

## <a name="retry-policies"></a>Políticas de repetição

Você pode usar uma política de repetição para tratamento de erros e exceções mais básicas. Se uma solicitação inicial tiver atingido o tempo limite ou falhado, o que significa qualquer solicitação que resulte em uma resposta 429 ou 5xx, essa política definirá se e como a ação repetirá a solicitação. 

Há quatro tipos de políticas de repetição: padrão, nenhum, intervalo fixo e intervalo exponencial. Se a sua definição de fluxo de trabalho não tiver uma política de repetição, a política padrão, conforme definido pelo serviço, será usada no lugar.

Para configurar políticas de repetição, se aplicável, abra o Designer de Aplicativo Lógico de seu aplicativo lógico e vá para **Configurações** para uma ação específica em seu aplicativo lógico. Você também pode definir políticas de repetição na seção **entradas** para uma ação específica ou um gatilho, se repetível, em sua definição de fluxo de trabalho. Esta é a sintaxe geral:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Para obter mais informações sobre a sintaxe e a seção **entradas**, consulte a [seção de política de repetição em Gatilhos e ações do fluxo de trabalho][retryPolicyMSDN]. Para obter informações sobre as limitações de políticas de repetição, consulte [Limites e configuração de Aplicativos Lógicos](../logic-apps/logic-apps-limits-and-config.md). 

### <a name="default"></a>Padrão

Quando você não define uma política de repetição na seção **retryPolicy**, seu aplicativo lógico usa a política padrão, que é uma [política de intervalo exponencial](#exponential-interval) que envia até quatro repetições em intervalos com aumento exponencial dimensionados por 7,5 segundos. O intervalo é limitado entre 5 e 45 segundos. Essa política é equivalente à política nesse exemplo de definição de fluxo de trabalho HTTP:

```json
"HTTP": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "exponential",
            "count": 4,
            "interval": "PT7.5S",
            "minimumInterval": "PT5S",
            "maximumInterval": "PT45S"
        }
    },
    "runAfter": {}
}
```

### <a name="none"></a>Nenhum

Se você definir **retryPolicy** para **nenhum**, essa política não é repetida para solicitações com falha.

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| Tipo | sim | Cadeia de caracteres | **nenhum** | 
||||| 

### <a name="fixed-interval"></a>Intervalo fixo

Se você definir **retryPolicy** para **fixo**, essa política tentará uma solicitação com falha aguardando o intervalo de tempo especificado antes de enviar a próxima solicitação.

| Nome do elemento | Obrigatório | type | DESCRIÇÃO |
| ------------ | -------- | ---- | ----------- |
| Tipo | sim | Cadeia de caracteres | **fixo** |
| count | sim | Número inteiro | O número de tentativas de repetição, que deve estar entre 1 e 90 | 
| intervalo | sim | Cadeia de caracteres | O intervalo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que deve estar entre PT5S e PT1D | 
||||| 

<a name="exponential-interval"></a>

### <a name="exponential-interval"></a>Intervalo exponencial

Se você definir **retryPolicy** para **exponencial**, essa política tentará uma solicitação com falha após um intervalo de tempo aleatório de um intervalo exponencialmente crescente. A política também garante enviar cada tentativa de repetição em um intervalo aleatório maior que **minimumInterval** e menor que **maximumInterval**. Políticas exponenciais exigem **contagem** e **intervalo**, enquanto os valores para **minimumInterval** e **maximumInterval** são opcionais. Se você deseja substituir os valores padrão PT5S e PT1D, respectivamente, você pode adicionar esses valores.

| Nome do elemento | Obrigatório | type | DESCRIÇÃO |
| ------------ | -------- | ---- | ----------- |
| Tipo | sim | Cadeia de caracteres | **exponencial** |
| count | sim | Número inteiro | O número de tentativas de repetição, que deve estar entre 1 e 90  |
| intervalo | sim | Cadeia de caracteres | O intervalo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que deve estar entre PT5S e PT1D. |
| minimumInterval | Não  | Cadeia de caracteres | O intervalo mínimo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que deve estar entre PT5S e **intervalo** |
| maximumInterval | Não  | Cadeia de caracteres | O intervalo mínimo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations), que deve estar entre **intervalo** e PT1D | 
||||| 

Esta tabela mostra como uma variável aleatória uniforme no intervalo indicado é gerada para cada repetição até e incluindo a **contagem**:

**Intervalo de variável aleatória**

| Número de Repetições | Intervalo mínimo | Intervalo máximo |
| ------------ | ---------------- | ---------------- |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| .... | | | 
|||| 

## <a name="catch-and-handle-failures-with-the-runafter-property"></a>Detectar e lidar com falhas com a propriedade RunAfter

Cada ação de aplicativo lógico declara as ações que devem terminar antes do início da ação, semelhante a como você especifica a ordem das etapas no fluxo de trabalho. Em uma definição de ação, a propriedade **runAfter** define essa ordenação e é um objeto que descreve quais status de ação e ações executam a ação.

Por padrão, todas as ações adicionadas no Designer do Aplicativo Lógico são definidas para serem executadas após a etapa anterior quando o resultado da etapa anterior for com **Êxito**. No entanto, você pode personalizar o valor **runAfter** para que as ações sejam acionadas quando as ações anteriores resultarem em **Falha**, **Ignoradas**, ou uma combinação desses valores. Por exemplo, para adicionar um item a um tópico específico do Barramento de Serviço após uma determinada ação **Insert_Row** falhar, você pode usar este exemplo de definição de **runAfter**:

```json
"Send_message": {
    "inputs": {
        "body": {
            "ContentData": "@{encodeBase64(body('Insert_Row'))}",
            "ContentType": "{ \"content-type\" : \"application/json\" }"
        },
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-westus.azure-apim.net/apim/servicebus"
            },
            "connection": {
                "name": "@parameters('$connections')['servicebus']['connectionId']"
            }
        },
        "method": "post",
        "path": "/@{encodeURIComponent('failures')}/messages"
    },
    "runAfter": {
        "Insert_Row": [
            "Failed"
        ]
    }
}
```

A propriedade **runAfter** é definida para ser executada quando o status de ação **Insert_Row** for **Falha**. Para executar a ação se o status da ação for **Com Êxito**, **Com Falha** ou **Ignorado**, use essa sintaxe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> As ações executadas e concluídas com êxito depois que uma ação anterior falhar serão marcadas como com **Êxito**. Esse comportamento significa que se você detectar todas as falhas em um fluxo de trabalho com êxito, a execução será marcada como com **Êxito**.

<a name="scopes"></a>

## <a name="evaluate-actions-with-scopes-and-their-results"></a>Avaliar ações com escopos e seus resultados

Semelhante à execução de etapas depois de ações individuais com a propriedade **runAfter**, você pode agrupar ações dentro de um [escopo](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md). Você pode usar escopos quando quiser agrupar ações logicamente, avaliar o status agregado do escopo e executar ações com base no status. Depois que todas as ações em um escopo concluem a execução, o próprio escopo também obtém seu próprio status. 

Para verificar o status de um escopo, é possível usar os mesmos critérios utilizados para verificar o status de execução de um aplicativo lógico, como com **Êxito**, com **Falha** e assim por diante. 

Por padrão, quando todas as ações do escopo são bem-sucedidas, o status do escopo é marcado como com **Êxito**. Se a ação final em um escopo resulta em **Falha** ou **Abortado**, o status do escopo fica marcado como **Falha**. 

Para capturar exceções em um escopo com **Falha** e executar ações que lidam com esses erros, você pode usar a propriedade **runAfter** nesse escopo com **Falha**. Dessa maneira, se *quaisquer* ações no escopo falharem e você usar a propriedade **runAfter** para esse escopo, será possível criar uma ação única para capturar falhas.

Para limites nos escopos, consulte [Limites e configurações](../logic-apps/logic-apps-limits-and-config.md).

### <a name="get-context-and-results-for-failures"></a>Obter o contexto e os resultados de falhas

Embora seja útil detectar falhas de um escopo, convém ter o contexto para ajudá-lo a entender exatamente quais ações falharam, além de quais erros ou códigos de status foram retornados. A função de fluxo de trabalho **@result()** fornece contexto sobre o resultado de todas as ações em um escopo.

A função **@result()** aceita um único parâmetro (nome do escopo) e retorna uma matriz de todos os resultados de ação dentro desse escopo. Esses objetos de ação incluem os mesmos atributos do objeto **@actions()**, como a hora de início, hora de término, status, entradas, IDs de correlação e saídas da ação. Para enviar o contexto de qualquer ação que falhou dentro de um escopo, você pode facilmente emparelhar uma função **@result()** com uma propriedade **runAfter**.

Para executar uma ação *para cada* ação em um escopo que tenha um resultado com **Falha** e filtrar a matriz de resultados para as ações que falharam, é possível emparelhar **@result()** com uma ação **[Filtrar Matriz](../connectors/connectors-native-query.md)** e um loop **[ForEach](../logic-apps/logic-apps-control-flow-loops.md)**. Você pode usar a matriz de resultados filtrados e executar uma ação para cada falha usando o loop **ForEach** . 

Aqui está um exemplo, seguido de uma explicação detalhada, que envia uma solicitação HTTP POST com o corpo da resposta de qualquer ação que falhou no escopo “My_Scope”:

```json
"Filter_array": {
    "inputs": {
        "from": "@result('My_Scope')",
        "where": "@equals(item()['status'], 'Failed')"
    },
    "runAfter": {
        "My_Scope": [
            "Failed"
        ]
    },
    "type": "Query"
},
"For_each": {
    "actions": {
        "Log_Exception": {
            "inputs": {
                "body": "@item()['outputs']['body']",
                "method": "POST",
                "headers": {
                    "x-failed-action-name": "@item()['name']",
                    "x-failed-tracking-id": "@item()['clientTrackingId']"
                },
                "uri": "http://requestb.in/"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "foreach": "@body('Filter_array')",
    "runAfter": {
        "Filter_array": [
            "Succeeded"
        ]
    },
    "type": "Foreach"
}
```

Aqui, está um passo a passo detalhado que descreve o que acontece nesse exemplo:

1. Para obter o resultado de todas as ações em “My_Scope”, a ação **Filtrar Matriz** filtra **@result('My_Scope')**.

2. A condição de **Filtrar Matriz** é qualquer item **@result()**que tenha um status igual a **Com Falha**. Essa condição filtra a matriz de todos os resultados de ação de “My_Scope”, para uma matriz com apenas resultados de ação com falha.

3. Execute uma ação de loop **For Each** nas saídas *matriz filtrada*. Esta etapa executa uma ação *para cada* resultado de ação com falha que foi filtrado anteriormente.

   Se uma ação única no escopo falhou, as ações no **foreach** serão executadas apenas uma vez. 
   Várias ações com falha causam uma ação por falha.

4. Envie um HTTP POST no corpo de resposta do item **foreach**, que é **@item()['outputs']['body']**. A forma do item **@result()** é igual à forma **@actions()** e pode ser analisada da mesma maneira.

5. Inclua dois cabeçalhos personalizados com o nome da ação com falha **@item()['name']** e a ID de rastreamento do cliente que executou com falha **@item()['clientTrackingId']**.

Para referência, aqui está um exemplo de um único item **@result()**, mostrando as propriedades **nome**, **corpo** e **clientTrackingId** que são analisadas no exemplo anterior. Fora de uma ação **foreach**, **@result()** retorna uma matriz desses objetos.

```json
{
    "name": "Example_Action_That_Failed",
    "inputs": {
        "uri": "https://myfailedaction.azurewebsites.net",
        "method": "POST"
    },
    "outputs": {
        "statusCode": 404,
        "headers": {
            "Date": "Thu, 11 Aug 2016 03:18:18 GMT",
            "Server": "Microsoft-IIS/8.0",
            "X-Powered-By": "ASP.NET",
            "Content-Length": "68",
            "Content-Type": "application/json"
        },
        "body": {
            "code": "ResourceNotFound",
            "message": "/docs/folder-name/resource-name does not exist"
        }
    },
    "startTime": "2016-08-11T03:18:19.7755341Z",
    "endTime": "2016-08-11T03:18:20.2598835Z",
    "trackingId": "bdd82e28-ba2c-4160-a700-e3a8f1a38e22",
    "clientTrackingId": "08587307213861835591296330354",
    "code": "NotFound",
    "status": "Failed"
}
```

Para executar diferentes padrões de tratamento de exceções, você pode usar as expressões descritas anteriormente neste artigo. Você pode optar por executar uma única ação de tratamento de exceções fora do escopo que aceita toda a matriz filtrada de falhas e remover a ação **foreach**. Adicionalmente, é possível incluir outras propriedades úteis da resposta **@result()** response, conforme descrito anteriormente.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos do Azure e telemetria

Os padrões anteriores são uma ótima maneira de identificar erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentemente da execução em si. 
O [Diagnóstico do Microsoft Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma maneira simples de enviar todos os eventos do fluxo de trabalho, incluindo todos os status de ação e execução, para uma conta de Armazenamento do Azure ou para um hub de eventos criado com Hubs de Eventos do Azure. 

Para avaliar o status de execução, você pode monitorar os logs e as métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir. Uma opção possível é transmitir todos os eventos através dos Hubs de Eventos para o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics é possível gravar consultas dinâmicas com base em quaisquer anomalias, médias ou falhas dos logs de diagnóstico. Você pode usar o Stream Analytics para enviar informações a outras fontes de dados, como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Próximas etapas

* [Veja como um cliente compila o tratamento de erros com Aplicativos Lógicos do Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md)
* [Encontrar mais exemplos e cenários dos Aplicativos Lógicos](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Saiba como criar implantações automatizadas para aplicativos lógicos](../logic-apps/logic-apps-create-deploy-template.md)
* [Compilar e implantar aplicativos lógicos no Visual Studio](logic-apps-deploy-from-vs.md)

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9