<properties
	pageTitle="Gatilho de temporizador do Azure Functions | Microsoft Azure"
	description="Entenda como usar gatilhos de temporizador no Azure Functions."
	services="functions"
	documentationCenter="na"
	authors="christopheranderson"
	manager="erikre"
	editor=""
	tags=""
	keywords="azure functions, funções, processamento de eventos, computação dinâmica, arquitetura sem servidor"/>

<tags
	ms.service="functions"
	ms.devlang="multiple"
	ms.topic="reference"
	ms.tgt_pltfrm="multiple"
	ms.workload="na"
	ms.date="05/16/2016"
	ms.author="chrande"/>

# Gatilho de temporizador do Azure Functions

Este artigo explica como configurar e codificar gatilhos de temporizador no Azure Functions. Os gatilhos de temporizador chamam funções com base em uma agenda, única ou recorrente.

[AZURE.INCLUDE [introdução](../../includes/functions-bindings-intro.md)]

## function.json para gatilho de temporizador

O arquivo *function.json* fornece uma expressão de agendamento.

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

A expressão de agendamento é uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui seis campos: {segundo} {minuto} {hora} {dia} {mês} {dia da semana}. Muitos documentos de expressão cron encontrados online omitem o campo {segundo} e, portanto, se você copiar de um deles, terá de ajustar o campo extra.

Veja alguns exemplos de expressão de agendamento.

Para disparar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *"
```

Para disparar uma vez a cada duas horas:

```json
"schedule": "0 0 */2 * * *",
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

[AZURE.INCLUDE [próximas etapas](../../includes/functions-bindings-next-steps.md)]

<!---HONumber=AcomDC_0615_2016-->