---
title: Gatilho de temporizador do Azure Functions | Microsoft Docs
description: Entenda como usar gatilhos de temporizador no Azure Functions.
services: functions
documentationcenter: na
author: christopheranderson
manager: erikre
editor: ''
tags: ''
keywords: azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor

ms.service: functions
ms.devlang: multiple
ms.topic: reference
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 08/22/2016
ms.author: chrande; glenga

---
# Gatilho de temporizador do Azure Functions
[!INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e codificar gatilhos de temporizador no Azure Functions. Os gatilhos de temporizador chamam funções com base em uma agenda, única ou recorrente.

[!INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

## function.json para gatilho de temporizador
O arquivo *function.json* fornece uma expressão de agendamento. Por exemplo, o agendamento a seguir executa a função a cada minuto:

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "name": "myTimer",
      "type": "timerTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

O gatilho de temporizador lida com a expansão de várias instâncias automaticamente: apenas uma única instância de uma função específica de temporizador será executada em todas as instâncias.

## Formato de expressão de agendamento
A expressão de agendamento é uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui 6 campos: `{second} {minute} {hour} {day} {month} {day of the week}`.

Observe que muitas das expressões cron encontradas online omitem o campo {segundo} e, portanto, se você copiar de um deles, terá de ajustar o campo extra.

Veja alguns outros exemplos de expressão de agendamento:

Para disparar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *"
```

Para disparar uma vez na parte superior de cada hora:

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

## Exemplo de código C# de gatilho de temporizador
Este exemplo de código G# grava um único log sempre que a função é disparada.

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log)
{
    log.Info($"C# Timer trigger function executed at: {DateTime.Now}");    
}
```

## Próximas etapas
[!INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0824_2016-->