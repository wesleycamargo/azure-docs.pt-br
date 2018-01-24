---
title: "Tratamento de erros e exceções para Aplicativos Lógicos no Azure | Microsoft Docs"
description: "Padrões para tratamento de erros e exceções em Aplicativos Lógicos."
services: logic-apps
documentationcenter: .net,nodejs,java
author: derek1ee
manager: anneta
editor: 
ms.assetid: e50ab2f2-1fdc-4d2a-be40-995a6cc5a0d4
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 10/18/2016
ms.author: LADocs; deli
ms.openlocfilehash: a74c7d18306359c9152f139299de1208b5932fe5
ms.sourcegitcommit: f46cbcff710f590aebe437c6dd459452ddf0af09
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/20/2017
---
# <a name="handle-errors-and-exceptions-in-logic-apps"></a>Tratar erros e exceções em Aplicativos Lógicos

Os Aplicativos Lógicos no Azure fornecem ferramentas avançadas e padrões para ajudá-lo a garantir que suas integrações sejam robustas e resilientes a falhas. Qualquer arquitetura de integração representa o desafio de lidar adequadamente com o tempo de inatividade ou problemas de sistemas dependentes. Os Aplicativos Lógicos tornam o tratamento de erros uma experiência de alto padrão. Eles fornecem as ferramentas necessárias para agir em exceções e erros em seus fluxos de trabalho.

## <a name="retry-policies"></a>Políticas de repetição

Uma política de repetição é o tipo mais básico de exceção e de tratamento de erros. Se uma solicitação inicial atingir o tempo limite ou falhar (qualquer solicitação que resulte em uma resposta 429 ou 5xx), uma política de repetição definirá se e como a ação deverá ser repetida. 

Há quatro tipos de políticas de repetição: padrão, nenhum, intervalo fixo e intervalo exponencial. Se uma política de repetição não for fornecida na definição do fluxo de trabalho, a política padrão, conforme definido pelo serviço, será usada. 

Você poderá configurar as políticas de repetição nas *entradas* para um gatilho ou ação particular, se for nova tentativa. Da mesma forma, é possível configurar as políticas de repetição (se aplicáveis) no Designer do Aplicativo Lógico. Para configurar uma política de repetição, no Designer do Aplicativo Lógico, acesse **Configurações** para uma ação específica.

Para obter informações sobre as limitações de políticas de repetição, consulte [Limites e configuração de Aplicativos Lógicos](../logic-apps/logic-apps-limits-and-config.md). Para obter mais informações sobre a sintaxe com suporte, consulte a [seção de política de repetição em Gatilhos e ações do fluxo de trabalho][retryPolicyMSDN].

### <a name="default"></a>Padrão

Se você não definir uma política de repetição (se **retryPolicy** estiver indefinido), será usada a política padrão. A política padrão é uma política de intervalo exponencial que envia até quatro tentativas, em intervalos exponencialmente aumentados com uma escala de 7,5 segundos. O intervalo é limitado a entre 5 e 45 segundos. Essa política padrão é equivalente à política nesse exemplo de definição de fluxo de trabalho HTTP:

```json
"HTTP":
{
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
    "runAfter": {},
    "type": "Http"
}
```

### <a name="none"></a>Nenhum

Se **retryPolicy** estiver definido para **nenhum**, uma solicitação com falha não será repetida.

| Nome do elemento | Obrigatório | type | DESCRIÇÃO |
| ------------ | -------- | ---- | ----------- |
| Tipo | sim | Cadeia de caracteres | **nenhum** |

### <a name="fixed-interval"></a>Intervalo fixo

Se **retryPolicy** estiver definido para **fixo**, a política tentará uma solicitação com falha aguardando o intervalo de tempo especificado antes de enviar a próxima solicitação.

| Nome do elemento | Obrigatório | type | DESCRIÇÃO |
| ------------ | -------- | ---- | ----------- |
| Tipo | sim | Cadeia de caracteres | **fixo** |
| count | sim | Número inteiro | Número de tentativas de repetição. Deve ser entre 1 e 90. |
| intervalo | sim | Cadeia de caracteres | Intervalo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve ser entre PT5S e PT1D. |

### <a name="exponential-interval"></a>Intervalo exponencial

Se **retryPolicy** estiver definido para **exponencial**, a política tentará uma solicitação com falha após um intervalo de tempo aleatório de um intervalo exponencialmente crescente. Cada tentativa de repetição é garantida para ser enviada em um intervalo aleatório maior que **minimumInterval** e menor que **maximumInterval**. Uma variável aleatória uniforme no intervalo indicado na tabela a seguir é gerada para cada repetição e até e incluindo a **contagem**:

**Intervalo de variável aleatória**

| Número de Repetições | Intervalo mínimo | Intervalo máximo |
| ------------ |  ------------ |  ------------ |
| 1 | Max(0, **minimumInterval**) | Min(interval, **maximumInterval**) |
| 2 | Max(interval, **minimumInterval**) | Min(2 * interval, **maximumInterval**) |
| 3 | Max(2 * interval, **minimumInterval**) | Min(4 * interval, **maximumInterval**) |
| 4 | Max(4 * interval, **minimumInterval**) | Min(8 * interval, **maximumInterval**) |
| ... |

Para políticas de tipo exponencial, a **contagem** e o **intervalo** são necessários. Os valores para **minimumInterval** e **maximumInterval** são opcionais. É possível adicioná-los para substituir os valores padrão de PT5S e PT1D, respectivamente.

| Nome do elemento | Obrigatório | type | DESCRIÇÃO |
| ------------ | -------- | ---- | ----------- |
| Tipo | sim | Cadeia de caracteres | **exponencial** |
| count | sim | Número inteiro | Número de tentativas de repetição. Deve ser entre 1 e 90.  |
| intervalo | sim | Cadeia de caracteres | Intervalo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve ser entre PT5S e PT1D. |
| minimumInterval | Não  | Cadeia de caracteres | Intervalo mínimo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve estar entre PT5S e o **intervalo**. |
| maximumInterval | Não  | Cadeia de caracteres | Intervalo mínimo de repetição no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations). Deve estar entre **intervalo** e PT1D. |

## <a name="catch-failures-with-the-runafter-property"></a>Detectar falhas com a propriedade runAfter

Cada ação de aplicativo lógico declara quais ações deverão ser concluídas antes do início da ação. É semelhante à ordem das etapas no fluxo de trabalho. Na definição de ação, essa solicitação é conhecida como a propriedade **runAfter**. 

A propriedade **runAfter** é um objeto que descreve quais status de ação e ações executam a ação. Por padrão, todas as ações adicionadas usando o Designer do Aplicativo Lógico são definidas para serem executadas após a etapa anterior, se o resultado da etapa anterior for com **Êxito**. 

No entanto, é possível personalizar o valor de **runAfter** para disparar ações quando as ações anteriores tiverem um resultado **Com Falha**, **Ignorado** ou um possível conjunto desses valores. Se você quiser adicionar um item a um tópico do Barramento de Serviço do Microsoft Azure designado após uma ação específica **Insert_Row** falhar, você poderá usar a seguinte configuração **runAfter**:

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

Observe que **runAfter** está configurada para disparar se o resultado da ação **Insert_Row** for **Com Falha**. Para executar a ação se o status da ação for **Com Êxito**, **Com Falha** ou **Ignorado**, use essa sintaxe:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

> [!TIP]
> As ações executadas e concluídas com êxito depois que uma ação anterior falhar serão marcadas como com **Êxito**. Isso significa que, se você capturar todas as falhas no fluxo de trabalho, a própria execução será marcada como com **Êxito**.

## <a name="scopes-and-results-to-evaluate-actions"></a>Escopos e resultados para avaliar ações

É possível agrupar ações dentro de um [escopo](../logic-apps/logic-apps-loops-and-scopes.md), semelhante à forma de execução após as ações individuais. Um escopo age como um agrupamento lógico de ações. 

Escopos são úteis para organizar suas ações de Aplicativo Lógico e para realizar avaliações de agregação do status de um escopo. O escopo em si recebe um status depois que todas as ações em um escopo são concluídas. O status do escopo é determinado com os mesmos critérios que uma execução. Se a ação final em um branch de execução for **Com Falha** ou **Anulado**, o status será **Com Falha**.

Para disparar ações específicas para quaisquer falhas ocorridas dentro do escopo, você poderá usar **runAfter** com um escopo marcado **Com Falha**. Se *quaisquer* ações no escopo falhar, se você usar **runAfter** para um escopo, será possível criar uma ação única para capturar falhas.

### <a name="get-the-context-of-failures-with-results"></a>Obter o contexto de falhas com resultados

Embora as capturas de falhas de um escopo sejam úteis, também é possível que você queira o contexto para ajudá-lo a entender exatamente quais ações falharam e saber quais erros ou códigos de status foram retornados. A função de fluxo de trabalho **@result()** fornece contexto sobre o resultado de todas as ações em um escopo.

A função **@result()** considera um único parâmetro (nome do escopo) e retorna uma matriz de todos os resultados de ação dentro desse escopo. Esses objetos de ação incluem os mesmos atributos que o objeto **@actions()**, incluindo a hora de início da ação, a hora de término da ação, o status da ação, as entradas da ação, as IDs de correlação da ação e as saídas da ação. 

Para enviar o contexto de qualquer ação que falhou dentro de um escopo, você pode facilmente emparelhar uma função **@result()** com uma propriedade **runAfter**.

Para executar uma ação *para cada* ação em um escopo que tenha um resultado **Com Falha**, e filtrar a matriz de resultados para ações que falharam, é possível emparelhar **@result()** com uma ação [Filter_array](../connectors/connectors-native-query.md) e um loop [foreach](../logic-apps/logic-apps-loops-and-scopes.md). Com a matriz de resultados filtrada, você poderá executar uma ação para cada falha usando o loop **foreach**. 

Aqui, está um exemplo que envia uma solicitação HTTP POST com o corpo de resposta de qualquer ação que falhou no escopo My_Scope:

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

Aqui, está um passo a passo detalhado para descrever o que acontece no exemplo anterior:

1. Para obter o resultado de todas as ações em My_Scope, a ação **Filter_array** filtra **@result('My_Scope')**.

2. A condição de **Filter_array** é qualquer item **@result()**que tenha um status igual a **Com Falha**. Essa condição filtra a matriz de todos os resultados de ação de My_Scope, para uma matriz com apenas resultados de ação com falha.

3. Execute uma ação **foreach** nas saídas de *matriz filtrada*. Esta etapa executa uma ação *para cada* resultado de ação com falha que foi filtrado anteriormente.

    Se uma ação única no escopo falhou, as ações no **foreach** serão executadas apenas uma vez. Várias ações com falha causam uma ação por falha.

4. Envie um HTTP POST no corpo de resposta do item **foreach** ou **@item()['outputs']['body']**. A forma de item **@result()** é a mesma que a forma **@actions()**. É possível analisá-lo da mesma maneira.

5. Inclua dois cabeçalhos personalizados com o nome da ação com falha **@item()['name']** e a ID de rastreamento do cliente que executou com falha **@item()['clientTrackingId']**.

Para referência, aqui está um exemplo de um único item **@result()**. Ele mostra as propriedades **nome**, **corpo** e **clientTrackingId** analisadas no exemplo anterior. Fora de uma ação **foreach**, **@result()** retorna uma matriz desses objetos.

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

Para diferentes padrões de tratamento de exceções, é possível usar as expressões descritas anteriormente no artigo. Você pode optar por executar uma única ação de tratamento de exceções fora do escopo que aceita toda a matriz filtrada de falhas e remover o **foreach**. Adicionalmente, é possível incluir outras propriedades úteis da resposta **@result()** response, conforme descrito anteriormente.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos do Azure e telemetria

Os padrões descritos neste artigo fornecem ótimas maneiras de tratamento de erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentes da execução. O [Diagnóstico do Microsoft Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md) fornece uma maneira simples de enviar todos os eventos do fluxo de trabalho (incluindo todos os status de ação e execução) para uma conta de armazenamento do Azure ou para um hub de eventos em Hubs de Eventos do Azure. 

Para avaliar os status de execução, você pode monitorar logs e métricas ou publicá-los em qualquer ferramenta de monitoramento que preferir. Uma opção possível é transmitir todos os eventos através dos Hubs de Eventos para o [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). No Stream Analytics é possível gravar consultas dinâmicas com base em quaisquer anomalias, médias ou falhas dos logs de diagnóstico. Você pode usar o Stream Analytics para enviar informações a outras fontes de dados, como filas, tópicos, SQL, Azure Cosmos DB ou Power BI.

## <a name="next-steps"></a>Próximas etapas

* Veja como um cliente [compila o tratamento de erros com os Aplicativos Lógicos no Azure](../logic-apps/logic-apps-scenario-error-and-exception-handling.md).
* Encontrar mais [exemplos e cenários dos Aplicativos Lógicos](../logic-apps/logic-apps-examples-and-scenarios.md).
* Saiba como criar [implantações automatizadas para aplicativos lógicos](../logic-apps/logic-apps-create-deploy-template.md).
* Saiba como [compilar e implantar aplicativos lógicos com o Visual Studio](logic-apps-deploy-from-vs.md).

<!-- References -->
[retryPolicyMSDN]: https://docs.microsoft.com/rest/api/logic/actions-and-triggers#Anchor_9
