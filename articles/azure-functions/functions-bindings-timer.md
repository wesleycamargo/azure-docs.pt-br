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

O arquivo *function.json* fornece uma expressão e uma opção de agenda que indica se a função deve ser disparada imediatamente.

```json
{
  "bindings": [
    {
      "schedule": "0 * * * * *",
      "runOnStartup": true,
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

A expressão de agendamento pode ser uma [expressão CRON](http://en.wikipedia.org/wiki/Cron#CRON_expression) que inclui seis campos: {segundo} {minuto} {hora} {dia} {mês} {dia da semana}. Muitos documentos de expressão cron encontrados online omitem o campo {segundo} e, portanto, se você copiar de um deles, terá de ajustar o campo extra.

A expressão de agendamento também pode estar no formato *hh:mm:ss* para especificar o atraso entre cada disparo da função.

Veja alguns exemplos de expressão de agendamento.

Para disparar uma vez a cada cinco minutos:

```json
"schedule": "0 */5 * * * *",
"runOnStartup": false,
```

Para disparar imediatamente e então a cada duas horas depois disso:

```json
"schedule": "0 0 */2 * * *",
"runOnStartup": true,
```

Para disparar a cada 15 segundos:

```json
"schedule": "00:00:15",
"runOnStartup": false,
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

<!---HONumber=AcomDC_0525_2016-->