---
title: "Ações do fluxo de trabalho e gatilhos - Aplicativos Lógicos do Azure | Microsoft Docs"
description: 
services: logic-apps
author: MandiOhlinger
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/17/2016
ms.author: LADocs; mandia
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: cc41bdb12cf11e60489e104af2df4dd0720dd91b
ms.contentlocale: pt-br
ms.lasthandoff: 09/07/2017

---

# <a name="workflow-actions-and-triggers-for-azure-logic-apps"></a>Ações do fluxo de trabalho e gatilhos para os Aplicativos Lógicos do Azure

Os aplicativos lógicos são compostos por gatilhos e ações. Há seis tipos de gatilhos. Cada tipo tem um comportamento e interface diferentes. Você também pode aprender sobre outros detalhes observando os detalhes da [Linguagem de Definição do Fluxo de Trabalho](logic-apps-workflow-definition-language.md).  
  
Continue lendo para saber mais sobre os gatilhos e ações, e como você pode usá-los para criar aplicativos de lógicos para melhorar seus processos de negócios e fluxos de trabalho.  
  
### <a name="triggers"></a>Gatilhos  

Um gatilho especifica as chamadas que podem iniciar uma execução do fluxo de trabalho de seu aplicativo lógico. Aqui estão as duas formas diferentes de iniciar uma execução do fluxo de trabalho:  
  
-   Gatilho de sondagem  

-   Gatilho de envio - chamando a [API REST do Serviço do Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows)  
  
Todos os gatilhos contêm estes elementos de alto nível:  
  
```json
"<name-of-the-trigger>" : {
    "type": "<type-of-trigger>",
    "inputs": { <settings-for-the-call> },
    "recurrence": {  
        "frequency": "Second|Minute|Hour|Week|Month|Year",
        "interval": "<recurrence interval in units of frequency>"
    },
    "conditions": [ <array-of-required-conditions > ],
    "splitOn" : "<property to create runs for>",
    "operationOptions": "<operation options on the trigger>"
}
```

### <a name="trigger-types-and-their-inputs"></a>Tipos de gatilho e suas entradas  

Você pode usar estes tipos de gatilhos:
  
-   **Solicitação** \- torna o aplicativo lógico um ponto de extremidade para você chamar  
  
-   **Recorrência** \- é acionado com base em um agendamento definido  
  
-   **HTTP** \- sonda um ponto de extremidade HTTP da Web. O ponto de extremidade HTTP deve estar de acordo com um contrato de gatilho específico \- usando um padrão assíncrono 202\- ou retornando uma matriz  
  
-   **ApiConnection** \- sonda como o gatilho HTTP, no entanto, tem a vantagem das [APIs gerenciadas pela Microsoft](https://docs.microsoft.com/azure/connectors/apis-list)  
  
-   **HTTPWebhook** \- abre um ponto de extremidade, semelhante ao gatilho Manual, porém, também chama uma URL especificada para registrar e cancelar o registro  
  
-   **ApiConnectionWebhook** \- opera como o gatilho HTTPWebhook, aproveitando as APIs gerenciadas pela Microsoft       
    Cada tipo de gatilho tem um conjunto diferente de **entradas** que define seu comportamento.  
  
## <a name="request-trigger"></a>Gatilho de solicitação  

Esse gatilho serve como um ponto de extremidade que você chama através de uma Solicitação HTTP para chamar seu aplicativo lógico. Um gatilho de solicitação é semelhante a este exemplo:  
  
```json
"<name-of-the-trigger>" : {
    "type" : "request",
    "kind": "http",
    "inputs" : {
        "schema" : {
            "properties" : {
                "myInputProperty1" : { "type" : "string" },
                "myInputProperty2" : { "type" : "number" }
            },
        "required" : [ "myInputProperty1" ],
        "type" : "object"
        }
    }
} 
```

Há também uma propriedade opcional denominada **esquema**:  
  
|Nome do elemento|Obrigatório|Descrição|  
|----------------|------------|---------------|  
|schema|Não|Um esquema JSON que valida a solicitação de entrada. Útil para ajudar as etapas subsequentes do fluxo de trabalho a saberem a quais propriedades fazer referência.|

Para invocar esse ponto de extremidade, você precisa chamar a API *listCallbackUrl*. Consulte [API REST do Serviço do Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows).  
  
## <a name="recurrence-trigger"></a>Gatilho de recorrência  

Um gatilho de Recorrência é aquele executado com base em um agendamento definido. Tal gatilho pode parecer com este exemplo:  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

Como você pode ver, é uma maneira simples de executar um fluxo de trabalho.  
  
|Nome do elemento|Obrigatório|Descrição|  
|----------------|------------|---------------|  
|frequência|Sim|Com que frequência o gatilho é executado. Use apenas um desses valores possíveis: segundo, minuto, hora, dia, semana, mês ou ano|  
|intervalo|Sim|Intervalo na frequência determinada para a recorrência|  
|startTime|Não|Se um startTime for fornecido sem uma diferença UTC, o fuso horário será usado.|  
|timeZone|não|Se um startTime for fornecido sem uma diferença UTC, o fuso horário será usado.|  
  
Você também pode agendar um gatilho para iniciar a execução em algum momento no futuro. Por exemplo, se você quiser iniciar um relatório semanal toda segunda-feira, poderá agendar o aplicativo lógico para iniciar toda segunda-feira criando o gatilho a seguir:  

```json
"dailyReport" : {
    "type": "recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime" : "2015-06-22T00:00:00Z"
    }
}
```

## <a name="http-trigger"></a>Gatilho HTTP  

Os gatilhos HTTP sondam um ponto de extremidade especificado e verifica a resposta para determinar se o fluxo de trabalho deve ser executado. O objeto inputs usa o conjunto de parâmetros necessários para construir uma chamada HTTP:  
  
|Nome do elemento|Obrigatório|Descrição|Tipo|  
|----------------|------------|---------------|--------|  
|estático|sim|Pode ser um dos seguintes métodos HTTP: GET, POST, PUT, DELETE, PATCH ou HEAD|Cadeia de caracteres|  
|uri|sim|O ponto de extremidade http ou https chamado. Máximo de 2 quilobytes.|Cadeia de caracteres|  
|consultas|Não|Um objeto que representa os parâmetros de consulta para adicionar à URL. Por exemplo, `"queries" : { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL.|Objeto|  
|headers|Não|Um objeto que representa cada um dos cabeçalhos enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|Objeto|  
|body|Não|Um objeto que representa o conteúdo enviado para o ponto de extremidade.|Objeto|  
|retryPolicy|Não|Um objeto que permite personalizar o comportamento de repetição para os erros 4xx ou 5xx.|Objeto|  
|autenticação|Não|Representa o método com o qual a solicitação deve ser autenticada. Para obter detalhes sobre esse objeto, consulte [Autenticação de Saída do Agendador](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). Além do Agendador, há mais uma propriedade com suporte: `authority` por padrão, esse valor é `https://login.windows.net` quando não especificado, mas você pode usar um público diferente como `https://login.windows\-ppe.net`|Objeto|  
  
O gatilho HTTP requer a API HTTP em conformidade com um padrão específico para funcionar bem com seu aplicativo lógico. Ele reconhece as seguintes propriedades:  
  
|Resposta|Obrigatório|Descrição|  
|------------|------------|---------------|  
|Código de status|Sim|Código de status 200 \(OK\) para fazer uma execução. Qualquer outro código de status não provoca uma execução.|  
|Repetir\-após o cabeçalho|Não|Número de segundos até o aplicativo lógico sondar o ponto de extremidade novamente.|  
|Cabeçalho do local|Não|A URL a chamar no próximo intervalo de sondagem. Se não for especificada, a URL original será usada.|  
  
Aqui estão alguns exemplos de comportamentos diferentes para diferentes tipos de solicitações:  
  
|Código de resposta|Repetir\-Após|Comportamento|  
|-----------------|----------------|------------|  
|200|\(nenhum\)|Execute o fluxo de trabalho e verifique novamente se há mais conteúdo após a recorrência definida.|  
|200|10|Execute o fluxo de trabalho e verifique novamente para obter mais conteúdo em 10 segundos.|  
|202|60|Não dispara o fluxo de trabalho. A próxima tentativa ocorre em um minuto, sujeito à recorrência definida. Se a recorrência definida for inferior a um minuto, o cabeçalho retry-after terá precedência. Caso contrário, a recorrência definida será seguida.|  
|400|\(nenhum\)|Solicitação inválida, não execute o fluxo de trabalho. Se não houver nenhuma **Política de Repetição** definida, a política padrão será usada. Após a quantidade de novas tentativas, o gatilho buscará novamente o conteúdo após a recorrência definida.|  
|500|\(nenhum\)|Erro do servidor, não execute o fluxo de trabalho.  Se não houver nenhuma **Política de Repetição** definida, a política padrão será usada. Após a quantidade de novas tentativas, o gatilho buscará novamente o conteúdo após a recorrência definida.|  
  
As saídas de um gatilho HTTP são parecidas com este exemplo:  
  
|Nome do elemento|Descrição|Tipo|  
|----------------|---------------|--------|  
|headers|Os cabeçalhos da resposta http.|Objeto|  
|body|O corpo da resposta http.|Objeto|  
  
## <a name="api-connection-trigger"></a>Gatilho de conexão da API  

O gatilho de conexão da API é semelhante ao gatilho HTTP em sua funcionalidade básica. No entanto, os parâmetros para identificar a ação são diferentes. Aqui está um exemplo:  
  
```json
"dailyReport" : {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            },
        }
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "awesomest"
    }
}
```

|Nome do elemento|Obrigatório|Tipo|Descrição|  
|----------------|------------|--------|---------------|  
|host|Sim||Gateway hospedado pelo ApiApp e id.|  
|estático|Sim|Cadeia de caracteres|Pode ser um dos seguintes métodos HTTP: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** ou **HEAD**|  
|consultas|Não|Objeto|Representa os parâmetros de consulta a ser adicionados à URL. Por exemplo, `"queries" : { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL.|  
|headers|Não|Objeto|Representa cada um dos cabeçalhos enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Não|Objeto|Representa o conteúdo enviado para o ponto de extremidade.|  
|retryPolicy|Não|Objeto|Permite personalizar o comportamento de repetição para os erros 4xx ou 5xx.|  
|autenticação|Não|Objeto|Representa o método com o qual a solicitação deve ser autenticada. Para obter detalhes sobre esse objeto, consulte [Autenticação de Saída do Agendador](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication)|  
  
As propriedades de host são:  
  
|Nome do elemento|Obrigatório|Descrição|  
|----------------|------------|---------------|  
|api runtimeUrl|Sim|Ponto de extremidade da API gerenciada.|  
|nome da conexão||Deve ser uma referência a um parâmetro denominado `$connection` e é o nome da conexão da API gerenciada que o fluxo de trabalho usa.|
  
As saídas de um gatilho de conexão da API são:
  
|Nome do elemento|Tipo|Descrição|  
|----------------|--------|---------------|  
|headers|Objeto|Os cabeçalhos da resposta http.|  
|body|Objeto|O corpo da resposta http.|  
  
## <a name="httpwebhook-trigger"></a>Gatilho HTTPWebhook  

O gatilho HTTPWebhook abre um ponto de extremidade, semelhante ao gatilho manual, mas também chama uma URL especificada para registrar e cancelar o registro. Aqui está um exemplo de como um gatilho HTTPWebhook pode ser:  

```json
"myappspottrigger": {
    "type": "httpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": { },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": { },
            "retryPolicy": { }
        },
        "unsubscribe": {
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "method": "POST",
            "authentication": { }
        }
    },
    "conditions": [ ]
    }
```

Muitas dessas seções são opcionais e o comportamento do Webhook depende de quais seções são fornecidas ou omitidas.  
As propriedades de um Webhook são as seguintes:  
  
|Nome do elemento|Obrigatório|Descrição|  
|----------------|------------|---------------|  
|assinar|Não|A solicitação de saída que é chamada quando o gatilho é criado e executa o registro inicial.|  
|cancelar assinatura|Não|A solicitação de saída quando o gatilho é excluído.|  
  
-   **Assinar** é a chamada de saída feita para começar a ouvir os eventos. Essa chamada inicia com o mesmo conjunto de parâmetros das ações HTTP normais. Essa chamada de saída é feita sempre que o fluxo de trabalho muda de alguma forma, por exemplo, sempre que as credenciais são distribuídas ou os parâmetros de entrada do gatilho mudam.
  
    Para oferecer suporte a essa chamada, há uma nova função: `@listCallbackUrl()`. Esta função retorna uma URL exclusiva para esse gatilho específico neste fluxo de trabalho. Representa o identificador exclusivo dos pontos de extremidade que usam o Serviço REST.  
  
-   **Cancelar assinatura** é chamado quando uma operação apresenta o gatilho como inválido, incluindo:  
  
    -   excluir ou desabilitar o gatilho  
  
    -   excluir ou desabilitar o fluxo de trabalho  
  
    -   excluir ou desabilitar a assinatura  
  
    O aplicativo lógico chama automaticamente a ação de cancelamento da assinatura. Os parâmetros dessa função são os mesmos do gatilho HTTP.  
  
    As saídas do gatilho HTTPWebhook são o conteúdo da solicitação de entrada:  
  
|Nome do elemento|Tipo|Descrição|  
|-----------------|--------|---------------|  
|headers|Objeto|Os cabeçalhos da solicitação http.|  
|body|Objeto|O corpo da solicitação http.|  

Os limites em uma ação de webhook podem ser especificados da mesma maneira como os [Limites Assíncronos do HTTP](#asynchronous-limits).
  

## <a name="conditions"></a>Condições  

Para qualquer gatilho, você pode usar uma ou mais condições para determinar se o fluxo de trabalho deve ser executado ou não. Por exemplo:  

```json
"dailyReport" : {
    "type": "recurrence",
    "conditions": [ {
        "expression": "@parameters('sendReports')"
    } ],
    "recurrence": {
        "frequency": "Day",
        "interval": "1"
    }
}
```

Neste caso, o relatório somente dispara enquanto o parâmetro `sendReports` do fluxo de trabalho está definido para true. Por fim, as condições podem referenciar o código de status do gatilho. Por exemplo, você poderia iniciar um fluxo de trabalho somente quando seu site retornasse um código de status 500, como a seguir:
  
```  
"conditions": [  
        {  
          "expression": "@equals(triggers().code, 'InternalServerError')"  
        }  
      ]  
```  
  
> [!NOTE]  
> Quando qualquer expressão referencia o código de status do gatilho \(de alguma forma\), o comportamento padrão \(dispara apenas quando 200 \(OK\)\) é substituído. Por exemplo, se você quiser disparar no código de status 200 e no código de status 201, precisará incluir: `@or(equals(triggers().code, 200),equals(triggers().code,201))` como sua condição.  
  
## <a name="start-multiple-runs-for-a-request"></a>Iniciar várias execuções para uma solicitação

Para iniciar várias execuções para uma única solicitação, `splitOn` é útil, por exemplo, quando você deseja sondar um ponto de extremidade que pode ter vários novos itens entre os intervalos de sondagem.
  
Com `splitOn`, você especifica a propriedade no conteúdo de resposta que contém a matriz de itens, sendo que cada um você deseja usar para iniciar uma execução do gatilho. Por exemplo, imagine que você tenha uma API que retorna a seguinte resposta:  
  
```json
{
    "Status" : "success",
    "Rows" : [
        {  
            "id" : 938109380,
            "name" : "mycoolrow"
        },
        {
            "id" : 938109381,
            "name" : "another row"
        }
    ]
}
```
  
Seu aplicativo lógico precisa apenas do conteúdo Linhas para que você possa construir o gatilho como neste exemplo:  
  
```json
"mysplitter" : {
    "type" : "http",
    "recurrence": {
        "frequency": "Minute",
        "interval": "1"
    },
    "intputs" : {
        "uri" : "https://mydomain.com/myAPI",
        "method" : "GET"
    },
    "splitOn" : "@triggerBody()?.Rows"
}
```
  
Em seguida, na definição do fluxo de trabalho, `@triggerBody().name` retorna `mycoolrow` para a primeira execução e `another row` para a segunda execução. As saídas do gatilho ficam como neste exemplo:  
  
```json
{
    "body" : {
        "id" : 938109381,
        "name" : "another row"
    }
}
```

Portanto, se você usar `SplitOn`, não será possível obter as propriedades que estão fora da matriz. Neste caso, o campo `Status`.  
  
> [!NOTE]  
> Neste exemplo, usamos o operador `?` para evitar uma falha caso a propriedade `Rows` não esteja presente. 
  
## <a name="single-run-instance"></a>Instância de execução única

Você pode configurar gatilhos que tenham uma propriedade de recorrência para disparar apenas se todas as execuções ativas concluíram. Se uma recorrência agendada ocorrer enquanto há uma execução em andamento, o gatilho irá ignorar e aguardar até o próximo intervalo de recorrência agendado para verificar novamente.

Você pode definir essa configuração com as opções de operação:

```json
"triggers": {
    "mytrigger": {
        "type": "http",
        "inputs": { ... },
        "recurrence": { ... },
        "operationOptions": "singleInstance"
    }
}
```

## <a name="types-and-inputs"></a>Tipos e entradas  

Há muitos tipos de ações, cada um com um comportamento exclusivo. As ações de coleção podem conter muitas outras ações em si.

### <a name="standard-actions"></a>Ações padrão  

-   **HTTP** esta ação chama um ponto de extremidade HTTP da Web.  
  
-   **ApiConnection** \- esta ação se comporta como a ação HTTP, mas usa as APIs gerenciadas pela Microsoft.  
  
-   **ApiConnectionWebhook** \- como i HTTPWebhook, mas usa as APIs gerenciadas pela Microsoft.  
  
-   **Resposta** \- esta ação define uma resposta para uma chamada de entrada.  
  
-   **Aguardar** \- esta ação simples aguarda um período fixo de tempo ou até uma hora específica.  
  
-   **Fluxo de trabalho** \- esta ação representa um fluxo de trabalho aninhado.  

-   **Função** \- essa ação representa uma função do Azure.

### <a name="collection-actions"></a>Ações de coleção

-   **Escopo** \- esta ação é um agrupamento lógico de outras ações.

-   **Condição** \- esta ação avalia uma expressão e executa ao desfio do resultado correspondente.

-   **ForEach** \- esta ação de loop itera uma matriz e executa as ações internas de cada item.

-   **Até** \- esta ação de loop executa as ações internas até uma condição ser verdadeira.
  
Cada tipo de ação tem um conjunto diferente de **entradas** que definem o comportamento de uma ação.  
  
## <a name="http-action"></a>Ação HTTP  

As ações HTTP chamam um ponto de extremidade especificado e verifica a resposta para determinar se o fluxo de trabalho deve ser executado. O objeto **inputs** usa o conjunto de parâmetros necessários para construir a chamada HTTP:  
  
|Nome do elemento|Obrigatório|Tipo|Descrição|  
|----------------|------------|--------|---------------|  
|estático|Sim|Cadeia de caracteres|Pode ser um dos seguintes métodos HTTP: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** ou **HEAD**|  
|uri|Sim|string|O ponto de extremidade http ou https chamado. O comprimento máximo é 2 quilobytes.|  
|consultas|Não|Objeto|Representa os parâmetros de consulta a ser adicionados à URL. Por exemplo, `"queries" : { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL.|  
|headers|Não|Objeto|Representa cada um dos cabeçalhos enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Não|Objeto|Representa o conteúdo enviado para o ponto de extremidade.|  
|retryPolicy|Não|Objeto|Permite personalizar o comportamento de repetição para os erros 4xx ou 5xx.|  
|operationsOptions|Não|string|Define o conjunto de comportamentos especiais a substituir.|  
|autenticação|Não|Objeto|Representa o método com o qual a solicitação deve ser autenticada. Para obter detalhes sobre esse objeto, consulte [Autenticação de Saída do Agendador](https://docs.microsoft.com/azure/scheduler/scheduler-outbound-authentication). Além do agendador, há mais uma propriedade com suporte: `authority`. Por padrão, isto é `https://login.windows.net` quando não especificado, mas você pode usar um público diferente como`https://login.windows\-ppe.net`|  
  
As ações HTTP \(e as ações de Conexão da API\) têm suporte para as políticas de repetição. Uma política de repetição se aplica às falhas intermitentes, caracterizadas como os códigos de status HTTP 408, 429 e 5xx, além de quaisquer exceções de conectividade. Essa política é descrita usando o objeto *retryPolicy* definido como mostrado aqui:
  
```json
"retryPolicy" : {
    "type": "<type-of-retry-policy>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```
  
O intervalo de repetição é especificado no formato ISO 8601. Esse intervalo tem um valor padrão e mínimo de 20 segundos, enquanto o valor máximo é de uma hora. A contagem de repetição padrão e máxima é de quatro horas. Se a definição da política de repetição não for especificada, uma estratégia `fixed` será usada com valores de contagem e intervalo de repetição padrão. Para desativar a política de repetição, defina seu tipo para `None`.  
  
Por exemplo, a seguinte ação tentará buscar as notícias mais recentes duas vezes se houver falhas intermitentes, com um total de três execuções, com um atraso de 30 segundos entre cada tentativa:  
  
```json
"latestNews" : {
    "type": "http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy" : {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```
### <a name="asynchronous-patterns"></a>Padrões assíncronos

Por padrão, todas as ações baseadas no HTTP suportam o padrão de operação assíncrona padrão. Portanto, se o servidor remoto indicar que a solicitação é aceita para o processamento, com uma resposta 202 \(Aceito\), o mecanismo dos Aplicativos Lógicos continuará sondando a URL especificada no cabeçalho de local da resposta até atingir um estado terminal de \(resposta\-não 202\).  
  
Para desativar o comportamento assíncrono descrito anteriormente, defina uma opção `DisableAsyncPattern` nas entradas de ação. Neste caso, a saída da ação baseia-se na resposta 202 inicial do servidor.  
  
```json
"invokeLongRunningOperation" : {
    "type": "http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

#### <a name="asynchronous-limits"></a>Limites Assíncronos

Um padrão assíncrono pode ser limitado em sua duração para um intervalo de tempo específico.  Se o intervalo de tempo transcorrer sem atingir um estado terminal, o status da ação será marcado como `Cancelled`, com um código `ActionTimedOut`.  O tempo limite é especificado no formato ISO 8601.  Os limites podem ser especificados com a seguinte sintaxe:

``` json
"<action-name>": {
    "type": "workflow|webhook|http|apiconnectionwebhook|apiconnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="api-connection"></a>Conexão da API  

A Conexão da API é uma ação que se refere a um conector gerenciado pela Microsoft.
Esta ação requer uma referência para uma conexão válida, e informações sobre a API e os parâmetros necessários.

|Nome do elemento|Obrigatório|Tipo|Descrição|  
|----------------|------------|--------|---------------|  
|host|Sim|Objeto|Representa as informações do conector, como runtimeUrl e uma referência para o objeto connection|
|estático|Sim|Cadeia de caracteres|Pode ser um dos seguintes métodos HTTP: **GET**, **POST**, **PUT**, **DELETE**, **PATCH** ou **HEAD**|  
|path|Sim|Cadeia de caracteres|O caminho da operação da API.|  
|consultas|Não|Objeto|Representa os parâmetros de consulta a ser adicionados à URL. Por exemplo, `"queries" : { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL.|  
|headers|Não|Objeto|Representa cada um dos cabeçalhos enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Não|Objeto|Representa o conteúdo enviado para o ponto de extremidade.|  
|retryPolicy|Não|Objeto|Permite personalizar o comportamento de repetição para os erros 4xx ou 5xx.|  
|operationsOptions|Não|string|Define o conjunto de comportamentos especiais a substituir.|  

```json
"Send_Email": {
    "type": "apiconnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "post",
        "body": {
            "Subject": "New Tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
    }
```

## <a name="api-connection-webhook-action"></a>Ação do webhook de Conexão da API

```json
"Send_approval_email": {
    "type": "apiconnectionwebhook",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "body": {
            "Message": {
                "Subject": "Approval Request",
                "Options": "Approve, Reject",
                "Importance": "Normal",
                "To": "me@email.com"
            }
        },
        "path": "/approvalmail",
        "authentication": "@parameters('$authentication')"
    },
    "runAfter": {}
}
```

Os limites em uma ação de webhook podem ser especificados da mesma maneira como os [Limites Assíncronos do HTTP](#asynchronous-limits).
  
## <a name="response-action"></a>Ação de resposta  

Este tipo de ação tem o conteúdo inteiro de resposta de uma solicitação HTTP e inclui um statusCode, corpo e cabeçalhos:  
  
```json
"myresponse" : {
    "type" : "response",
    "inputs" : {
        "statusCode" : 200,
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        },
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        }
    },
    "runAfter": {}
}
```
  
A ação de resposta tem restrições especiais que não se aplicam a outras ações. Especificamente:  
  
-   As ações de resposta não podem ser paralelas em uma definição porque é necessária uma resposta determinista para a solicitação de entrada.  
  
-   Se uma ação de resposta for alcançada depois da solicitação de entrada receber uma resposta, a ação será considerada falha com \(conflito\) e como resultado, a execução será `Failed`.  
  
-   Um fluxo de trabalho com ações de Resposta não pode ter um `splitOn` em seu gatilho porque uma chamada provoca muitas execuções. Como resultado, isso deve ser validado quando o fluxo é PUT e causa uma Solicitação Incorreta.  
  
## <a name="wait-action"></a>Ação para aguardar  

A ação `wait` suspende a execução do fluxo de trabalho no intervalo especificado. Por exemplo, para esperar 15 minutos, você pode usar este trecho de código:  
  
```json
"waitForFifteenMinutes" : {
    "type": "wait",
    "inputs": {
        "interval": {
            "unit" : "minute",
            "count" : 15
        }
    }
}
```  
  
Como alternativa, para esperar até um momento específico, você poderá usar este exemplo:  
  
```json
"waitUntilOctober" : {
    "type": "wait",
    "inputs": {
        "until": {
            "timestamp" : "2016-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> A duração de espera pode ser especificada usando o objeto **interval** ou o objeto **until**, mas não ambos.  
  
|Nome|Obrigatório|Tipo|Descrição|  
|--------|------------|--------|---------------|  
|intervalo|Não|Objeto|A duração de espera com base na quantidade de tempo.|  
|unidade do intervalo|Sim|Cadeia de caracteres|Um destes intervalos: segundo, minuto, hora, dia, semana, mês, ano.|  
|contagem do intervalo|Sim|Cadeia de caracteres|Duração com base na unidade interna determinada.|  
|until|Não|Objeto|A duração de espera com base em um ponto no tempo.|  
|until carimbo data/hora|Sim|Cadeia de caracteres|Cadeia de caracteres&#124;O ponto no tempo em UTC quando a espera expira.|  

## <a name="query-action"></a>Ação de consulta

A ação `query` permite filtrar uma matriz com base em uma condição. Por exemplo, para selecionar números maiores que 2, você poderá usar:

```json
"FilterNumbers" : {
    "type": "query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

A saída da ação `query` é uma matriz com elementos da matriz de entrada que atende a condição.

> [!NOTE]
> Se nenhum valor atender a condição `where`, o resultado será uma matriz vazia.

|Nome|Obrigatório|Tipo|Descrição|
|--------|------------|--------|---------------|
|Da|Sim|Matriz|A matriz de origem.|
|onde|Sim|Cadeia de caracteres|A condição a ser aplicada em cada elemento da matriz de origem.|

## <a name="select-action"></a>Ação selecionar

A ação `select` permite projetar cada elemento de uma matriz em um novo valor.
Por exemplo, para converter uma matriz de números em uma matriz de objetos, você pode usar:

```json
"SelectNumbers" : {
    "type": "select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

A saída da ação `select` é uma matriz que tem a mesma cardinalidade que a matriz de entrada, com cada elemento transformado conforme definido pela propriedade `select`. Se a entrada for uma matriz vazia, a saída também será uma matriz vazia.

|Nome|Obrigatório|Tipo|Descrição|
|--------|------------|--------|---------------|
|Da|Sim|Matriz|A matriz de origem.|
|selecionar|Sim|Qualquer|A projeção a ser aplicada em cada elemento da matriz de origem.|

## <a name="terminate-action"></a>Ação para finalizar

A ação Finalizar para a execução do fluxo de trabalho, anulando quaisquer ações em trânsito e ignorando as ações restantes. Por exemplo, para encerrar uma execução com um status **Falha**, você poderá usar o trecho a seguir:

```json
"HandleUnexpectedResponse" : {
    "type": "terminate",
    "inputs": {
        "runStatus" : "failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response.",
        }
    }
}
```

> [!NOTE]
> As ações já concluídas não são afetadas pela ação para finalizar.

|Nome|Obrigatório|Tipo|Descrição|
|--------|------------|--------|---------------|
|runStatus|Sim|Cadeia de caracteres|O status da execução de destino. **Falha** ou **Cancelado**.|
|runError|Não|Objeto|Os detalhes do erro. Só há suporte quando **runStatus** está definido para **Falha**.|
|código runError|Não|string|O código de erro da execução.|
|mensagem runError|Não|Cadeia de caracteres|A mensagem de erro da execução.|

## <a name="compose-action"></a>Ação para compor

A ação Compor permite construir um objeto arbitrário. A saída da ação para compor é o resultado de avaliar suas entradas. Por exemplo, você pode usar a ação para compor e mesclar as saídas de várias ações:

```json
"composeUserRecord" : {
    "type": "compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
        }
    }
}
```

> [!NOTE]
> A ação **Compor** pode ser usada para construir qualquer saída, inclusive objetos, matrizes e qualquer outro tipo com suporte nativo dos aplicativos lógicos, como XML e binários.

## <a name="table-action"></a>Ação tabela

A `table` permite converter uma matriz de itens em uma tabela **CSV** ou **HTML**.

Suponha que @triggerBody() seja

```json
[{
  "id": 0,
  "name": "apples"
},{
  "id": 1, 
  "name": "oranges"
}]
```

E permita que a ação a seja definida como

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html"
    }
}
```

Isso produzirá

<table><thead><tr><th>ID</th><th>name</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>"

Para personalizar a tabela, você pode especificar explicitamente as colunas. Por exemplo:

```json
"ConvertToTable" : {
    "type": "table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [{
          "header": "produce id",
          "value": "@item().id"
        },{
          "header": "description",
          "value": "@concat('fresh ', item().name)"
        }]
    }
}
```

Isso produzirá

<table><thead><tr><th>produce id</th><th>description</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>"

Se o valor da propriedade `from` for uma matriz vazia, a saída será uma tabela vazia.

|Nome|Obrigatório|Tipo|Descrição|
|--------|------------|--------|---------------|
|Da|Sim|Matriz|A matriz de origem.|
|formato|Sim|Cadeia de caracteres|O formato, **CSV** ou **HTML**.|
|colunas|Não|Matriz|As colunas. Permite substituir a forma padrão da tabela.|
|cabeçalho de coluna|Não|Cadeia de caracteres|O cabeçalho da coluna.|
|valor da coluna|Sim|Cadeia de caracteres|O valor da coluna.|

## <a name="workflow-action"></a>Ação do fluxo de trabalho   

|Nome|Obrigatório|Tipo|Descrição|  
|--------|------------|--------|---------------|  
|id do host|Sim|Cadeia de caracteres|A ID de recurso do fluxo de trabalho que você deseja chamar.|  
|triggerName do host|Sim|Cadeia de caracteres|O nome do gatilho que você deseja chamar.|  
|consultas|Não|Objeto|Representa os parâmetros de consulta a ser adicionados à URL. Por exemplo, `"queries" : { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL.|  
|headers|Não|Objeto|Representa cada um dos cabeçalhos enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us",  "Content-Type": "application/json" }`|  
|body|Não|Objeto|Representa o conteúdo enviado para o ponto de extremidade.|  
  
```json
"mynestedwf" : {
    "type" : "workflow",
    "inputs" : {
        "host" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Logic/mywf001",
            "triggerName " : "mytrigger001"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()",
            "Content-type" : "application/json"
        },
        "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
    }
```
  
Uma verificação de acesso é feita no fluxo de trabalho \(mais especificamente, o gatilho\), que significa que você precisa ter acesso ao fluxo de trabalho.  
  
As saídas da ação `workflow` baseiam-se no que foi definido na ação `response` no fluxo de trabalho filho. Se você não definiu nenhuma ação `response`, então, as saídas estarão vazias.  

## <a name="function-action"></a>Ação de função   

|Nome|Obrigatório|Tipo|Descrição|  
|--------|------------|--------|---------------|  
|Id de Função|Sim|string|A ID de recurso da função que você deseja invocar.|  
|estático|Não|Cadeia de caracteres|O método HTTP usado para invocar a função. Por padrão, ele é `POST` quando não especificada.|  
|consultas|Não|Objeto|Representa os parâmetros de consulta a ser adicionados à URL. Por exemplo, `"queries" : { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL.|  
|headers|Não|Objeto|Representa cada um dos cabeçalhos enviados para a solicitação. Por exemplo, para definir o idioma e o tipo em uma solicitação: `"headers" : { "Accept-Language": "en-us" }`.|  
|body|Não|Objeto|Representa o conteúdo enviado para o ponto de extremidade.|  

```json
"myfunc" : {
    "type" : "Function",
    "inputs" : {
        "function" : {
            "id" : "/subscriptions/xxxxyyyyzzz/resourceGroups/rg001/providers/Microsoft.Web/sites/myfuncapp/functions/myfunc"
        },
        "queries" : {
            "extrafield" : "specialValue"
        },  
        "headers" : {
            "x-ms-date" : "@utcnow()"
        },
        "method" : "POST",
    "body" : {
            "contentFieldOne" : "value100",
            "anotherField" : 10.001
        }
    },
    "runAfter": {}
}
```

Quando você salvar o aplicativo lógico, podemos realizar algumas verificações na função referenciada:
-   Você precisa ter acesso à função.
-   Apenas o gatilho HTTP padrão ou gatilho de webhook JSON genérico é permitido.
-   Ele não deve ter nenhuma rota definida.
-   Apenas "função" e o nível de autorização "anônimo" é permitido.

A URL do gatilho é recuperada, armazenado em cache e usada em tempo de execução. Portanto, se qualquer operação invalida a URL armazenada em cache, a ação falhará em tempo de execução. Para solucionar esse problema, salve o aplicativo lógico novamente, que fará com que o aplicativo lógico para recuperar e armazenar em cache o URL do gatilho novamente.

## <a name="collection-actions-scopes-and-loops"></a>Ações da coleção (escopos e loops)

Alguns tipos de ação podem conter ações em si. As ações de referência em uma coleção podem ser referenciadas diretamente de fora da coleção. Se você definiu `http` em um escopo, `@body('http')` ainda será válido em qualquer lugar em um fluxo de trabalho. As ações em uma coleção podem executar com `runAfter` apenas as outras ações na mesma coleção.

## <a name="scope-action"></a>Ação de escopo

A ação `scope` permite agrupar logicamente as ações em um fluxo de trabalho.

|Nome|Obrigatório|Tipo|Descrição|  
|--------|------------|--------|---------------|  
|Ações|Sim|Objeto|As ações internas a executar no escopo|

```json
{
    "myScope": {
        "type": "scope",
        "actions": {
            "call_bing": {
                "type": "http",
                "inputs": {
                    "url": "http://www.bing.com"
                }
            }
        }
    }
}
```

## <a name="foreach-action"></a>Ação ForEach

Esta ação de loop itera uma matriz e executa as ações internas de cada item. Por padrão, o loop foreach é executado em paralelo (20 execuções em paralelo por vez). Você pode definir regras de execução usando o parâmetro `operationOptions`.

|Nome|Obrigatório|Tipo|Descrição|  
|--------|------------|--------|---------------|  
|Ações|Sim|Objeto|As ações internas a executar no loop|
|foreach|Sim|string|A matriz para iterar|
|operationOptions|não|string|Quaisquer opções de operação para o comportamento. Atualmente, tem suporte apenas para `sequential` para executar as iterações sequencialmente (o comportamento padrão é paralelo)|

```json
"forEach_email": {
    "type": "foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                }
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "runAfter":{
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Ação Until

Esta ação de loop executa as ações internas até uma condição ser verdadeira.

|Nome|Obrigatório|Tipo|Descrição|  
|--------|------------|--------|---------------|  
|Ações|Sim|Objeto|As ações internas a executar no loop|
|expressão|Sim|string|A expressão a avaliar após cada iteração|
|limite|sim|Objeto|Os limites do loop - pelo menos, um limite deve ser definido|
|count|não|int|O limite para o número de iterações que podem ser executadas|
|Tempo limite|não|string|O tempo limite de quanto tempo deve executar um loop.  Formato ISO 8601|


```json
 "Until_succeeded": {
    "actions": {
        "Http": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {},
    "type": "Until"
}
```

## <a name="conditions---if-action"></a>Condições - Ação If

A ação `If` permite que você avalie uma condição e execute um desvio caso a expressão seja avaliada como `true`.

|Nome|Obrigatório|Tipo|Descrição|  
|--------|------------|--------|---------------|  
|Ações|Sim|Objeto|As ações internas a executar quando a expressão é avaliada como `true`|
|expressão|Sim|string|A expressão a avaliar|
|else|não|Objeto|As ações internas a executar quando a expressão é avaliada como `false`|
  
```json
"My_condition": {
    "actions": {
        "If_true": {
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {},
            "type": "Http"
        }
    },
    "else": {
        "actions": {
            "if_false": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://myurl"
                },
                "runAfter": {},
                "type": "Http"
            }
        }
    },
    "expression": "@equals(triggerBody(), json(true))",
    "runAfter": {},
    "type": "If"
}
```  
  
A tabela a seguir mostra exemplos de como as condições podem usar expressões em uma ação:  
  
|Valor JSON|Result|  
|--------------|----------|  
|`"expression": "@parameters('hasSpecialAction')"`|Qualquer valor avaliado como verdadeiro faz com que essa condição passe. Apenas as expressões boolianas têm suporte. Para converter outros tipos em booliano, use as funções `empty`, `equals`.|  
|`"expression": "@greater(actions('act1').output.value, parameters('threshold'))"`|Há suporte para as funções de comparação. Por exemplo aqui, a ação é executada somente quando a saída act1 é maior que o limite.|  
|`"expression": "@or(greater(actions('act1').output.value, parameters('threshold')), less(actions('act1').output.value, 100))"`|Também há suporte para as funções lógicas para criar expressões boolianas aninhadas. Neste caso, a ação é executada quando a saída act1 está acima do limite ou abaixo de 100.|  
|`"expression": "@equals(length(actions('act1').outputs.errors), 0))"`|Você pode usar as funções de matriz para verificar se uma matriz tem algum item. Neste caso, a ação é executada quando a matriz de erros está vazia.| 
|`"expression": "parameters('hasSpecialAction')"`|Erro - não é uma condição válida porque @ é necessário para as condições.|  
  
Se uma condição for avaliada com êxito, a condição está marcada como `Succeeded`. As ações no objeto `actions` ou `else` são avaliadas como `Succeeded` quando executadas e bem-sucedidas, `Failed` quando executadas e apresentam falha ou `Skipped` quando o desvio não é executado.

## <a name="next-steps"></a>Próximas etapas

[API REST do Serviço de Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows)

