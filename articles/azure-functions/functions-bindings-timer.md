---
title: Gatilho de temporizador do Azure Functions
description: Entenda como usar gatilhos de temporizador no Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: cfowler
editor: 
tags: 
keywords: "azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"
ms.assetid: d2f013d1-f458-42ae-baf8-1810138118ac
ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 02/27/2017
ms.author: glenga
ms.custom: 
ms.openlocfilehash: 2a62d70b22081e45bc318dd9fb624b37cf7069e3
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2017
---
# <a name="azure-functions-timer-trigger"></a>Gatilho de temporizador do Azure Functions

Este artigo explica como trabalhar com gatilhos de temporizador no Azure Functions. Um gatilho de temporizador permite executar uma função em uma agenda. 

[!INCLUDE [intro](../../includes/functions-bindings-intro.md)]

## <a name="example"></a>Exemplo

Consulte o exemplo específico a um idioma:

* [Pré-compilado C#](#trigger---c-example)
* [Script C#](#trigger---c-script-example)
* [F#](#trigger---f-example)
* [JavaScript](#trigger---javascript-example)

### <a name="c-example"></a>Exemplo de C#

O exemplo a seguir mostra uma [função C# pré-compilada](functions-dotnet-class-library.md) executada a cada cinco minutos:

```cs
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");
}
```

### <a name="c-script-example"></a>Exemplo 2 de C# script

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função C# script](functions-reference-csharp.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código de script do C#:

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    if(myTimer.IsPastDue)
    {
        log.Info("Timer is running late!");
    }
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}" );  
}
```

### <a name="f-example"></a>Exemplo de F#

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função F# script](functions-reference-fsharp.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código F# script:

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter ) =
    if (myTimer.IsPastDue) then
        log.Info("F# function is running late.")
    let now = DateTime.Now.ToLongTimeString()
    log.Info(sprintf "F# function executed at %s!" now)
```

### <a name="javascript-example"></a>Exemplo de JavaScript

O exemplo a seguir mostra uma associação de gatilho de temporizador em um arquivo *function.json* e uma [função JavaScript](functions-reference-node.md) que usa a associação. A função grava um log que indica se esta chamada de função deve-se a uma ocorrência de agendamento ausente.

Aqui estão os dados de associação no arquivo *function.json*:

```json
{
    "schedule": "0 */5 * * * *",
    "name": "myTimer",
    "type": "timerTrigger",
    "direction": "in"
}
```

Aqui está o código F# script:

```JavaScript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();

    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    context.log('Node.js timer trigger function ran!', timeStamp);   

    context.done();
};
```

## <a name="attributes-for-precompiled-c"></a>Atributos para C# pré-compilado

Para funções [C# pré-compiladas](functions-dotnet-class-library.md), use o [TimerTriggerAttribute](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerTriggerAttribute.cs), definido no pacote NuGet [Microsoft.Azure.WebJobs.Extensions](http://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions).

O construtor do atributo usa a expressão CRON, conforme mostrado no exemplo a seguir:

```csharp
[FunctionName("TimerTriggerCSharp")]
public static void Run([TimerTrigger("0 */5 * * * *")]TimerInfo myTimer, TraceWriter log)
 ```

Você pode especificar um `TimeSpan` em vez de uma expressão CRON, se seu aplicativo de função for executado em um plano do Serviço de Aplicativo (não em um plano de Consumo).

## <a name="configuration"></a>Configuração

A tabela a seguir explica as propriedades de configuração de associação que você define no arquivo *function.json* e no atributo `TimerTrigger`.

|Propriedade function.json | Propriedade de atributo |Descrição|
|---------|---------|----------------------|
|**tipo** | n/d | Deve ser definido como "timerTrigger". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure.|
|**direction** | n/d | Deve ser definido como "in". Essa propriedade é definida automaticamente quando você cria o gatilho no portal do Azure. |
|**name** | n/d | O nome da variável que representa o objeto de temporizador no código de função. | 
|**schedule**|**ScheduleExpression**|No plano de Consumo, você pode definir agendas com uma expressão CRON. Se você estiver usando um Plano do Serviço de Aplicativo, também pode usar uma `TimeSpan` cadeia de caracteres. As seções a seguir explicam as expressões CRON. Você pode colocar a expressão de agenda em uma configuração de aplicativo e definir essa propriedade como um valor inserido em **%** símbolos, como neste exemplo: "%NameOfAppSettingWithCRONExpression%". Quando você estiver desenvolvendo localmente, as configurações de aplicativo entram em valores do [arquivo local.settings.json](functions-run-local.md#local-settings-file).|

### <a name="cron-format"></a>Formato CRON 

Uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) para o gatilho de temporizador do Azure Functions inclui esses seis campos: 

```
{second} {minute} {hour} {day} {month} {day-of-week}
```

>[!NOTE]   
>Muitas das expressões CRON encontradas online omitem o campo `{second}`. Se você copiar de um deles, adicione o campo `{second}` ausente.

### <a name="cron-time-zones"></a>Fusos horários CRON

O fuso horário padrão usado com as expressões CRON é a Hora Universal Coordenada (UTC). Para que a expressão CRON se baseie em outro fuso horário, crie uma nova configuração de aplicativo para o aplicativo de funções denominada `WEBSITE_TIME_ZONE`. Defina o valor para o nome do fuso horário desejado, conforme mostrado no [Índice de fuso horário da Microsoft](https://technet.microsoft.com/library/cc749073(v=ws.10).aspx). 

Por exemplo, a *Hora padrão da costa leste dos EUA* é UTC-05:00. Para que o timer dispare às 10:00 AM EST diariamente, use a seguinte expressão CRON que considera o fuso horário UTC:

```json
"schedule": "0 0 15 * * *",
``` 

Como alternativa, você pode adicionar uma nova configuração de aplicativo para seu aplicativo de funções denominada `WEBSITE_TIME_ZONE` e definir o valor como **Horário padrão da costa leste dos EUA**.  Em seguida, a seguinte expressão CRON poderia ser usada para 10:00 EST: 

```json
"schedule": "0 0 10 * * *",
``` 
### <a name="cron-examples"></a>Exemplos de CRON

Aqui estão alguns exemplos de expressões CRON, que você pode usar para o gatilho de temporizador no Azure Functions. 

Para disparar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *"
```

Para disparar uma vez a cada hora:

```json
"schedule": "0 0 * * * *",
```

Para disparar uma vez a cada duas horas:

```json
"schedule": "0 0 */2 * * *",
```

Para disparar uma vez a cada hora das 9h às 17h:

```json
"schedule": "0 0 9-17 * * *",
```

Para disparar às 9h30 todos os dias:

```json
"schedule": "0 30 9 * * *",
```

Para disparar às 9h30 todos os dias da semana:

```json
"schedule": "0 30 9 * * 1-5",
```

## <a name="usage"></a>Uso

Quando uma função de gatilho de timer é chamada, o [objeto timer](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions/Extensions/Timers/TimerInfo.cs) é passado para a função. O JSON a seguir é uma representação de exemplo do objeto timer. 

```json
{
    "Schedule":{
    },
    "ScheduleStatus": {
        "Last":"2016-10-04T10:15:00.012699+00:00",
        "Next":"2016-10-04T10:20:00+00:00"
    },
    "IsPastDue":false
}
```

## <a name="scale-out"></a>Escalabilidade

O gatilho de timer dá suporte à expansão em várias instâncias. Uma única instância de uma função específica de timer é executada em todas as instâncias.

## <a name="next-steps"></a>Próximas etapas

> [!div class="nextstepaction"]
> [Vá para um guia de início rápido que use um gatilho de temporizador](functions-create-scheduled-function.md)

> [!div class="nextstepaction"]
> [Aprenda mais sobre gatilhos e de associações do Azure Functions](functions-triggers-bindings.md)
