---
title: Ações e gatilhos do fluxo de trabalho – Aplicativos Lógicos do Azure | Microsoft Docs
description: Saiba mais sobre gatilhos e ações em definições de fluxo de trabalho para Aplicativo Lógico do Azure
services: logic-apps
author: kevinlam1
manager: SyntaxC4
editor: ''
documentationcenter: ''
ms.assetid: 86a53bb3-01ba-4e83-89b7-c9a7074cb159
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: reference
ms.date: 5/8/2018
ms.author: klam; LADocs
ms.openlocfilehash: 88ee3d810a80bed418e8dbafa4f3e35ccf5e85b1
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/08/2018
ms.locfileid: "33886775"
---
# <a name="triggers-and-actions-for-workflow-definitions-in-azure-logic-apps"></a>Gatilhos e ações para definições de fluxo de trabalho em Aplicativo Lógico do Azure

Em [Aplicativo Lógico do Azure](../logic-apps/logic-apps-overview.md), todos os fluxos de trabalho de aplicativos lógicos iniciam com gatilhos seguidos por ações. Este artigo descreve os gatilhos e as ações que podem ser utilizados para compilar aplicativos lógicos para automatizar fluxos de trabalho de negócios ou processos nas soluções de integração. É possível compilar aplicativos lógicos visualmente com o Designer de Aplicativos Lógicos ou criando diretamente as definições de fluxo de trabalho subjacentes com [Idioma de Definição do Fluxo de Trabalho](../logic-apps/logic-apps-workflow-definition-language.md). Você pode usar o portal do Azure ou o Visual Studio. Saiba como [funciona o preço para gatilhos e ações](../logic-apps/logic-apps-pricing.md).

<a name="triggers-overview"></a>

## <a name="triggers-overview"></a>Visão geral de gatilhos

Todos os aplicativos lógicos iniciam com um gatilho, o qual define as chamadas que podem instanciar e iniciar um fluxo de trabalho de aplicativo lógico. Aqui estão os tipos de gatilhos que você pode usar:

* Um gatilho de *sondagem*, que verifica o ponto de extremidade HTTP do serviço em intervalos regulares
* Um gatilho de *push*, que chama a [API REST do Serviço de Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows)
 
Todos os gatilhos têm esses elementos de nível superior, embora alguns sejam opcionais:  
  
```json
"<triggerName>": {
   "type": "<triggerType>",
   "inputs": { "<trigger-behavior-settings>" },
   "recurrence": { 
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "conditions": [ <array-with-required-conditions> ],
   "splitOn": "<property-used-for-creating-runs>",
   "operationOptions": "<optional-trigger-operations>"
}
```

*Obrigatório*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| <*triggerName*> | Objeto JSON | O nome do gatilho, que é um objeto descrito em formato JSON (Javascript Object Notation)  | 
| Tipo | Cadeia de caracteres | O tipo de gatilho, por exemplo: "Http" ou "ApiConnection" | 
| inputs | Objeto JSON | As entradas do gatilho que definem o comportamento do gatilho | 
| recurrence | Objeto JSON | A frequência e o intervalo que descrevem com que frequência o gatilho aciona |  
| frequência | Cadeia de caracteres | A unidade de tempo que descreve com que frequência o gatilho aciona: "Segundo", "Minuto", "Hora", "Dia", "Semana" ou "Mês" | 
| intervalo | Número inteiro | Um inteiro positivo que descreve com que frequência o gatilho é acionado com base na frequência. <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| [conditions](#trigger-conditions) | Matriz | Uma ou mais condições que determinam se é necessário ou não executar o fluxo de trabalho | 
| [splitOn](#split-on-debatch) | Cadeia de caracteres | Uma expressão que é dividida ou *retirada em lote*, organiza itens em várias instâncias de fluxo de trabalho para processamento. Esta opção está disponível para gatilhos que retornam uma matriz e somente ao trabalhar diretamente na exibição de código. | 
| [operationOptions](#trigger-operation-options) | Cadeia de caracteres | Alguns gatilhos fornecem opções adicionais que permitem alterar o comportamento do gatilho padrão | 
||||| 

## <a name="trigger-types-and-details"></a>Tipos de gatilhos e detalhes  

Cada tipo de gatilho possui uma interface e entradas diferentes que definem o comportamento do gatilho. 

| Tipo de gatilho | DESCRIÇÃO | 
| ------------ | ----------- | 
| [**Recurrence**](#recurrence-trigger) | Acionado com base em um agendamento definido. Você pode definir uma data e hora futura para acionar esse gatilho. Com base na frequência, você também pode especificar os horários e dias para executar o fluxo de trabalho. | 
| [**Request**](#request-trigger)  | Torna o aplicativo lógico em um ponto de extremidade resgatável, também conhecido como gatilho "manual". Por exemplo, consulte [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md). | 
| [**HTTP**](#http-trigger) | Verifica ou *sonda*, um ponto de extremidade da Web de HTTP. O ponto de extremidade HTTP deve estar em conformidade com um contrato de gatilho específico usando um padrão assíncrono "202" ou retornando uma matriz. | 
| [**ApiConnection**](#apiconnection-trigger) | Funciona como o gatilho HTTP, mas usa [APIs gerenciadas pela Microsoft](../connectors/apis-list.md). | 
| [**HTTPWebhook**](#httpwebhook-trigger) | Funciona como o gatilho de Solicitação, mas chama uma URL especificada para registrar e cancelar o registro. |
| [**ApiConnectionWebhook**](#apiconnectionwebhook-trigger) | Funciona como o gatilho HTTPWebhook, mas usa [APIs gerenciadas pela Microsoft](../connectors/apis-list.md). | 
||| 

<a name="recurrence-trigger"></a>

## <a name="recurrence-trigger"></a>Gatilho de recorrência  

Esse gatilho executa com base na recorrência e programação especificadas e fornece uma maneira fácil de executar regularmente um fluxo de trabalho. 

A seguir, é apresentada a definição de gatilho:

```json
"Recurrence": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month",
      "interval": <recurrence-interval-based-on-frequency>,
      "startTime": "<start-date-time-with-format-YYYY-MM-DDThh:mm:ss>",
      "timeZone": "<time-zone>",
      "schedule": {
         // Applies only when frequency is Day or Week. Separate values with commas.
         "hours": [ <one-or-more-hour-marks> ], 
         // Applies only when frequency is Day or Week. Separate values with commas.
         "minutes": [ <one-or-more-minute-marks> ], 
         // Applies only when frequency is Week. Separate values with commas.
         "weekDays": [ "Monday, Tuesday, Wednesday, Thursday, Friday, Saturday, Sunday" ] 
      }
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```
*Obrigatório*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| Recorrência | Objeto JSON | O nome do gatilho, que é um objeto descrito em formato JSON (Javascript Object Notation)  | 
| Tipo | Cadeia de caracteres | O tipo de gatilho, que é "Recorrência" | 
| inputs | Objeto JSON | As entradas do gatilho que definem o comportamento do gatilho | 
| recurrence | Objeto JSON | A frequência e o intervalo que descrevem com que frequência o gatilho aciona |  
| frequência | Cadeia de caracteres | A unidade de tempo que descreve com que frequência o gatilho aciona: "Segundo", "Minuto", "Hora", "Dia", "Semana" ou "Mês" | 
| intervalo | Número inteiro | Um inteiro positivo que descreve com que frequência o gatilho é acionado com base na frequência. <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| startTime | Cadeia de caracteres | A data e hora de início neste formato: <p>AAAA-MM-DDThh:mm:ss se você especificar um fuso horário <p>-ou- <p>AAAA-MM-DDThh:mm:ssZ se você não especificar um fuso horário <p>Por exemplo, se você quiser 18 de setembro de 2017 às 14h00, especifique "2017-09-18T14:00:00" e especifique um fuso horário como "Hora Padrão do Pacífico" ou especifique "2017-09-18T14:00:00Z" sem um fuso horário. <p>**Observação:** a hora de início deve seguir a [especificação de data e hora ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Combined_date_and_time_representations) no [formato de data e hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time), mas sem uma [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Se você não especificar um fuso horário, será necessário adicionar a letra "Z" no final sem espaços. Essa letra "Z" refere-se ao equivalente em [hora náutica](https://en.wikipedia.org/wiki/Nautical_time). <p>Para agendamentos simples, a hora de início é a primeira ocorrência, enquanto que, para agendamentos complexos, o gatilho não é disparado antes da hora de início. Para obter mais informações sobre datas e horas de início, consulte [Criar e agendar tarefas de execução regularmente](../connectors/connectors-native-recurrence.md). | 
| timeZone | Cadeia de caracteres | Aplica-se somente quando você especifica uma hora de início, porque o gatilho não aceita [diferença UTC](https://en.wikipedia.org/wiki/UTC_offset). Especifique o fuso horário que deseja aplicar. | 
| hours | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 23, separados por vírgulas, como as horas do dia nas quais você deseja executar o fluxo de trabalho. <p>Por exemplo, se você especificar "10", "12" e "14", você obterá 10h, 12h e 14h como as marcas de hora. | 
| minutes | Inteiro ou matriz de inteiros | Se você selecionar "Dia" ou "Semana" para `frequency`, poderá selecionar um ou mais números inteiros, de 0 a 59, separados por vírgulas, como os minutos da hora nos quais você deseja executar o fluxo de trabalho. <p>Por exemplo, você pode especificar "30" como a marca de minutos e, usando o exemplo anterior como as horas do dia, você obtém 10h30, 12h30 e 14h30. | 
| weekDays | Cadeia de caracteres ou matriz de cadeia de caracteres | Se você selecionar "Semana" para `frequency`, poderá selecionar um ou mais dias, separados por vírgulas, nos quais deseja executar o fluxo de trabalho: “segunda-feira”, “terça-feira”, “quarta-feira”, “quinta-feira”, “Sexta-feira”, “sábado” e “domingo” | 
| simultaneidade | Objeto JSON | Para gatilhos de sondagem e recorrentes, esse objeto especifica o número máximo de instâncias de fluxo de trabalho que podem ser executadas ao mesmo tempo. Use esse valor para limitar as solicitações recebidas pelos sistemas back-end. <p>Por exemplo, esse valor define o limite de simultaneidade para 10 instâncias: `"concurrency": { "runs": 10 }` | 
| operationOptions | Cadeia de caracteres | A opção `singleInstance` especifica que o gatilho é acionado somente depois que todas as execuções ativas forem concluídas. Consulte [Gatilhos: acionados somente após a conclusão das execuções ativas](#single-instance). | 
|||| 

*Exemplo 1*

Esse gatilho de recorrência básica executa diariamente:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

*Exemplo 2*

É possível especificar uma data e hora de início para acionar o gatilho. Esse gatilho de recorrência inicia na data especificada e, em seguida, é acionado diariamente:

```json
"recurrenceTriggerName": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Day",
      "interval": 1,
      "startTime": "2017-09-18T00:00:00Z"
   }
}
```

*Exemplo 3*

Esse gatilho de recorrência inicia em 9 de setembro de 2017 às 14h00 e é acionado semanalmente às segundas-feiras às 10h30, 12h30 e 14h30, Hora Padrão do Pacífico:

``` json
"myRecurrenceTrigger": {
   "type": "Recurrence",
   "recurrence": {
      "frequency": "Week",
      "interval": 1,
      "schedule": {
         "hours": [ 10, 12, 14 ],
         "minutes": [ 30 ],
         "weekDays": [ "Monday" ]
      },
      "startTime": "2017-09-07T14:00:00",
      "timeZone": "Pacific Standard Time"
   }
}
```

Para obter mais informações e exemplos para esse gatilho, consulte [Criar e agendar tarefas de execução regularmente](../connectors/connectors-native-recurrence.md).

<a name="request-trigger"></a>

## <a name="request-trigger"></a>Gatilho de solicitação

Esse gatilho torna o aplicativo lógico resgatável, criando um ponto de extremidade que pode aceitar solicitações HTTP de entrada. Para chamar esse gatilho, é necessário usar a `listCallbackUrl` API na [API REST do Serviço de Fluxo de Trabalho](https://docs.microsoft.com/rest/api/logic/workflows). Para saber como usar esse gatilho como um ponto de extremidade HTTP, consulte [Chamar, disparar ou aninhar fluxos de trabalho com pontos de extremidade HTTP](../logic-apps/logic-apps-http-endpoint.md).

```json
"manual": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "GET | POST | PUT | PATCH | DELETE | HEAD",
      "relativePath": "<relative-path-for-accepted-parameter>",
      "schema": {
         "type": "object",
         "properties": { 
            "<propertyName>": {
               "type": "<property-type>"
            }
         },
         "required": [ "<required-properties>" ]
      }
   }
}
```

*Obrigatório*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| manual | Objeto JSON | O nome do gatilho, que é um objeto descrito em formato JSON (Javascript Object Notation)  | 
| Tipo | Cadeia de caracteres | O tipo de gatilho, que é "Solicitação" | 
| kind | Cadeia de caracteres | O tipo de solicitação, que é "Http" | 
| inputs | Objeto JSON | As entradas do gatilho que definem o comportamento do gatilho | 
|||| 

*Opcional*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| estático | Cadeia de caracteres | O método que as solicitações devem usar para chamar o gatilho: "GET", "PUT", "POST", "PATCH", "DELETE" ou "HEAD" |
| relativePath | Cadeia de caracteres | O caminho relativo para o parâmetro que a URL do ponto de extremidade HTTP aceita | 
| schema | Objeto JSON | O esquema JSON que descreve e valida a carga, ou entradas, que o gatilho recebe da solicitação de entrada. Esse esquema ajuda as ações subsequentes do fluxo de trabalho a conhecer as propriedades para referência. | 
| propriedades | Objeto JSON | Uma ou mais propriedades no esquema JSON que descreve a carga | 
| obrigatório | Matriz | Uma ou mais propriedades que exigem valores | 
|||| 

*Exemplo*

Este gatilho de solicitação especifica que uma solicitação de entrada usa o método HTTP POST para chamar o gatilho e um esquema que valida a entrada da solicitação de entrada: 

```json
"myRequestTrigger": {
   "type": "Request",
   "kind": "Http",
   "inputs": {
      "method": "POST",
      "schema": {
         "type": "Object",
         "properties": {
            "customerName": {
               "type": "String"
            },
            "customerAddress": { 
               "type": "Object",
               "properties": {
                  "streetAddress": {
                     "type": "String"
                  },
                  "city": {
                     "type": "String"
                  }
               }
            }
         }
      }
   }
} 
```

<a name="http-trigger"></a>

## <a name="http-trigger"></a>Gatilho HTTP  

Esse gatilho sonda um ponto de extremidade especificado e verifica a resposta. A resposta determina se o fluxo de trabalho deve executar ou não. O objeto JSON `inputs` inclui e requer os parâmetros `method` e `uri` necessários para construir a chamada HTTP:

```json
"HTTP": {
   "type": "Http",
   "inputs": {
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "uri": "<HTTP-or-HTTPS-endpoint-to-poll>",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": <recurrence-interval-based-on-frequency>
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Obrigatório*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| HTTP | Objeto JSON | O nome do gatilho, que é um objeto descrito em formato JSON (Javascript Object Notation)  | 
| Tipo | Cadeia de caracteres | O tipo de gatilho, que é "Http" | 
| inputs | Objeto JSON | As entradas do gatilho que definem o comportamento do gatilho | 
| estático | sim | Cadeia de caracteres | O método HTTP para pesquisar o ponto de extremidade especificado: "GET", "PUT", "POST", "PATCH", "DELETE" ou "HEAD" | 
| uri | sim| Cadeia de caracteres | A URL do ponto de extremidade HTTP ou HTTPS que o gatilho verifica ou sonda <p>Tamanho máximo da cadeia de caracteres: 2 KB | 
| recurrence | Objeto JSON | A frequência e o intervalo que descrevem com que frequência o gatilho aciona |  
| frequência | Cadeia de caracteres | A unidade de tempo que descreve com que frequência o gatilho aciona: "Segundo", "Minuto", "Hora", "Dia", "Semana" ou "Mês" | 
| intervalo | Número inteiro | Um inteiro positivo que descreve com que frequência o gatilho é acionado com base na frequência. <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| consultas | Objeto JSON | Quaisquer parâmetros de consulta que você deseja incluir com a URL <p>Por exemplo, esse elemento adiciona a cadeia de caracteres de consulta `?api-version=2015-02-01` à URL: <p>`"queries": { "api-version": "2015-02-01" }` <p>Resultado: `https://contoso.com?api-version=2015-02-01` | 
| headers | Objeto JSON | Um ou mais cabeçalhos para enviar com a solicitação <p>Por exemplo, para definir o idioma e o tipo de uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Objeto JSON | A carga (dados) para enviar ao ponto de extremidade | 
| Autenticação | Objeto JSON | O método que a solicitação de entrada deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). Além do Agendador, a propriedade `authority` tem suporte. Quando não especificado, o valor padrão é `https://login.windows.net`, mas é possível usar um valor diferente, como`https://login.windows\-ppe.net`. | 
| retryPolicy | Objeto JSON | Esse objeto personaliza o comportamento de nova tentativa para erros intermitentes com códigos de status 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| simultaneidade | Objeto JSON | Para gatilhos de sondagem e recorrentes, esse objeto especifica o número máximo de instâncias de fluxo de trabalho que podem ser executadas ao mesmo tempo. Use esse valor para limitar as solicitações recebidas pelos sistemas back-end. <p>Por exemplo, esse valor define o limite de simultaneidade para 10 instâncias: <p>`"concurrency": { "runs": 10 }` | 
| operationOptions | Cadeia de caracteres | A opção `singleInstance` especifica que o gatilho é acionado somente depois que todas as execuções ativas forem concluídas. Consulte [Gatilhos: acionados somente após a conclusão das execuções ativas](#single-instance). | 
|||| 

Para trabalhar bem com o aplicativo lógico, o gatilho HTTP requer que a API HTTP esteja em conformidade com um padrão específico. O gatilho HTTP reconhece essas propriedades:  
  
| Response | Obrigatório | DESCRIÇÃO | 
| -------- | -------- | ----------- |  
| Código de status | sim | O código de status "200 OK" inicia uma execução. Qualquer outro código de status não inicia uma execução. | 
| Cabeçalho Retry-after | Não  | O número de segundos até o aplicativo lógico pesquisar o ponto de extremidade novamente | 
| Cabeçalho do local | Não  | A URL a chamar no próximo intervalo de sondagem. Se não for especificada, a URL original será usada. | 
|||| 

*Exemplo de comportamentos para diferentes solicitações*

| Código de status | Tentar novamente após | Comportamento | 
| ----------- | ----------- | -------- | 
| 200 | {none} | Execute o fluxo de trabalho e verifique novamente se há mais dados após a recorrência definida. | 
| 200 | 10 segundos | Execute o fluxo de trabalho e verifique novamente se há mais dados após 10 segundos. |  
| 202 | 60 segundos | Não dispara o fluxo de trabalho. A próxima tentativa ocorre em um minuto, sujeito à recorrência definida. Se a recorrência definida for inferior a um minuto, o cabeçalho retry-after terá precedência. Caso contrário, a recorrência definida será usada. | 
| 400 | {none} | Solicitação inválida, não execute o fluxo de trabalho. Se nenhum `retryPolicy` for definido, a política padrão será usada. Após a quantidade de novas tentativas ter sido atingida, o gatilho buscará novamente os dados após a recorrência definida. | 
| 500 | {none}| Erro do servidor, não execute o fluxo de trabalho. Se nenhum `retryPolicy` for definido, a política padrão será usada. Após a quantidade de novas tentativas ter sido atingida, o gatilho buscará novamente os dados após a recorrência definida. | 
|||| 

### <a name="http-trigger-outputs"></a>Saídas do gatilho HTTP

| Nome do elemento | type | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| headers | Objeto JSON | Os cabeçalhos da resposta HTTP | 
| body | Objeto JSON | O corpo da resposta HTTP | 
|||| 

<a name="apiconnection-trigger"></a>

## <a name="apiconnection-trigger"></a>Gatilho APIConnection  

Esse gatilho funciona como o [gatilho HTTP](#http-trigger), mas usa [APIs gerenciadas pela Microsoft](../connectors/apis-list.md) para que os parâmetros desse gatilho sejam diferentes. 

Essa é a definição de gatilho, ainda que muitas seções sejam opcionais, desse modo, o comportamento do gatilho depende da inclusão ou não das seções:

```json
"<APIConnectionTriggerName>": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "<managed-API-endpoint-URL>"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         },
      },
      "method": "GET | PUT | POST | PATCH | DELETE | HEAD",
      "queries": "<query-parameters>",
      "headers": { "<headers-for-request>" },
      "body": { "<payload-to-send>" },
      "authentication": { "<authentication-method>" },
      "retryPolicy": {
          "type": "<retry-policy-type>",
          "interval": "<retry-interval>",
          "count": <number-retry-attempts>
      }
   },
   "recurrence": {
      "frequency": "Second | Minute | Hour | Day | Week | Month | Year",
      "interval": "<recurrence-interval-based-on-frequency>"
   },
   "runtimeConfiguration": {
      "concurrency": {
         "runs": <maximum-number-for-concurrently-running-workflow-instances>
      }
   },
   "operationOptions": "singleInstance"
}
```

*Obrigatório*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| *APIConnectionTriggerName* | Objeto JSON | O nome do gatilho, que é um objeto descrito em formato JSON (Javascript Object Notation)  | 
| Tipo | Cadeia de caracteres | O tipo de gatilho, que é "ApiConnection" | 
| inputs | Objeto JSON | As entradas do gatilho que definem o comportamento do gatilho | 
| host | Objeto JSON | O objeto JSON que descreve o gateway do host e a ID da API gerenciada <p>O objeto JSON `host` tem esses elementos: `api` e `connection` | 
| api | Objeto JSON | A URL do ponto de extremidade da API gerenciada: <p>`"runtimeUrl": "<managed-API-endpoint-URL>"` | 
| connection | Objeto JSON | O nome da conexão da API gerenciada usada pelo fluxo de trabalho, que deve incluir uma referência a um parâmetro denominado `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>'].name"` | 
| estático | Cadeia de caracteres | O método HTTP para comunicar-se com a API gerenciada: "GET", "PUT", "POST", "PATCH", "DELETE" ou "HEAD" | 
| recurrence | Objeto JSON | A frequência e o intervalo que descrevem com que frequência o gatilho aciona |  
| frequência | Cadeia de caracteres | A unidade de tempo que descreve com que frequência o gatilho aciona: "Segundo", "Minuto", "Hora", "Dia", "Semana" ou "Mês" | 
| intervalo | Número inteiro | Um inteiro positivo que descreve com que frequência o gatilho é acionado com base na frequência. <p>Aqui estão os intervalos mínimos e máximos: <p>– Mês: 1 a 16 meses </br>–Dia: 1 a 500 dias </br>– Hora: 1 a 12.000 horas </br>– Minuto: 1 a 72.000 minutos </br>– Segundo: 1 a 9.999.999 segundos<p>Por exemplo, se o intervalo for 6 e a frequência for "Mês", a recorrência será a cada 6 meses. | 
|||| 

*Opcional*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| consultas | Objeto JSON | Quaisquer parâmetros de consulta que você deseja incluir com a URL <p>Por exemplo, esse elemento adiciona a cadeia de caracteres de consulta `?api-version=2015-02-01` à URL: <p>`"queries": { "api-version": "2015-02-01" }` <p>Resultado: `https://contoso.com?api-version=2015-02-01` | 
| headers | Objeto JSON | Um ou mais cabeçalhos para enviar com a solicitação <p>Por exemplo, para definir o idioma e o tipo de uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Objeto JSON | O objeto JSON que descreve a carga (dados) a ser enviada para a API gerenciada | 
| Autenticação | Objeto JSON | O método que uma solicitação de entrada deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | Objeto JSON | Esse objeto personaliza o comportamento de nova tentativa para erros intermitentes com códigos de status 4xx ou 5xx: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| simultaneidade | Objeto JSON | Para gatilhos de sondagem e recorrentes, esse objeto especifica o número máximo de instâncias de fluxo de trabalho que podem ser executadas ao mesmo tempo. Use esse valor para limitar as solicitações recebidas pelos sistemas back-end. <p>Por exemplo, esse valor define o limite de simultaneidade para 10 instâncias: `"concurrency": { "runs": 10 }` | 
| operationOptions | Cadeia de caracteres | A opção `singleInstance` especifica que o gatilho é acionado somente depois que todas as execuções ativas forem concluídas. Consulte [Gatilhos: acionados somente após a conclusão das execuções ativas](#single-instance). | 
||||

*Exemplo*

```json
"Create_daily_report": {
   "type": "ApiConnection",
   "inputs": {
      "host": {
         "api": {
            "runtimeUrl": "https://myReportsRepo.example.com/"
         },
         "connection": {
            "name": "@parameters('$connections')['<connection-name>'].name"
         }     
      },
      "method": "POST",
      "body": {
         "category": "statusReports"
      }  
   },
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

### <a name="apiconnection-trigger-outputs"></a>Saídas do gatilho APIConnection
 
| Nome do elemento | type | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| headers | Objeto JSON | Os cabeçalhos da resposta HTTP | 
| body | Objeto JSON | O corpo da resposta HTTP | 
|||| 

<a name="httpwebhook-trigger"></a>

## <a name="httpwebhook-trigger"></a>Gatilho HTTPWebhook  

Esse gatilho funciona como o [Gatilho de solicitação](#request-trigger), criando um ponto de extremidade resgatável para o aplicativo lógico. No entanto, esse gatilho também chama uma URL de ponto de extremidade especificada para registrar ou cancelar o registro de uma assinatura. Você pode especificar os limites em um gatilho webhook da mesma maneira que os [Limites Assíncronos do HTTP](#asynchronous-limits). 

Essa é a definição de gatilho, embora muitas seções sejam opcionais e o comportamento do gatilho depende das seções que você usa ou omite:

```json
"HTTP_Webhook": {
    "type": "HttpWebhook",
    "inputs": {
        "subscribe": {
            "method": "POST",
            "uri": "<subscribe-to-endpoint-URL>",
            "headers": { "<headers-for-request>" },
            "body": {
                "hub.callback": "@{listCallbackUrl()}",
                "hub.mode": "subscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {},
            "retryPolicy": {}
        },
        "unsubscribe": {
            "method": "POST",
            "url": "<unsubscribe-from-endpoint-URL>",
            "body": {
                "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
                "hub.mode": "unsubscribe",
                "hub.topic": "<subscription-topic>"
            },
            "authentication": {}
        }
    },
}
```

*Obrigatório*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| HTTP_Webhook | Objeto JSON | O nome do gatilho, que é um objeto descrito em formato JSON (Javascript Object Notation)  | 
| Tipo | Cadeia de caracteres | O tipo de gatilho, que é "HttpWebhook" | 
| inputs | Objeto JSON | As entradas do gatilho que definem o comportamento do gatilho | 
| assinar | Objeto JSON| A solicitação de saída para chamar e executar o registro inicial quando o gatilho é criado. Essa chamada acontece para que o gatilho possa começar a ouvir eventos no ponto de extremidade. Para obter mais informações, consulte [assinar e cancelar assinatura](#subscribe-unsubscribe). | 
| estático | Cadeia de caracteres | O método HTTP usado para a solicitação de assinatura: "GET", "PUT", "POST", "PATCH", "DELETE" ou "HEAD" | 
| uri | Cadeia de caracteres | A URL do ponto de extremidade para onde enviar a solicitação de assinatura | 
|||| 

*Opcional*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| cancelar assinatura | Objeto JSON | A solicitação de saída para chamar e cancelar automaticamente a assinatura quando uma operação torna o gatilho inválido. Para obter mais informações, consulte [assinar e cancelar assinatura](#subscribe-unsubscribe). | 
| estático | Cadeia de caracteres | O método HTTP a ser usado para a solicitação de cancelamento: "GET", "PUT", "POST", "PATCH", "DELETE" ou "HEAD" | 
| uri | Cadeia de caracteres | A URL do ponto de extremidade para onde enviar a solicitação de cancelamento | 
| body | Objeto JSON | O objeto JSON que descreve a carga (dados) da solicitação de assinatura ou cancelamento | 
| Autenticação | Objeto JSON | O método que uma solicitação de entrada deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
| retryPolicy | Objeto JSON | Esse objeto personaliza o comportamento de nova tentativa para erros intermitentes com códigos de status 4xx ou 5xx: <p>`"retryPolicy": { "type": "<retry-policy-type>", "interval": "<retry-interval>", "count": <number-retry-attempts> }` <p>Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
|||| 

*Exemplo*

```json
"myAppSpotTrigger": {
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
      },
      "unsubscribe": {
         "method": "POST",
         "url": "https://pubsubhubbub.appspot.com/subscribe",
         "body": {
            "hub.callback": "@{workflow().endpoint}@{listCallbackUrl()}",
            "hub.mode": "unsubscribe",
            "hub.topic": "https://pubsubhubbub.appspot.com/articleCategories/technology"
         },
      }
   },
}
```

<a name="subscribe-unsubscribe"></a>

### <a name="subscribe-and-unsubscribe"></a>`subscribe` e `unsubscribe`

A chamada `subscribe` ocorre quando o fluxo de trabalho é alterado de alguma forma, por exemplo, quando as credenciais são renovadas ou quando os parâmetros de entrada do gatilho alteram. A chamada usa os mesmos parâmetros das ações HTTP padrão. 
 
A chamada `unsubscribe` ocorre automaticamente quando uma operação torna o gatilho HTTPWebhook inválido, por exemplo:

* Excluir ou desabilitar o gatilho. 
* excluir ou desabilitar o fluxo de trabalho. 
* excluir ou desabilitar a assinatura. 

Para dar suporte a essas chamadas, a função `@listCallbackUrl()` retorna uma "URL de retorno de chamada" exclusivo para esse gatilho. Essa URL representa um identificador exclusivo para os pontos de extremidade que usam a API REST do serviço. Os parâmetros dessa função são os mesmos do gatilho HTTP.

### <a name="httpwebhook-trigger-outputs"></a>Saídas do gatilho HTTPWebhook

| Nome do elemento | type | DESCRIÇÃO |
| ------------ | ---- | ----------- |
| headers | Objeto JSON | Os cabeçalhos da resposta HTTP | 
| body | Objeto JSON | O corpo da resposta HTTP | 
|||| 

<a name="apiconnectionwebhook-trigger"></a>

## <a name="apiconnectionwebhook-trigger"></a>Gatilho ApiConnectionWebhook

Esse gatilho funciona como o [gatilho HTTPWebhook](#httpwebhook-trigger), mas usa [APIs gerenciadas pela Microsoft](../connectors/apis-list.md). 

A seguir, é apresentada a definição de gatilho:

```json
"<ApiConnectionWebhookTriggerName>": {
   "type": "ApiConnectionWebhook",
   "inputs": {
      "host": {
         "connection": {
            "name": "@parameters('$connections')['<connection-name>']['connectionId']"
         }
      },        
      "body": {
          "NotificationUrl": "@{listCallbackUrl()}"
      },
      "queries": "<query-parameters>"
   }
}
```

*Obrigatório*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| <*ApiConnectionWebhookTriggerName*> | Objeto JSON | O nome do gatilho, que é um objeto descrito em formato JSON (Javascript Object Notation)  | 
| Tipo | Cadeia de caracteres | O tipo de gatilho, que é "ApiConnectionWebhook" | 
| inputs | Objeto JSON | As entradas do gatilho que definem o comportamento do gatilho | 
| host | Objeto JSON | O objeto JSON que descreve o gateway do host e a ID da API gerenciada <p>O objeto JSON `host` tem esses elementos: `api` e `connection` | 
| connection | Objeto JSON | O nome da conexão da API gerenciada usada pelo fluxo de trabalho, que deve incluir uma referência a um parâmetro denominado `$connection`: <p>`"name": "@parameters('$connections')['<connection-name>']['connectionId']"` | 
| body | Objeto JSON | O objeto JSON que descreve a carga (dados) a ser enviada para a API gerenciada | 
| NotificationUrl | Cadeia de caracteres | Retorna uma "URL de retorno de chamada" exclusivo para esse gatilho que a API gerenciada pode usar | 
|||| 

*Opcional*

| Nome do elemento | type | DESCRIÇÃO | 
| ------------ | ---- | ----------- | 
| consultas | Objeto JSON | Quaisquer parâmetros de consulta que você deseja incluir com a URL <p>Por exemplo, esse elemento adiciona a cadeia de caracteres de consulta `?folderPath=Inbox` à URL: <p>`"queries": { "folderPath": "Inbox" }` <p>Resultado: `https://<managed-API-URL>?folderPath=Inbox` | 
|||| 

<a name="trigger-conditions"></a>

## <a name="triggers-conditions"></a>Gatilhos: condições

Para qualquer gatilho, é possível incluir uma matriz com uma ou mais condições que determinam se o fluxo de trabalho deve ser executado ou não. Neste exemplo, o gatilho do relatório é acionado apenas enquanto o parâmetro `sendReports` do fluxo de trabalho estiver definido como true. 

```json
"myDailyReportTrigger": {
   "type": "Recurrence",
   "conditions": [ {
      "expression": "@parameters('sendReports')"
   } ],
   "recurrence": {
      "frequency": "Day",
      "interval": 1
   }
}
```

Além disso, as condições podem referenciar o código de status do gatilho. Por exemplo, suponha que você queira iniciar um fluxo de trabalho somente quando o site da Web retornar um código de status "500":

``` json
"conditions": [ {
   "expression": "@equals(triggers().code, 'InternalServerError')"  
} ]  
```  

> [!NOTE]
> Por padrão, um gatilho é acionado somente no recebimento de uma resposta "200 OK". Quando uma expressão referenciar o código de status de um gatilho de alguma forma, o comportamento padrão do gatilho será substituído. Portanto, se você quiser que o gatilho seja acionado para vários códigos de status, por exemplo, o código de status 200 e o código de status 201, inclua essa instrução como sua condição: 
>
> `@or(equals(triggers().code, 200),equals(triggers().code, 201))` 

<a name="split-on-debatch"></a>

## <a name="triggers-split-an-array-into-multiple-runs"></a>Gatilhos: dividir uma matriz em várias execuções

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
        "interval": 1
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

<a name="trigger-operation-options"></a>

## <a name="triggers-operation-options"></a>Gatilhos: Opções de operação

Esses gatilhos fornecem mais opções que permitem alterar o comportamento padrão.

| Gatilho | Opção de operação | DESCRIÇÃO |
|---------|------------------|-------------|
| [Recurrence](#recurrence-trigger), <br>[HTTP](#http-trigger), <br>[ApiConnection](#apiconnection-trigger) | singleInstance | Aciona o gatilho somente após a conclusão de todas as execuções ativas. |
||||

<a name="single-instance"></a>

### <a name="triggers-fire-only-after-active-runs-finish"></a>Gatilhos: acionados somente após a conclusão das execuções ativas

Para gatilhos em que você pode definir a recorrência, é possível especificar que o gatilho acione somente depois que todas as execuções ativas tiverem sido concluídas. Se uma recorrência agendada ocorrer enquanto uma instância de fluxo de trabalho estiver em execução, o gatilho irá ignorar e aguardar até a próxima recorrência agendada antes de verificar novamente. Por exemplo: 

```json
"myRecurringTrigger": {
    "type": "Recurrence",
    "recurrence": {
        "frequency": "Hour",
        "interval": 1,
    },
    "operationOptions": "singleInstance"
}
```

<a name="actions-overview"></a>

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
| consultas | Não  | Objeto JSON | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto JSON | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto JSON | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto JSON | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Não  | Cadeia de caracteres | Define o conjunto de comportamentos especiais a substituir. | 
| Autenticação | Não  | Objeto JSON | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). <p>Além do Agendador, há mais uma propriedade com suporte: `authority`. Por padrão, esse valor é `https://login.windows.net` quando não especificado, mas você pode usar um valor diferente, como `https://login.windows\-ppe.net`. | 
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
| host | sim | Objeto JSON | Representa as informações do conector, como `runtimeUrl` e uma referência para o objeto de conexão. | 
| estático | sim | Cadeia de caracteres | Usa um dos seguintes métodos HTTP: “GET”, “POST”, “PUT”, “DELETE”, “PATCH” ou “HEAD” | 
| caminho | sim | Cadeia de caracteres | O caminho da operação da API | 
| consultas | Não  | Objeto JSON | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto JSON | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto JSON | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto JSON | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Não  | Cadeia de caracteres | Define o conjunto de comportamentos especiais a substituir. | 
| Autenticação | Não  | Objeto JSON | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
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
| host | sim | Objeto JSON | Representa as informações do conector, como `runtimeUrl` e uma referência para o objeto de conexão. | 
| caminho | sim | Cadeia de caracteres | O caminho da operação da API | 
| consultas | Não  | Objeto JSON | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto JSON | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto JSON | Representa a carga enviada para o ponto de extremidade. | 
| retryPolicy | Não  | Objeto JSON | Use esse objeto para personalizar o comportamento de repetição para os erros 4xx ou 5xx. Para mais informações, consulte [Políticas de repetição](../logic-apps/logic-apps-exception-handling.md). | 
| operationsOptions | Não  | Cadeia de caracteres | Define o conjunto de comportamentos especiais a substituir. | 
| Autenticação | Não  | Objeto JSON | Representa o método que a solicitação deve usar para autenticação. Para obter mais informações, consulte [Autenticação de saída do Agendador](../scheduler/scheduler-outbound-authentication.md). |
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
| consultas | Não  | Objeto JSON | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto JSON | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto JSON | Representa a carga enviada para o ponto de extremidade. | 
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
| runError | Não  | Objeto JSON | Os detalhes do erro. Com suporte somente quando `runStatus` estiver definido como `Failed`. |
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
| until | Não  | Objeto JSON | A duração de espera com base em um ponto no tempo | 
| until carimbo data/hora | sim | Cadeia de caracteres | O ponto no tempo no [formato de hora UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time) quando a espera expira | 
| intervalo | Não  | Objeto JSON | A duração de espera com base na contagem e unidade do intervalo | 
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
| consultas | Não  | Objeto JSON | Representa todos os parâmetros que você deseja incluir na URL. <p>Por exemplo, `"queries": { "api-version": "2015-02-01" }` adiciona `?api-version=2015-02-01` à URL. | 
| headers | Não  | Objeto JSON | Representa cada cabeçalho que é enviado na solicitação. <p>Por exemplo, para definir o idioma e o tipo em uma solicitação: <p>`"headers": { "Accept-Language": "en-us", "Content-Type": "application/json" }` | 
| body | Não  | Objeto JSON | Representa o conteúdo enviado para o ponto de extremidade. | 
||||| 

As saídas da ação baseiam-se no que foi definido na ação `Response` no fluxo de trabalho filho. Se o fluxo de trabalho secundário não definir uma ação `Response`, as saídas ficarão vazias.

## <a name="collection-actions-overview"></a>Visão geral de ações da coleção

Para ajudá-lo a controlar a execução do fluxo de trabalho, as ações de coleção podem incluir outras ações. Você pode se referir diretamente às ações de referência em uma coleção fora da coleção. Por exemplo, se você definir uma ação `Http` em um escopo, `@body('http')` ainda será válido em qualquer lugar no fluxo de trabalho. Além disso, as ações em uma coleção podem apenas "correr atrás" de outras ações na mesma coleção.

## <a name="if-action"></a>Ação If

Essa ação, que é uma instrução condicional, permite que você avalie uma condição e execute uma ramificação caso a expressão seja avaliada como true. Se a condição for avaliada como verdadeira, a condição será marcada com o status "Com êxito". Ações que estão nos objetos `actions` ou `else` são avaliadas quanto a estes valores:

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
| Ações | sim | Objeto JSON | As ações internas para executar quando `expression` for avaliado como `true` | 
| expressão | sim | Cadeia de caracteres | A expressão a avaliar |
| else | Não  | Objeto JSON | As ações internas para executar quando `expression` for avaliado como `false` |
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
      "myCase1": {
         "actions": {
           "myAction1": {}
         },
         "case": "<result1>"
      },
      "myCase2": {
         "actions": {
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
| cases | sim | Objeto JSON | Contém os conjuntos de ações internas que são executadas com base no resultado da expressão. | 
| case | sim | Cadeia de caracteres | O valor para corresponder ao resultado | 
| Ações | sim | Objeto JSON | As ações internas que são executadas para o caso que corresponde ao resultado da expressão | 
| padrão | Não  | Objeto JSON | As ações internas que são executadas quando nenhum caso corresponde ao resultado | 
||||| 

Por exemplo: 

``` json
"myApprovalEmailAction": {
   "type": "Switch",
   "expression": "@body('Send_approval_email')?['SelectedOption']",
   "cases": {
      "Case": {
         "actions": {
           "Send_an_email": {...}
         },
         "case": "Approve"
      },
      "Case_2": {
         "actions": {
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
| Ações | sim | Objeto JSON | As ações internas a serem executadas dentro do loop | 
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
| Ações | sim | Objeto JSON | As ações internas a serem executadas dentro do loop | 
| expressão | sim | Cadeia de caracteres | A expressão a avaliar após cada iteração | 
| limite | sim | Objeto JSON | Os limites do loop. Deve definir, pelo menos, um limite. | 
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
| Ações | sim | Objeto JSON | As ações internas para serem executadas dentro do escopo |
||||| 

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre [Linguagem de Definição de Fluxo de Dados](../logic-apps/logic-apps-workflow-definition-language.md)
* Saiba mais sobre [API REST do fluxo de trabalho](https://docs.microsoft.com/rest/api/logic/workflows)