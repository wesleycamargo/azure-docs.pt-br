---
title: Ações e gatilhos do fluxo de trabalho – Aplicativos Lógicos do Azure | Microsoft Docs
description: Conheça os gatilhos e as ações para criar fluxos de trabalho e processos automatizados com aplicativos lógicos
services: logic-apps
author: divyaswarnkar
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 10/13/2017
ms.author: klam; LADocs
ms.openlocfilehash: 28d28888ce66c354da39dc636579655aadbb9e51
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/20/2018
---
# <a name="triggers-and-actions-for-logic-app-workflows"></a>Gatilhos e ações para fluxos de trabalho de aplicativos lógicos

Todos os aplicativos lógicos começam com um gatilho seguido por ações. Este artigo descreve os tipos de gatilhos e ações que você pode usar para criar integrações do sistema e automatizar processos ou fluxos de trabalho de negócios criando aplicativos lógicos. 
  
## <a name="triggers-overview"></a>Visão geral de gatilhos 

Todos os aplicativos lógicos com um gatilho, que especifica as chamadas que podem iniciar a execução de um aplicativo lógico. Aqui estão os tipos de gatilhos que você pode usar:

* Um gatilho de *sondagem*, que verifica o ponto de extremidade HTTP do serviço em intervalos regulares
* Um gatilho de *push*, que chama a [API REST do Serviço de Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
  
Todos os gatilhos contêm estes elementos de alto nível:  
  
```json
"<myTriggerName>": {
    "type": "<triggerType>",
    "inputs": { <callSettings> },
    "recurrence": {  
        "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
        "interval": "<recurrence-interval-based-on-frequency>"
    },
    "conditions": [ <array-with-required-conditions> ],
    "splitOn": "<property-used-for-creating-runs>",
    "operationOptions": "<options-for-operations-on-the-trigger>"
}
```

## <a name="trigger-types-and-inputs"></a>Tipos e entradas de gatilhos  

Cada tipo de gatilho tem uma interface diferente e *entradas* diferentes que definem seu comportamento. 

| Tipo de gatilho | DESCRIÇÃO | 
| ------------ | ----------- | 
| **Recorrência** | Acionado com base em um agendamento definido. Você pode definir uma data e hora futura para acionar esse gatilho. Com base na frequência, você também pode especificar os horários e dias para executar o fluxo de trabalho. | 
| **Solicitação**  | Torna seu aplicativo lógico em um ponto de extremidade que você pode chamar, também conhecido como "gatilho manual". | 
| **HTTP** | Verifica ou *sonda*, um ponto de extremidade da Web de HTTP. O ponto de extremidade HTTP deve estar em conformidade com um contrato de gatilho específico usando um padrão assíncrono “202” ou retornando uma matriz. | 
| **ApiConnection** | Sonda como um gatilho HTTP, mas usa [APIs gerenciadas pela Microsoft](../connectors/apis-list.md). | 
| **HTTPWebhook** | Torna o seu aplicativo lógico em um ponto de extremidade que pode ser chamado, como o gatilho de **Solicitação**, mas chama uma URL especificada para registrar e cancelar o registro. |
| **ApiConnectionWebhook** | Funciona como o gatilho **HTTPWebhook**, mas usa APIs gerenciadas pela Microsoft. | 
||| 

Para obter mais informações, consulte [Linguagem de Definição de Fluxo de Trabalho](../logic-apps/logic-apps-workflow-definition-language.md). 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Gatilho de recorrência  

Esse gatilho é executado com base na recorrência e na agenda que você especifica e fornece uma maneira fácil para executar regularmente um fluxo de trabalho. 

Aqui está um exemplo de gatilho de recorrência básico executado diariamente:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Você também pode agendar uma data e hora de início para disparar o gatilho. Por exemplo, para iniciar um relatório semanal toda segunda-feira, você poderá agendar o aplicativo lógico para iniciar em uma segunda-feira específica como neste exemplo: 

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": "1",
        "startTime": "2017-09-18T00:00:00Z"
    }
}
```

Eis uma definição para esse gatilho:

```json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "second|minute|hour|day|week|month",
        "interval": <recurrence-interval-based-on-frequency>,
        "schedule": {
            // Applies only when frequency is Day or Week. Separate values with commas.
            "hours": [ <one-or-more-hour-marks> ], 
            // Applies only when frequency is Day or Week. Separate values with commas.
            "minutes": [ <one-or-more-minute-marks> ], 
            // Applies only when frequency is Week. Separate values with commas.
            "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
        },
        "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
        "timeZone": "<specify-time-zone>"
    }
}
```

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| frequência | sim | Cadeia de caracteres | A unidade de tempo para a frequência com que o gatilho dispara. Use apenas um destes valores: "second", "minute", "hour", "day", "week" ou "month" | 
| intervalo | sim | Número inteiro | Um inteiro positivo que descreve a frequência na qual o fluxo de trabalho é executado com base na frequência. <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. | 
| timeZone | Não  | Cadeia de caracteres | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que deseja aplicar. | 
| startTime | Não  | Cadeia de caracteres | Especifique a data e hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você especificar um fuso horário <p>-ou- <p>AAAA-MM-DDThh:mm:ssZ se você não especificar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 14h, especifique "2017-09-18T14:00:00" e selecione um fuso horário, como “Hora do Pacífico”. Ou, especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** a hora de início deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não especificar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. Para obter mais informações sobre datas e horas de início, consulte [Criar e agendar tarefas de execução regularmente](../connectors/connectors-native-recurrence.md). | 
| weekDays | Não  | Cadeia de caracteres ou matriz de cadeia de caracteres | Se você selecionar "Semana" para `frequency`, poderá selecionar um ou mais dias, separados por vírgulas, nos quais deseja executar o fluxo de trabalho: “segunda-feira”, “terça-feira”, “quarta-feira”, “quinta-feira”, “Sexta-feira”, “sábado” e “domingo” | 
| hours | Não  | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 23, separados por vírgulas, como as horas do dia nas quais você deseja executar o fluxo de trabalho. <p>Por exemplo, se você especificar "10", "12" e "14", você obterá 10h, 12h e 14h como as marcas de hora. | 
| minutes | Não  | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 59, separados por vírgulas, como os minutos da hora nos quais você deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minutos e, usando o exemplo anterior como as horas do dia, você obtém 10h30, 12h30 e 14h30. | 
||||| 

Por exemplo, esse gatilho de recorrência especifica que o seu aplicativo lógico é executado semanalmente toda segunda-feira às 10:30, 12:30 e 14:30 na Hora Padrão do Pacífico, não iniciando antes de 9 de setembro de 2017 às 14:00:

``` json
"myRecurrenceTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Week",
        "interval": 1,
        "schedule": {
            "hours": [
                10,
                12,
                14
            ],
            "minutes": [
                30
            ],
            "weekDays": [
                "Monday"
            ]
        },
       "startTime": "2017-09-07T14:00:00",
       "timeZone": "Pacific Standard Time"
    }
}
```

Para obter mais informações com recorrência e exemplos de hora de início para esse gatilho, consulte [Criar e agendar tarefas de execução regularmente](../connectors/connectors-native-recurrence.md).

## <a name="request-trigger"></a>Gatilho de solicitação

Esse gatilho serve como um ponto de extremidade que você usa para chamar seu aplicativo lógico por meio de uma solicitação HTTP. Um gatilho de solicitação é semelhante a este exemplo:  
  
```json
"myRequestTrigger": {
    "type": "Request",
    "kind": "Http",
    "inputs": {
        "schema": {
            "type": "Object",
            "properties": {
                "myInputProperty1": { "type" : "string" },
                "myInputProperty2": { "type" : "number" }
            },
            "required": [ "myInputProperty1" ]
        }
    }
} 
```

Esse gatilho tem uma propriedade opcional denominada `schema`:
  
| Nome do elemento | Obrigatório | type | DESCRIÇÃO |
| ------------ | -------- | ---- | ----------- |
| schema | Não  | Objeto | Um esquema JSON que valida a solicitação de entrada. Útil para ajudar as etapas subsequentes do fluxo de trabalho a saberem a quais propriedades fazer referência. | 
||||| 

Para invocar esse gatilho como ponto de extremidade, você precisa chamar a API `listCallbackUrl`. Consulte [API REST do Serviço do Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows).

## <a name="http-trigger"></a>Gatilho HTTP  

Esse gatilho sonda um ponto de extremidade especificado e verifica a resposta para determinar se o fluxo de trabalho deve ser executado ou não. Aqui, o objeto `inputs` usa esses parâmetros necessários para construir uma chamada HTTP: 

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| estático | sim | Cadeia de caracteres | Usa um dos seguintes métodos HTTP: “GET”, “POST”, “PUT”, “DELETE”, “PATCH” ou “HEAD” | 
| uri | sim| Cadeia de caracteres | O ponto de extremidade HTTP ou HTTPS que o gatilho verifica. Tamanho máximo da cadeia de caracteres: 2 KB | 
| consultas | Não  | Objeto | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| Autenticação | Não  | Objeto | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). <p>Além do Agendador, há mais uma propriedade com suporte: `authority`. Por padrão, esse valor é `https://login.windows.net` quando não especificado, mas você pode usar um valor diferente, como `https://login.windows\-ppe.net`. | 
||||| 

Uma *política de repetição* se aplica às falhas intermitentes, caracterizadas como os códigos de status HTTP 408, 429 e 5xx, além de quaisquer exceções de conectividade. Você pode definir essa política com o objeto `retryPolicy` conforme mostrado aqui:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Para funcionar bem com seu aplicativo lógico, o gatilho HTTP requer a API HTTP estar em conformidade com um padrão específico. O gatilho reconhece estas propriedades:  
  
| Response | Obrigatório | DESCRIÇÃO | 
| -------- | -------- | ----------- |  
| Código de status | sim | O código de status 200 ("OK") causa uma execução. Qualquer outro código de status não provoca uma execução. | 
| Cabeçalho Retry-after | Não  | O número de segundos até que o aplicativo lógico sonde o ponto de extremidade novamente. | 
| Cabeçalho do local | Não  | A URL a chamar no próximo intervalo de sondagem. Se não for especificada, a URL original será usada. | 
|||| 

Aqui estão alguns exemplos de comportamentos para diferentes tipos de solicitações:
  
| Código de resposta | Tentar novamente após | Comportamento | 
| ------------- | ----------- | -------- | 
| 200 | {none} | Execute o fluxo de trabalho e verifique novamente se há mais dados após a recorrência definida. | 
| 200 | 10 segundos | Execute o fluxo de trabalho e verifique novamente se há mais dados após 10 segundos. |  
| 202 | 60 segundos | Não dispara o fluxo de trabalho. A próxima tentativa ocorre em um minuto, sujeito à recorrência definida. Se a recorrência definida for inferior a um minuto, o cabeçalho retry-after terá precedência. Caso contrário, a recorrência definida será usada. | 
| 400 | {none} | Solicitação inválida, não execute o fluxo de trabalho. Se nenhum `retryPolicy` for definido, a política padrão será usada. Após a quantidade de novas tentativas ter sido atingida, o gatilho buscará novamente os dados após a recorrência definida. | 
| 500 | {none}| Erro do servidor, não execute o fluxo de trabalho. Se nenhum `retryPolicy` for definido, a política padrão será usada. Após a quantidade de novas tentativas ter sido atingida, o gatilho buscará novamente os dados após a recorrência definida. | 
|||| 

Aqui estão as saídas do gatilho HTTP: 
  
| Nome do elemento | type | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| headers | Objeto | Os cabeçalhos da resposta HTTP | 
| body | Objeto | O corpo da resposta HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Gatilho APIConnection  

Na funcionalidade básica, esse gatilho funciona como o gatilho HTTP. No entanto, os parâmetros para identificar a ação são diferentes. Veja um exemplo:   
  
```json
"myDailyReportTrigger": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://myarticles.example.com/"
            }
        },
        "connection": {
            "name": "@parameters('$connections')['myconnection'].name"
        }
    },  
    "method": "POST",
    "body": {
        "category": "myCategory"
    }
}
```

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| host | sim | Objeto | O gateway hospedado e a ID para o Aplicativo de API | 
| estático | sim | Cadeia de caracteres | Usa um dos seguintes métodos HTTP: “GET”, “POST”, “PUT”, “DELETE”, “PATCH” ou “HEAD” | 
| consultas | Não  | Objeto | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| Autenticação | Não  | Objeto | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). | 
||||| 

Para o objeto `host`, estas são as propriedades:  
  
| Nome do elemento | Obrigatório | DESCRIÇÃO | 
| ------------ | -------- | ----------- | 
| api runtimeUrl | sim | O ponto de extremidade da API gerenciada | 
| nome da conexão |  | O nome da conexão de API gerenciada que o fluxo de trabalho usa. Deve fazer referência a um parâmetro denominado `$connection`. |
|||| 

Uma *política de repetição* se aplica às falhas intermitentes, caracterizadas como os códigos de status HTTP 408, 429 e 5xx, além de quaisquer exceções de conectividade. Você pode definir essa política com o objeto `retryPolicy` conforme mostrado aqui:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Estas são as saídas de um gatilho de Conexão da API:
  
| Nome do elemento | type | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| headers | Objeto | Os cabeçalhos da resposta HTTP | 
| body | Objeto | O corpo da resposta HTTP | 
|||| 

Saiba mais sobre [como funcionam os preços de gatilhos da Conexão de API](../logic-apps/logic-apps-pricing.md#triggers).

## <a name="httpwebhook-trigger"></a>Gatilho HTTPWebhook  

O gatilho fornece um ponto de extremidade, semelhante ao gatilho `Request`, mas o gatilho HTTPWebhook também chama uma URL especificada para registrar e cancelar o registro. Aqui está um exemplo de como um gatilho HTTPWebhook pode ser:

```json
"myAppsSpotTrigger": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "https://pubsubhubbub.appspot.com/subscribe",
            "headers": {},
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "https://pubsubhubbub.appspot.com/subscribe",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
            },
            "authentication": {}
        }
    },
    "conditions": []
}
```

Muitas dessas seções são opcionais e o comportamento de gatilho HTTPWebhook depende das seções que você fornecer ou omitir. Estas são as propriedades para o gatilho HTTPWebhook:
  
| Nome do elemento | Obrigatório | DESCRIÇÃO | 
| ------------ | -------- | ----------- |  
| assinar | Não  | Especifica a solicitação de saída para chamar quando o gatilho é criado e realiza o registro inicial. | 
| cancelar assinatura | Não  | Especifica a solicitação de saída para chamar quando o gatilho é excluído. | 
|||| 

Você pode especificar os limites em um gatilho webhook da mesma maneira que os [Limites Assíncronos do HTTP](#asynchronous-limits). Aqui estão mais informações sobre as ações `subscribe` e `unsubscribe`:

* `subscribe` é chamado de modo que o gatilho possa começar a escutar eventos. Essa chamada saída começa com os mesmos parâmetros que ações HTTP padrão. Essa chamada acontece quando o fluxo de trabalho muda de alguma forma, por exemplo, sempre que as credenciais são distribuídas ou os parâmetros de entrada do gatilho mudam. 
  
  Para dar suporte a essa chamada, a função `@listCallbackUrl()` retorna uma URL exclusiva para esse gatilho específico neste fluxo de trabalho. Essa URL representa o identificador exclusivo dos pontos de extremidade que usam a API REST do serviço.
  
* `unsubscribe` é chamado automaticamente quando uma operação torna esse gatilho inválido, incluindo estas operações:

  * Excluir ou desabilitar o gatilho. 
  * excluir ou desabilitar o fluxo de trabalho. 
  * excluir ou desabilitar a assinatura. 
  
  Os parâmetros dessa função são os mesmos do gatilho HTTP.

Estas são as saídas do gatilho HTTPWebhook e são o conteúdo da solicitação de entrada:
  
| Nome do elemento | type | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| headers | Objeto | Os cabeçalhos da resposta HTTP | 
| body | Objeto | O corpo da resposta HTTP | 
|||| 

## <a name="triggers-conditions"></a>Gatilhos: condições

Para qualquer gatilho, você pode usar uma ou mais condições para determinar se o fluxo de trabalho deve ser executado ou não. Neste caso, o relatório somente dispara enquanto o parâmetro `sendReports` do fluxo de trabalho está definido como true. 

```json
"myDailyReportTrigger": {
    "type": "Recurrence",
    "conditions": [ 
        {
            "expression": "@parameters('sendReports')"
        } 
    ],
    "recurrence": {
        "frequency": "Day",
        "interval": 1
    }
}
```

Por fim, as condições podem referenciar o código de status do gatilho. Por exemplo, você pode iniciar um fluxo de trabalho somente quando seu site retornar um código de status 500:
  
``` json
"conditions": [ 
    {  
      "expression": "@equals(triggers().code, 'InternalServerError')"  
    }  
]  
```  

> [!NOTE]
> Por padrão, um gatilho é acionado somente no recebimento de uma resposta "200 OK". Quando uma expressão referenciar o código de status de um gatilho de alguma forma, o comportamento padrão do gatilho será substituído. Portanto, se desejar que o gatilho seja acionado com base em vários códigos de status, por exemplo, o código de status 200 e o código de status 201, você deverá incluir essa instrução como sua condição: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-process-an-array-with-multiple-runs"></a>Gatilhos: processar uma matriz com várias execuções

Se o gatilho retornar uma matriz para o aplicativo lógico processar, às vezes, um loop "for each" poderá levar muito tempo para processar cada item da matriz. Em vez disso, você pode usar a propriedade **SplitOn** em seu gatilho para *retirar em lote* a matriz. 

A retirada em lote divide os itens da matriz e inicia uma nova instância do aplicativo lógico que é executado para cada item da matriz. Essa abordagem é útil, por exemplo, quando você deseja sondar um ponto de extremidade que pode retornar vários itens novos entre os intervalos de sondagem.
Para o número máximo de itens de matriz que **SplitOn** pode processar em uma execução única do aplicativo lógico, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md). 

> [!NOTE]
> Você pode adicionar **SplitOn** somente a gatilhos definindo ou substituindo manualmente na exibição de código para a definição de JSON do seu aplicativo lógico. Não é possível usar **SplitOn** quando você deseja implementar um padrão de resposta síncrona. Qualquer fluxo de trabalho que usa **SplitOn** e inclui uma ação de resposta é executado de forma assíncrona e envia imediatamente uma resposta `202 ACCEPTED`.

Se o arquivo Swagger do gatilho descrever uma carga que é uma matriz, a propriedade **SplitOn** será adicionada automaticamente ao seu gatilho. Caso contrário, adicione essa propriedade dentro da carga de resposta que tem a matriz da qual deseja remover o lote. 

Por exemplo, suponha que você tenha uma API que retorna a seguinte resposta: 
  
```json
{
    "Status": "Succeeded",
    "Rows": [ 
        { 
            "id": 938109380,
            "name": "customer-name-one"
        },
        {
            "id": 938109381,
            "name": "customer-name-two"
        }
    ]
}
```
  
Seu aplicativo lógico precisa apenas do conteúdo de `Rows` para que você possa criar o gatilho como neste exemplo.

``` json
"myDebatchTrigger": {
    "type": "Http",
    "recurrence": {
        "frequency": "Second",
        "interval": "1"
    },
    "inputs": {
        "uri": "https://mydomain.com/myAPI",
        "method": "GET"
    },
    "splitOn": "@triggerBody()?.Rows"
}
```

> [!NOTE]
> Portanto, se você usar o comando `SplitOn`, não será possível obter as propriedades que estão fora da matriz. Portanto, para este exemplo, não é possível obter a propriedade `status` na resposta retornada da API.
> 
> Para evitar uma falha se a propriedade `Rows` não existir, este exemplo usa o operador `?`.

Sua definição de fluxo de trabalho agora pode usar `@triggerBody().name` para obter `customer-name-one` da primeira execução e `customer-name-two` da segunda execução. Portanto, as saídas do gatilho ficam como neste exemplo:

```json
{
    "body": {
        "id": 938109380,
        "name": "customer-name-one"
    }
}
```

```json
{
    "body": {
        "id": 938109381,
        "name": "customer-name-two"
    }
}
```
  
## <a name="triggers-fire-only-after-all-active-runs-finish"></a>Gatilhos: são acionados somente após a conclusão de todas as execuções ativas

Você pode configurar os gatilhos de recorrência para que eles sejam acionados somente quando todas as execuções ativas forem concluídas. Para definir essa configuração, defina a propriedade `operationOptions` como `singleInstance`:

```json
"myTrigger": {
    "type": "Http",
    "inputs": { },
    "recurrence": { },
    "operationOptions": "singleInstance"
}
```

Se uma recorrência agendada ocorrer enquanto houver a execução de uma instância de fluxo de trabalho, o gatilho ignorará e aguardará até o próximo intervalo de recorrência agendado para verificar novamente.

## <a name="actions-overview"></a>Visão geral de ações

Há muitos tipos de ações, cada um com um comportamento exclusivo. Cada tipo de ação tem diferentes entradas que definem o comportamento de uma ação. As ações de coleção podem conter muitas outras ações em si. 

### <a name="standard-actions"></a>Ações padrão  

| Tipo de ação | DESCRIÇÃO | 
| ----------- | ----------- | 
| **HTTP** | Chama um ponto de extremidade HTTP da Web. | 
| **ApiConnection**  | Funciona como a ação HTTP, mas usa [APIs gerenciadas pela Microsoft](https://docs.microsoft.com/azure/connectors/apis-list). | 
| **ApiConnectionWebhook** | Funciona como HTTPWebhook, mas usa APIs gerenciadas pela Microsoft. | 
| **Resposta** | Define a resposta para uma chamada de entrada. | 
| **Compor** | Cria um objeto arbitrário de entradas da ação. | 
| **Função** | Representa uma função do Azure. | 
| **Aguardar** | Aguarda um período fixo de tempo ou até uma hora específica. | 
| **Fluxo de trabalho** | Representa um fluxo de trabalho aninhado. | 
| **Compor** | Cria um objeto arbitrário de entradas da ação. | 
| **Consulta** | Filtra uma matriz com base em uma condição. | 
| **Selecionar** | Projeta cada elemento de uma matriz em um novo valor. Por exemplo, você pode converter uma matriz de números em uma matriz de objetos. | 
| **Tabela** | Converte uma matriz de itens em uma tabela CSV ou HTML. | 
| **Encerrar** | Interrompe a execução de um fluxo de trabalho. | 
| **Aguardar** | Aguarda um período fixo de tempo ou até uma hora específica. | 
| **Fluxo de trabalho** | Representa um fluxo de trabalho aninhado. | 
||| 

### <a name="collection-actions"></a>Ações de coleção

| Tipo de ação | DESCRIÇÃO | 
| ----------- | ----------- | 
| **If** | Avalia uma expressão e, com base no resultado, executa a ramificação correspondente. | 
| **Switch** | Executa ações diferentes com base em valores específicos de um objeto. | 
| **ForEach** | Esta ação de loop itera por meio de uma matriz e executa as ações internas de cada item da matriz. | 
| **Until** | Esta ação de loop executa as ações internas até uma condição ser verdadeira. | 
| **Escopo** | Use para o agrupamento lógico de outras ações. | 
|||  

## <a name="http-action"></a>Ação HTTP  

Uma ação HTTP chama um ponto de extremidade especificado e verifica a resposta para determinar se o fluxo de trabalho deve ser executado ou não. Por exemplo: 
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest"
    }
}
```

Aqui, o objeto `inputs` usa esses parâmetros necessários para construir uma chamada HTTP: 

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| estático | sim | Cadeia de caracteres | Usa um dos seguintes métodos HTTP: “GET”, “POST”, “PUT”, “DELETE”, “PATCH” ou “HEAD” | 
| uri | sim| Cadeia de caracteres | O ponto de extremidade HTTP ou HTTPS que o gatilho verifica. Tamanho máximo da cadeia de caracteres: 2 KB | 
| consultas | Não  | Objeto | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Não  | Cadeia de caracteres | Define o conjunto de comportamentos especiais a substituir. | 
| Autenticação | Não  | Objeto | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). <p>Além do Agendador, há mais uma propriedade com suporte: `authority`. Por padrão, esse valor é `https://login.windows.net` quando não especificado, mas você pode usar um valor diferente, como `https://login.windows\-ppe.net`. | 
||||| 

As ações HTTP e as ações de APIConnection têm suporte para as *políticas de repetição*. Uma política de repetição se aplica às falhas intermitentes, caracterizadas como os códigos de status HTTP 408, 429 e 5xx, além de quaisquer exceções de conectividade. Você pode definir essa política com o objeto `retryPolicy` conforme mostrado aqui:
  
```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

Nesse exemplo, a ação HTTP tenta buscar as notícias mais recentes duas vezes se houver falhas intermitentes, com um total de três execuções, com um atraso de 30 segundos entre cada tentativa:
  
```json
"myLatestNewsAction": {
    "type": "Http",
    "inputs": {
        "method": "GET",
        "uri": "https://mynews.example.com/latest",
        "retryPolicy": {
            "type": "fixed",
            "interval": "PT30S",
            "count": 2
        }
    }
}
```

O intervalo de repetição é especificado no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Esse intervalo tem um valor padrão e mínimo de 20 segundos, enquanto o valor máximo é de uma hora. A contagem de repetição padrão e máxima é de quatro horas. Se uma definição da política de repetição não for especificada, uma estratégia `fixed` será usada com valores de contagem e intervalo de repetição padrão. Para desativar a política de repetição, defina seu tipo para `None`.

### <a name="asynchronous-patterns"></a>Padrões assíncronos

Por padrão, todas as ações baseadas no HTTP suportam o padrão de operação assíncrona padrão. Portanto, se o servidor remoto indicar que a solicitação é aceita para o processamento, com uma resposta “202 ACEITO”, o mecanismo dos Aplicativos Lógicos continuará sondando a URL especificada no cabeçalho de local da resposta até atingir um estado terminal de resposta não 202.
  
Para desabilitar o comportamento assíncrono descrito anteriormente, defina `operationOptions` como `DisableAsyncPattern` nas entradas de ação. Neste caso, a saída da ação baseia-se na resposta 202 inicial do servidor. Por exemplo: 
  
```json
"invokeLongRunningOperationAction": {
    "type": "Http",
    "inputs": {
        "method": "POST",
        "uri": "https://host.example.com/resources"
    },
    "operationOptions": "DisableAsyncPattern"
}
```

<a name="asynchronous-limits"></a>

#### <a name="asynchronous-limits"></a>Limites assíncronos

Você pode limitar a duração de um padrão assíncrono para um intervalo de tempo específico. Se o intervalo de tempo transcorrer sem atingir um estado terminal, o status da ação será marcado como `Cancelled`, com um código `ActionTimedOut`. O tempo limite é especificado no formato ISO 8601. Este exemplo mostra como você pode especificar limites:


``` json
"<action-name>": {
    "type": "Workflow|Webhook|Http|ApiConnectionWebhook|ApiConnection",
    "inputs": { },
    "limit": {
        "timeout": "PT10S"
    }
}
```
  
## <a name="apiconnection-action"></a>Ação APIConnection

Esta ação faz referência a um conector gerenciado pela Microsoft, solicitando uma referência para uma conexão válida e informações sobre a API e os parâmetros. Aqui está um exemplo de ação APIConnection:

```json
"Send_Email": {
    "type": "ApiConnection",
    "inputs": {
        "host": {
            "api": {
                "runtimeUrl": "https://logic-apis-df.azure-apim.net/apim/office365"
            },
            "connection": {
                "name": "@parameters('$connections')['office365']['connectionId']"
            }
        },
        "method": "POST",
        "body": {
            "Subject": "New tweet from @{triggerBody()['TweetedBy']}",
            "Body": "@{triggerBody()['TweetText']}",
            "To": "me@example.com"
        },
        "path": "/Mail"
    },
    "runAfter": {}
}
```

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| host | sim | Objeto | Representa as informações do conector, como `runtimeUrl` e uma referência para o objeto de conexão. | 
| estático | sim | Cadeia de caracteres | Usa um dos seguintes métodos HTTP: “GET”, “POST”, “PUT”, “DELETE”, “PATCH” ou “HEAD” | 
| caminho | sim | Cadeia de caracteres | O caminho da operação da API | 
| consultas | Não  | Objeto | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Não  | Cadeia de caracteres | Define o conjunto de comportamentos especiais a substituir. | 
| Autenticação | Não  | Objeto | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
||||| 

Uma política de repetição se aplica às falhas intermitentes, caracterizadas como os códigos de status HTTP 408, 429 e 5xx, além de quaisquer exceções de conectividade. Você pode definir essa política com o objeto `retryPolicy` conforme mostrado aqui:

```json
"retryPolicy": {
    "type": "<retry-policy-type>",
    "interval": <retry-interval>,
    "count": <number-of-retry-attempts>
}
```

## <a name="apiconnection-webhook-action"></a>Ação do webhook de APIConnection

A ação APIConnectionWebhook se refere a um conector gerenciado pela Microsoft. Esta ação requer uma referência para uma conexão válida e informações sobre a API e os parâmetros. Você pode especificar os limites em uma ação de webhook da mesma maneira como os [Limites Assíncronos do HTTP](#asynchronous-limits).

```json
"Send_approval_email": {
    "type": "ApiConnectionWebhook",
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

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| host | sim | Objeto | Representa as informações do conector, como `runtimeUrl` e uma referência para o objeto de conexão. | 
| caminho | sim | Cadeia de caracteres | O caminho da operação da API | 
| consultas | Não  | Objeto | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Não  | Cadeia de caracteres | Define o conjunto de comportamentos especiais a substituir. | 
| Autenticação | Não  | Objeto | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
||||| 

## <a name="response-action"></a>Ação de resposta  

Essa ação tem o conteúdo inteiro de resposta de uma solicitação HTTP e inclui um `statusCode`, `body` e `headers`:
  
```json
"myResponseAction": {
    "type": "Response",
    "inputs": {
        "statusCode": 200,
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        },
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        }
    },
    "runAfter": {}
}
```

A ação de resposta tem restrições especiais que não se aplicam a outras ações, especificamente:  
  
* Você não pode ter ações de resposta em branches paralelas dentro de uma definição de aplicativo lógico porque a solicitação de entrada requer uma resposta determinística.
  
* Se o fluxo de trabalho alcançar uma ação de resposta após a solicitação de entrada já ter recebido a resposta, a ação de resposta é considerada com falha ou em conflito. Como resultado, a execução do aplicativo lógico é marcada como `Failed`.
  
* Um fluxo de trabalho com ações de resposta não pode usar o comando `splitOn` na definição de gatilho porque a chamada cria várias execuções. Como resultado, verifique para esse caso quando a operação de fluxo de trabalho é PUT e retorna uma resposta de "solicitação incorreta".

## <a name="compose-action"></a>Ação para compor

Essa ação permite que você construa um objeto arbitrário e a saída é o resultado da avaliação de entradas da ação. 

> [!NOTE]
> Você pode usar a ação `Compose` para construir qualquer saída, inclusive objetos, matrizes e qualquer outro tipo com suporte nativo dos aplicativos lógicos, como XML e binários.

Por exemplo, você pode usar a ação `Compose` para mesclar as saídas de várias ações:

```json
"composeUserRecordAction": {
    "type": "Compose",
    "inputs": {
        "firstName": "@actions('getUser').firstName",
        "alias": "@actions('getUser').alias",
        "thumbnailLink": "@actions('lookupThumbnail').url"
    }
}
```

## <a name="function-action"></a>Ação de função

Essa ação permite que você represente e chame uma [função do Azure](../azure-functions/functions-overview.md), por exemplo:

```json
"<my-Azure-Function-name>": {
   "type": "Function",
    "inputs": {
        "function": {
            "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.Web/sites/<your-Azure-function-app-name>/functions/<your-Azure-function-name>"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()"
        },
        "method": "POST",
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- |  
| Id de Função | sim | Cadeia de caracteres | A ID de recurso para a função do Azure que deseja chamar. | 
| estático | Não  | Cadeia de caracteres | O método HTTP usado para chamar a função. Se não for especificado, "POST" será o método padrão. | 
| consultas | Não  | Objeto | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto | Representa a carga enviada para o ponto de extremidade. | 
|||||

Quando você salva seu aplicativo lógico, o mecanismo Aplicativos Lógicos realiza algumas verificações na função referenciada:

* Você precisa ter acesso à função.
* Você pode usar apenas um gatilho HTTP padrão ou um gatilho de Webhook JSON genérico.
* A função não deve ter nenhuma rota definida.
* Apenas os níveis de autorização "função" e "anônimo" são permitidos.

> [!NOTE]
> O mecanismo de Aplicativos Lógicos recupera e armazena em cache a URL do gatilho, que é usada no tempo de execução. Portanto, se qualquer operação invalida a URL armazenada em cache, a ação falhará em tempo de execução. Para solucionar esse problema, salve o aplicativo lógico novamente, o que faz com que o aplicativo lógico recupere e armazene em cache a URL do gatilho novamente.

## <a name="select-action"></a>Ação selecionar

Essa ação permite projetar cada elemento de uma matriz em um novo valor. Este exemplo converte uma matriz de números em uma matriz de objetos:

```json
"selectNumbersAction": {
    "type": "Select",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "select": { "number": "@item()" }
    }
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| de | sim | Matriz | A matriz de origem |
| selecionar | sim | Qualquer | A projeção a ser aplicada a cada elemento da matriz de origem |
||||| 

A saída da ação `select` é uma matriz que tem a mesma cardinalidade da matriz de entrada. Cada elemento é transformado conforme definido pela propriedade `select`. Se a entrada for uma matriz vazia, a saída também será uma matriz vazia.

## <a name="terminate-action"></a>Ação para finalizar

Essa ação interrompe uma execução de fluxo de trabalho, cancelando as ações em andamento e ignorando todas as ações restantes. A ação de encerramento não afeta as ações já concluídas.

Por exemplo, para interromper uma execução com status `Failed`:

```json
"HandleUnexpectedResponse": {
    "type": "Terminate",
    "inputs": {
        "runStatus": "Failed",
        "runError": {
            "code": "UnexpectedResponse",
            "message": "Received an unexpected response",
        }
    }
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| runStatus | sim | Cadeia de caracteres | O status da execução de destino, que é `Failed` ou `Cancelled` |
| runError | Não  | Objeto | Os detalhes do erro. Com suporte somente quando `runStatus` estiver definido como `Failed`. |
| código runError | Não  | Cadeia de caracteres | O código de erro da execução |
| mensagem runError | Não  | Cadeia de caracteres | A mensagem de erro da execução | 
||||| 

## <a name="query-action"></a>Ação de consulta

Essa ação permite filtrar uma matriz com base em uma condição. 

> [!NOTE]
> Você não pode usar a ação Compor para construir saídas, inclusive objetos, matrizes e qualquer outro tipo com suporte nativo dos aplicativos lógicos, como XML e binários.

Por exemplo, para selecionar números maiores que dois:

```json
"filterNumbersAction": {
    "type": "Query",
    "inputs": {
        "from": [ 1, 3, 0, 5, 4, 2 ],
        "where": "@greater(item(), 2)"
    }
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| de | sim | Matriz | A matriz de origem |
| onde | sim | Cadeia de caracteres | A condição que é aplicada a cada elemento da matriz de origem. Se nenhum valor atender a condição `where`, o resultado será uma matriz vazia. |
||||| 

A saída da ação `query` é uma matriz com elementos da matriz de entrada que atende a condição.

## <a name="table-action"></a>Ação tabela

Essa ação permite converter uma matriz em uma tabela CSV ou HTML. 

```json
"ConvertToTable": {
    "type": "Table",
    "inputs": {
        "from": "<source-array>",
        "format": "CSV | HTML"
    }
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| de | sim | Matriz | A matriz de origem. Se o valor da propriedade `from` for uma matriz vazia, a saída será uma tabela vazia. | 
| formato | sim | Cadeia de caracteres | O formato de tabela que você deseja, "CSV" ou "HTML" | 
| colunas | Não  | Matriz | As colunas da tabela que você deseja. Use para substituir a forma da tabela padrão. | 
| cabeçalho de coluna | Não  | Cadeia de caracteres | O cabeçalho da coluna | 
| valor da coluna | sim | Cadeia de caracteres | O valor da coluna | 
||||| 

Suponha que você defina uma ação de tabela, como neste exemplo:

```json
"convertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "HTML"
    }
}
```

E use essa matriz para `@triggerBody()`:

```json
[ {"ID": 0, "Name": "apples"},{"ID": 1, "Name": "oranges"} ]
```

Esta é a saída deste exemplo:

<table><thead><tr><th>ID</th><th>NOME</th></tr></thead><tbody><tr><td>0</td><td>apples</td></tr><tr><td>1</td><td>oranges</td></tr></tbody></table>

Para personalizar essa tabela, você pode especificar explicitamente as colunas, por exemplo:

```json
"ConvertToTableAction": {
    "type": "Table",
    "inputs": {
        "from": "@triggerBody()",
        "format": "html",
        "columns": [ 
            {
                "header": "Produce ID",
                "value": "@item().id"
            },
            {
              "header": "Description",
              "value": "@concat('fresh ', item().name)"
            }
        ]
    }
}
```

Esta é a saída deste exemplo:

<table><thead><tr><th>Produzir ID</th><th>DESCRIÇÃO</th></tr></thead><tbody><tr><td>0</td><td>fresh apples</td></tr><tr><td>1</td><td>fresh oranges</td></tr></tbody></table>

## <a name="wait-action"></a>Ação para aguardar  

Essa ação suspende a execução do fluxo de trabalho no intervalo especificado. Este exemplo faz com que o fluxo de trabalho aguarde 15 minutos:
  
```json
"waitForFifteenMinutesAction": {
    "type": "Wait",
    "inputs": {
        "interval": {
            "unit": "minute",
            "count": 15
        }
    }
}
```
  
Como alternativa, para esperar até um momento específico, você poderá usar este exemplo:
  
```json
"waitUntilOctoberAction": {
    "type": "Wait",
    "inputs": {
        "until": {
            "timestamp": "2017-10-01T00:00:00Z"
        }
    }
}
```
  
> [!NOTE]  
> Você pode especificar a duração da espera com o objeto `interval` ou o objeto `until`, mas não ambos.

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- | 
| until | Não  | Objeto | A duração de espera com base em um ponto no tempo | 
| until carimbo data/hora | sim | Cadeia de caracteres | O ponto no tempo no [formato de hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) quando a espera expira | 
| intervalo | Não  | Objeto | A duração de espera com base na contagem e unidade do intervalo | 
| unidade do intervalo | sim | Cadeia de caracteres | A unidade de tempo. Use apenas um destes valores: "second", "minute", "hour", "day", "week" ou "month" | 
| contagem do intervalo | sim | Número inteiro | Um inteiro positivo que representa o número de unidades de intervalo usado para a duração de espera | 
||||| 

## <a name="workflow-action"></a>Ação do fluxo de trabalho

Essa ação permite que você aninhe um fluxo de trabalho. O mecanismo de Aplicativos Lógicos executa uma verificação de acesso do fluxo de trabalho secundário, mais especificamente do gatilho, o que significa que você deve ter acesso ao fluxo de trabalho secundário. Por exemplo: 

```json
"<my-nested-workflow-action-name>": {
    "type": "Workflow",
    "inputs": {
        "host": {
            "id": "/subscriptions/<my-subscription-ID>/resourceGroups/<my-resource-group-name>/providers/Microsoft.Logic/<my-nested-workflow-action-name>",
            "triggerName": "mytrigger001"
        },
        "queries": {
            "extrafield": "specialValue"
        },  
        "headers": {
            "x-ms-date": "@utcnow()",
            "Content-type": "application/json"
        },
        "body": {
            "contentFieldOne": "value100",
            "anotherField": 10.001
        }
    },
    "runAfter": {}
}
```

| Nome do elemento | Obrigatório | type | DESCRIÇÃO | 
| ------------ | -------- | ---- | ----------- |  
| id do host | sim | Cadeia de caracteres| A ID de recurso do fluxo de trabalho que você deseja chamar | 
| triggerName do host | sim | Cadeia de caracteres | O nome do gatilho que você deseja invocar | 
| consultas | Não  | Objeto | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto | Representa o conteúdo enviado para o ponto de extremidade. | 
||||| 

As saídas da ação baseiam-se no que foi definido na ação `Response` no fluxo de trabalho filho. Se o fluxo de trabalho secundário não definir uma ação `Response`, as saídas ficarão vazias.

## <a name="collection-actions-overview"></a>Visão geral de ações da coleção

Para ajudá-lo a controlar a execução do fluxo de trabalho, as ações de coleção podem incluir outras ações. Você pode se referir diretamente às ações de referência em uma coleção fora da coleção. Por exemplo, se você definir uma ação `Http` em um escopo, `@body('http')` ainda será válido em qualquer lugar no fluxo de trabalho. Além disso, as ações em uma coleção podem apenas "correr atrás" de outras ações na mesma coleção.

## <a name="if-action"></a>Ação If

Essa ação, que é uma instrução condicional, permite que você avalie uma condição e execute uma ramificação caso a expressão seja avaliada como true. Se a condição for avaliada com êxito como true, a condição será marcada como "Êxito". Ações que estão nos objetos `actions` ou `else` são avaliadas quanto a estes valores:

* "Êxito" quando são executadas e bem-sucedidas
* "Falha" quando são executadas e falham
* "Ignorado" quando a respectiva ramificação não é executada

Saiba mais sobre [instruções condicionais em aplicativos lógicos](../logic-apps/logic-apps-control-flow-conditional-statement.md).

``` json
"<my-condition-name>": {
  "type": "If",
  "expression": "<condition>",
  "actions": {
    "if-true-run-this-action": {
      "type": <action-type>,
      "inputs": {},
      "runAfter": {}
    }
  },
  "else": {
    "actions": {
        "if-false-run-this-action": {
            "type": <action-type>,
            "inputs": {},
            "runAfter": {}
        }
    }
  },
  "runAfter": {}
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| Ações | sim | Objeto | As ações internas para executar quando `expression` for avaliado como `true` | 
| expressão | sim | Cadeia de caracteres | A expressão a avaliar |
| else | Não  | Objeto | As ações internas para executar quando `expression` for avaliado como `false` |
||||| 

Por exemplo: 

```json
"myCondition": {
    "type": "If",
    "actions": {
        "if-true-check-this-website": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://this-url"
            },
            "runAfter": {}
        }
    },
    "else": {
        "actions": {
            "if-false-check-this-other-website": {
                "type": "Http",
                "inputs": {
                    "method": "GET",
                    "uri": "http://this-other-url"
                },
                "runAfter": {}
            }
        }
    }
}
```  

### <a name="how-conditions-can-use-expressions-in-actions"></a>Como as condições podem usar expressões em ações

Estes são alguns exemplos que mostram como você pode usar expressões em condições:
  
| Expressão JSON | Result | 
| --------------- | ------ | 
| `"expression": "@parameters('hasSpecialAction')"` | Qualquer valor avaliado como true faz com que essa condição seja aprovada. Dá suporte apenas as expressões boolianas. Para converter outros tipos em booliano, use estas funções: `empty` ou `equals` | 
| `"expression": "@greater(actions('action1').output.value, parameters('threshold'))"` | Dá suporte a funções de comparação. Por exemplo aqui, a ação é executada somente quando a saída de action1 é maior que o valor limite. | 
| `"expression": "@or(greater(actions('action1').output.value, parameters('threshold')), less(actions('action1').output.value, 100))"` | Oferece suporte a funções lógicas para a criação de expressões boolianas aninhadas. Neste exemplo, a ação é executada somente quando a saída de action1 é maior que o limite ou menor que 100. | 
| `"expression": "@equals(length(actions('action1').outputs.errors), 0))"` | Você pode usar as funções de matriz para verificar se uma matriz tem algum item. Neste exemplo, a ação é executada quando a matriz de erros está vazia. | 
| `"expression": "parameters('hasSpecialAction')"` | Esta expressão causa um erro e não é uma condição válida. As condições devem usar o símbolo "@". | 
||| 

## <a name="switch-action"></a>Ação Switch

Esta ação, que é uma instrução switch, executa ações diferentes com base em valores específicos de um objeto, uma expressão ou um token. Essa ação avalia o objeto, a expressão ou o token, escolhe o caso que coincide com o resultado e executa ações para somente esse caso. Quando nenhum caso corresponde ao resultado, a ação padrão é executada. Apenas um caso deve corresponder ao resultado quando a instrução switch for executada. Saiba mais sobre [instruções switch em aplicativos lógicos](../logic-apps/logic-apps-control-flow-switch-statement.md).

``` json
"<my-switch-statement-name>": {
   "type": "Switch",
   "expression": "<evaluate-this-object-expression-token>",
   "cases": {
      "myCase1" : {
         "actions" : {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions" : {
           "myAction2": {}
         },
         "case": "<result2>"
      }
   },
   "default": {
      "actions": {
          "myDefaultAction": {}
      }
   },
   "runAfter": {}
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| expressão | sim | Cadeia de caracteres | O objeto, a expressão ou o token para avaliar | 
| cases | sim | Objeto | Contém os conjuntos de ações internas que são executadas com base no resultado da expressão. | 
| case | sim | Cadeia de caracteres | O valor para corresponder ao resultado | 
| Ações | sim | Objeto | As ações internas que são executadas para o caso que corresponde ao resultado da expressão | 
| padrão | Não  | Objeto | As ações internas que são executadas quando nenhum caso corresponde ao resultado | 
||||| 

Por exemplo: 

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions" : {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions" : {
           "Send_an_email_2": {...}
         },
         "case": "Reject"
      }
   },
   "default": {
      "actions": {}
   },
   "runAfter": {
      "Send_approval_email": [
         "Succeeded"
      ]
   }
}
```

## <a name="foreach-action"></a>Ação Foreach

Esta ação de loop itera por meio de uma matriz e executa as ações internas de cada item da matriz. Por padrão, o loop Foreach é executado em paralelo. Para o número máximo de ciclos paralelos que loops "for each" podem executar, consulte [Limites e configuração](../logic-apps/logic-apps-limits-and-config.md). Para executar cada ciclo sequencialmente, defina o parâmetro `operationOptions` como `Sequential`. Saiba mais sobre [loops Foreach em aplicativos lógicos](../logic-apps/logic-apps-control-flow-loops.md#foreach-loop).

```json
"<my-forEach-loop-name>": {
    "type": "Foreach",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    },
    "foreach": "<array>",
    "runAfter": {}
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| Ações | sim | Objeto | As ações internas a serem executadas dentro do loop | 
| foreach | sim | Cadeia de caracteres | A matriz pela qual iterar | 
| operationOptions | Não  | Cadeia de caracteres | Especifica as opções de operação para personalizar o comportamento. Atualmente, dá suporte apenas a `Sequential` para executar em sequência iterações em que o comportamento padrão é paralelo. |
||||| 

Por exemplo: 

```json
"forEach_EmailAction": {
    "type": "Foreach",
    "foreach": "@body('email_filter')",
    "actions": {
        "Send_email": {
            "type": "ApiConnection",
            "inputs": {
                "body": {
                    "to": "@item()",
                    "from": "me@contoso.com",
                    "message": "Hello, thank you for ordering"
                },
                "host": {
                    "connection": {
                        "id": "@parameters('$connections')['office365']['connection']['id']"
                    }
                }
            }
        }
    },
    "foreach": "@body('email_filter')",
    "runAfter": {
        "email_filter": [ "Succeeded" ]
    }
}
```

## <a name="until-action"></a>Ação Until

Esta ação de loop executa as ações internas até uma condição ser avaliada como true. Saiba mais sobre [loops "until" em aplicativos lógicos](../logic-apps/logic-apps-control-flow-loops.md#until-loop).

```json
 "<my-Until-loop-name>": {
    "type": "Until",
    "actions": {
        "myActionName": {
            "type": "<action-type>",
            "inputs": {},
            "runAfter": {}
        }
    },
    "expression": "<myCondition>",
    "limit": {
        "count": 1000,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- | 
| Ações | sim | Objeto | As ações internas a serem executadas dentro do loop | 
| expressão | sim | Cadeia de caracteres | A expressão a avaliar após cada iteração | 
| limite | sim | Objeto | Os limites do loop. Deve definir, pelo menos, um limite. | 
| count | Não  | Número inteiro | O limite do número de iterações para executar | 
| Tempo limite | Não  | Cadeia de caracteres | O tempo limite no [formato ISO 8601](https://en.wikipedia.org/wiki/ISO_8601) que especifica por quanto tempo o loop deve ser executado |
||||| 

Por exemplo: 

```json
 "runUntilSucceededAction": {
    "type": "Until",
    "actions": {
        "Http": {
            "type": "Http",
            "inputs": {
                "method": "GET",
                "uri": "http://myurl"
            },
            "runAfter": {}
        }
    },
    "expression": "@equals(outputs('Http')['statusCode', 200)",
    "limit": {
        "count": 100,
        "timeout": "PT1H"
    },
    "runAfter": {}
}
```

## <a name="scope-action"></a>Ação de escopo

Essa ação permite agrupar logicamente as ações em um fluxo de trabalho. O escopo também obtém seu próprio status após o término da execução de todas as ações nesse escopo. Saiba mais sobre os [escopos](../logic-apps/logic-apps-control-flow-run-steps-group-scopes.md).

```json
"<my-scope-action-name>": {
    "type": "Scope",
    "actions": {
        "myInnerAction1": {
            "type": "<action-type>",
            "inputs": {}
        },
        "myInnerAction2": {
            "type": "<action-type>",
            "inputs": {}
        }
    }
}
```

| NOME | Obrigatório | type | DESCRIÇÃO | 
| ---- | -------- | ---- | ----------- |  
| Ações | sim | Objeto | As ações internas para serem executadas dentro do escopo |
||||| 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Linguagem de Definição de Fluxo de Dados](../logic-apps/logic-apps-workflow-definition-language.md)
* Saiba mais sobre [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)