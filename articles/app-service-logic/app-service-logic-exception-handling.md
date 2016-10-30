<properties
   pageTitle="Tratamento de exceções dos Aplicativos Lógicos | Microsoft Azure"
   description="Conheça os padrões para tratamento de erros e de exceções com os Aplicativos Lógicos do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="10/18/2016"
   ms.author="jehollan"/>


# <a name="logic-apps-error-and-exception-handling"></a>Tratamento de exceções e de erros dos Aplicativos Lógicos

Os Aplicativos Lógicos fornecem um conjunto de ferramentas e padrões avançados para garantir que suas integrações sejam sólidas e resistentes a falhas.  Um dos desafios de qualquer arquitetura de integração é garantir que o tempo de inatividade ou problemas de sistemas dependentes sejam tratados corretamente.  Os Aplicativos lógicos transformam o tratamento de erros em uma experiência de qualidade, dando a você as ferramentas necessárias para agir em relação às exceções e aos erros em seus fluxos de trabalho.

## <a name="retry-policies"></a>Políticas de repetição

O tipo mais básico de exceção e de tratamento de erros é uma política de repetição.  Essa política define se a ação deve tentar novamente quando a solicitação inicial atingiu o tempo limite ou falhou (qualquer solicitação que resultou em uma resposta 429 ou 5xx).  Por padrão, todas as ações tentam novamente por mais quatro vezes em intervalos de 20 segundos.  Portanto, se a primeira solicitação recebeu uma resposta `500 Internal Server Error` , o mecanismo de fluxo de trabalho pausa por 20 segundos e tenta a solicitação novamente.  Se depois de todas as novas tentativas a resposta ainda for uma exceção ou falha, o fluxo de trabalho prosseguirá e marcará o status da ação como `Failed`.

Você pode configurar políticas de repetição nas **entradas** de uma ação específica.  Uma política de repetição pode ser configurada para testar intervalos de até quatro vezes em uma hora.  Os detalhes completos sobre as propriedades de entrada podem ser [encontrados no MSDN][retryPolicyMSDN].

```json
"retryPolicy" : {
      "type": "<type-of-retry-policy>",
      "interval": <retry-interval>,
      "count": <number-of-retry-attempts>
    }
```

Se você quisesse que sua ação de HTTP repetisse quatro vezes e aguardasse dez minutos entre cada tentativa, ela teria a seguinte definição:

```json
"HTTP": 
{
    "inputs": {
        "method": "GET",
        "uri": "http://myAPIendpoint/api/action",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT10M",
            "count": 4
        }
    },
    "runAfter": {},
    "type": "Http"
}
```

Para obter mais detalhes sobre a sintaxe com suporte, veja a [seção retry-policy no MSDN][retryPolicyMSDN].

## <a name="runafter-property-to-catch-failures"></a>Propriedade RunAfter para detectar falhas

Cada ação de aplicativo lógico declara quais ações precisam ser concluídas antes da ação ser iniciada.  Pense nisso como a ordem das etapas no fluxo de trabalho.  Essa classificação é conhecida como a propriedade `runAfter` na definição de ação.  É um objeto que descreve quais ações e status da ação devem executar a ação.  Por padrão, todas as ações adicionadas pelo designer são definidas para `runAfter` a etapa anterior se a etapa anterior foi `Succeeded`.  No entanto, você pode personalizar esse valor para disparar ações quando as ações anteriores são `Failed`, `Skipped` ou um possível conjunto desses valores.  Se você quisesse adicionar um item a um tópico do Barramento de Serviço designado após uma ação específica `Insert_Row` falhar, usaria a seguinte `runAfter` configuração:

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

Observe que a propriedade `runAfter` está definida para ser acionada se a ação `Insert_Row` é `Failed`.  Para executar a ação se o status da ação é `Succeeded`, `Failed` ou `Skipped` a sintaxe é:

```json
"runAfter": {
        "Insert_Row": [
            "Failed", "Succeeded", "Skipped"
        ]
    }
```

>[AZURE.TIP] Ações que são executadas após a falha de uma ação anterior e são concluídas com êxito são marcadas como `Succeeded`.  Esse comportamento significa que se você detectar todas as falhas em um fluxo de trabalho com êxito, a execução será marcada como `Succeeded`.

## <a name="scopes-and-results-to-evaluate-actions"></a>Escopos e resultados para avaliar ações

Assim como você pode executar depois de ações individuais, também pode agrupar ações em um mesmo [escopo](app-service-logic-loops-and-scopes.md) , o que funciona como um agrupamento lógico de ações.  Escopos são úteis para organizar suas ações de aplicativo lógico e para realizar avaliações de agregação do status de um escopo.  O escopo em si receberá um status após a conclusão de todas as ações em um escopo.  O status do escopo é determinado pelos mesmos critérios de uma execução: se a ação final em uma ramificação de execução é `Failed` ou `Aborted`, o status é `Failed`.

Você pode `runAfter` um escopo que foi marcado como `Failed` para disparar ações específicas no caso de falhas ocorridas dentro do escopo.  A execução após a falha de um escopo permite que você crie uma única ação para detectar falhas se *alguma* ação falhar no escopo.

### <a name="getting-the-context-of-failures-with-results"></a>Obtendo o contexto de falhas com resultados

A captura de falhas de um escopo é muito útil, mas também convém ter o contexto para compreender exatamente quais ações falharam e os erros ou códigos de status que foram retornados.  A função de fluxo de trabalho `@result()` fornece o contexto no resultado de todas as ações em um escopo.

`@result()` usa um único parâmetro, o nome do escopo, e retorna uma matriz de todos os resultados de ação nesse escopo.  Esses objetos de ação incluem os mesmos atributos do objeto `@actions()` , incluindo a hora de início, a hora de término, o status, as entradas, as IDs de correlação e as saídas da ação.  Você pode combinar facilmente uma função `@result()` com um `runAfter` para enviar o contexto de qualquer ação com falha em um escopo.

Se quiser executar uma ação *for each* em um escopo que `Failed`, você poderá combinar `@result()` com uma ação **[Matriz de Filtro](../connectors/connectors-native-query.md)** e um loop **[ForEach](app-service-logic-loops-and-scopes.md)**.  Isso permite que você filtre a matriz de resultados para ações com falha.  Você pode usar a matriz de resultados filtrados e executar uma ação para cada falha usando o loop **ForEach** .  Eis um exemplo abaixo, seguido de uma explicação detalhada.  Esse exemplo envia uma solicitação HTTP POST com o corpo de resposta de qualquer ação que falhou no escopo `My_Scope`.

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

Eis uma explicação detalhada do que está acontecendo:

1. A ação **Matriz de Filtro** para filtrar o `@result('My_Scope')` e obter o resultado de todas as ações em `My_Scope`
1. A condição da **Matriz de Filtro** é qualquer item `@result()` com status igual a `Failed`.  Isso filtra a matriz de todos os resultados de ação de `My_Scope` e deixa apenas uma matriz de resultados de ação com falha.
1. Execute uma ação **For Each** nas saídas **Matriz Filtrada**.  Isso executará uma ação *for each* com falha no resultado da ação que filtramos acima.
    - Se houver uma única ação no escopo com falha, as ações `foreach` serão executadas somente uma vez.  Muitas ações com falha causariam uma ação por falha.
1. Envie um HTTP POST no corpo da resposta do item `foreach` ou `@item()['outputs']['body']`.  A forma do item `@result()` é igual à forma `@actions()` e pode ser analisada da mesma maneira.
1. Também incluídos dois cabeçalhos personalizados com o nome da ação com falha `@item()['name']` e a ID de rastreamento do cliente que executou com falha `@item()['clientTrackingId']`.

Para referência, aqui está um exemplo de um único item `@result()` .  Você pode ver as propriedades `name`, `body` e `clientTrackingId` analisadas no exemplo acima.  Observe que fora de uma `foreach`, `@result()` retorna uma matriz desses objetos.

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
            "message": "/docs/foo/bar does not exist"
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

Você pode usar as expressões acima para executar padrões de tratamento de exceção diferentes.  Você pode optar por executar uma única ação de tratamento de exceção fora do escopo que aceita toda a matriz filtrada de falhas e remover a `foreach`.  Você também pode incluir outras propriedades úteis da resposta `@result()` mostrada acima.

## <a name="azure-diagnostics-and-telemetry"></a>Diagnósticos do Azure e telemetria

Os padrões acima são uma ótima maneira de identificar erros e exceções dentro de uma execução, mas você também pode identificar e responder a erros independentemente da execução em si.  [Diagnóstico do Azure](app-service-logic-monitor-your-logic-apps.md) fornece uma maneira simples de enviar todos os eventos de fluxo de trabalho (incluindo todos os status de execução e de ação) a uma conta de Armazenamento do Azure ou a um Hub de Eventos do Azure.  Você pode monitorar os logs e as métricas ou publicá-los na ferramenta de monitoramento que preferir, para avaliar o status de execução.  Uma opção possível é transmitir todos os eventos usando o Hub de Eventos do Azure no [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).  No Stream Analytics, você pode dar baixa em consultas nas anomalias, médias ou falhas dos logs de diagnóstico.  O Stream Analytics pode enviar a saída facilmente para outras fontes de dados, como filas, tópicos, SQL, Banco de Dados de Documentos e Power BI.

## <a name="next-steps"></a>Próximas etapas
- [Confira como um cliente criou um tratamento de erros robusto com os Aplicativos Lógicos](app-service-logic-scenario-error-and-exception-handling.md)
- [Encontrar mais exemplos e cenários dos Aplicativos Lógicos](app-service-logic-examples-and-scenarios.md)
- [Saiba como criar implantações automatizadas de aplicativos lógicos](app-service-logic-create-deploy-template.md)
- [Projetar e implantar aplicativos lógicos do Visual Studio](app-service-logic-deploy-from-vs.md)


<!-- References -->
[retryPolicyMSDN]: https://msdn.microsoft.com/library/azure/mt643939.aspx#Anchor_9


<!--HONumber=Oct16_HO2-->


