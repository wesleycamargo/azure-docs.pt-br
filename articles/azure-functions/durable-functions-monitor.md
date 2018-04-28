---
title: Monitores nas Funções Duráveis - Azure
description: Saiba como implementar um monitor de status utilizando a extensão de Funções Duráveis do Azure Functions.
services: functions
author: kashimiz
manager: cfowler
editor: ''
tags: ''
keywords: ''
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 03/19/2018
ms.author: azfuncdf
ms.openlocfilehash: 9cb7a076ea922b9868bd439d160aec96f044e3b6
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2018
---
# <a name="monitor-scenario-in-durable-functions---weather-watcher-sample"></a>Cenário do Monitor em Funções Duráveis - Exemplo de observador meteorológico

O padrão de monitoramento refere-se a um processo *recorrente* flexível em um fluxo de trabalho - por exemplo, sondagem até que determinadas condições sejam atendidas. Este artigo explica um exemplo que usa as [Funções Duráveis](durable-functions-overview.md) para implementar o monitoramento.

## <a name="prerequisites"></a>pré-requisitos

* [Instalar Funções Duráveis](durable-functions-install.md).
* Complete a [Sequência Hello](durable-functions-sequence.md) passo a passo.

## <a name="scenario-overview"></a>Visão geral do cenário

Este exemplo monitora as condições meteorológicas atuais de um local e alerta um usuário por SMS quando as condições climáticas melhoram. Você poderia usar uma função disparada por temporizador para verificar a meteorologia e enviar alertas. No entanto, um problema com essa abordagem é o **gerenciamento do tempo de vida**. Se apenas um alerta for enviado, o monitor deverá desabilitar-se após a detecção das condições climáticas. O padrão de monitoramento pode encerrar sua própria execução, entre outros benefícios:

* Os monitores são executados em intervalos, não em agendamentos: um disparador com timer *executa* a cada hora; um monitor *aguarda* uma hora entre as ações. As ações de um monitor não se sobrepõem, exceto se especificado, o que pode ser importante para tarefas de execução longa.
* Os monitores podem ter intervalos dinâmicos: o tempo de espera pode mudar com base em alguma condição.
* Os monitores poderão terminar quando alguma condição for atendida ou terminada por outro processo.
* Monitores podem receber parâmetros. O exemplo mostra como o mesmo processo de monitoramento meteorológico pode ser aplicado a qualquer número do telefone e localização solicitados.
* Monitores são escalonáveis. Como cada monitor é uma instância de orquestração, vários monitores podem ser criados sem a necessidade de criar novas funções ou definir mais código.
* Os monitores integram-se facilmente em fluxos de trabalho maiores. Um monitor pode ser uma seção de uma função de orquestração mais complexa ou uma [sub-orquestração](https://docs.microsoft.com/azure/azure-functions/durable-functions-sub-orchestrations).

## <a name="configuring-twilio-integration"></a>Configurando a integração com o Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="configuring-weather-underground-integration"></a>Configurar a integração do Weather Underground

Este exemplo envolve o uso da API do Weather Underground para verificar as condições climáticas atuais de um local.

A primeira coisa que você precisa é de uma conta do Weather Underground. É possível criar uma gratuitamente em [https://www.wunderground.com/signup](https://www.wunderground.com/signup). Depois de ter uma conta, será necessário adquirir uma chave de API. Você pode fazer isso, visitando [https://www.wunderground.com/weather/api](https://www.wunderground.com/weather/api) e selecionando Configurações de Chaves. O plano do Desenvolvedor Stratus é gratuito e suficiente para executar este exemplo.

Após ter uma chave de API, adicione a **configuração de aplicativo** a seguir no seu aplicativo de funções.

| Nome da configuração do aplicativo | Descrição do valor |
| - | - |
| **WeatherUndergroundApiKey**  | A chave de API do Weather Underground. |

## <a name="the-functions"></a>As funções

Este artigo explica as seguintes funções no aplicativo de exemplo:

* `E3_Monitor`: uma função do orquestrador que chama `E3_GetIsClear` periodicamente. Chamará `E3_SendGoodWeatherAlert` se `E3_GetIsClear` retornar verdadeiro.
* `E3_GetIsClear`: uma função de atividade que verifica as condições meteorológicas atuais de um local.
* `E3_SendGoodWeatherAlert`: uma função de atividade que envia uma mensagem SMS via Twilio.

As seções a seguir explicam a configuração e o código utilizados para o script C#. O código para desenvolvimento no Visual Studio é exibido no final do artigo.
 
## <a name="the-weather-monitoring-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>A orquestração de monitoramento meteorológico (Código de exemplo do Portal do Azure e Visual Studio Code)

A função **E3_Monitor** usa *function.json* padrão para funções de orquestrador.

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_Monitor/function.json)]

Este é o código que implementa a função:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_Monitor/run.csx)]

Essa função de orquestrador executa as ações a seguir:

1. Obtém o **MonitorRequest** que consiste na *localização* a monitorar e no *número do telefone* para o qual ele enviará uma notificação de SMS.
2. Determina o tempo de expiração do monitor. O exemplo usa um valor embutido em código para brevidade.
3. Chama **E3_GetIsClear** para determinar se há boas condições climáticas na localização solicitada.
4. Se o houver boas condições climáticas, chamará **E3_SendGoodWeatherAlert** para enviar uma notificação de SMS para o número do telefone solicitado.
5. Cria um temporizador durável para retomar a orquestração no próximo intervalo de sondagem. O exemplo usa um valor embutido em código para brevidade.
6. Continua em execução até que [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) passe o tempo de expiração do monitor ou um alerta por SMS seja enviado.

Várias instâncias de orquestrador podem ser executadas simultaneamente enviando vários **MonitorRequests**. A localização a ser monitorada e o número do telefone para envio de um alerta por SMS podem ser excluídos.

## <a name="strongly-typed-data-transfer"></a>Transferência de dados fortemente tipados

O orquestrador requer várias partes dos dados, portanto, [objetos POCO compartilhados](functions-reference-csharp.md#reusing-csx-code) são usados para transferência de dados fortemente tipados: [!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/MonitorRequest.csx)]

[!code-csharp[Main](~/samples-durable-functions/samples/csx/shared/Location.csx)]

## <a name="helper-activity-functions"></a>Funções de atividade auxiliares

Como com outros exemplo, as funções de atividade auxiliares são funções regulares que usam a associação de gatilho `activityTrigger`. A função **E3_GetIsClear** função obtém as condições meteorológicas atuais, utilizando a API do Weather Underground e determina se as condições estão boas. O *function.json* é definido da seguinte maneira:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/function.json)]

E aqui está a implementação. Tal como os POCOs utilizados para transferência de dados, a lógica para identificar a chamada de API e analisar a resposta JSON é abstraída em uma classe compartilhada. É possível localizá-la como parte do [código de exemplo do Visual Studio](#run-the-sample).

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_GetIsClear/run.csx)]

A função **E3_SendGoodWeatherAlert** usa a associação do Twilio para enviar uma mensagem SMS notificando o usuário final de que as condições climáticas estão favoráveis para uma caminhada. O *function.json* é simples:

[!code-json[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/function.json)]

E aqui está o código que envia a mensagem SMS:

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E3_SendGoodWeatherAlert/run.csx)]

## <a name="run-the-sample"></a>Execute o exemplo

Usando as funções disparadas por HTTP incluídas no exemplo, você pode iniciar a orquestração enviando a seguinte solicitação HTTP POST:

```
POST https://{host}/orchestrators/E3_Monitor
Content-Length: 77
Content-Type: application/json

{ "Location": { "City": "Redmond", "State": "WA" }, "Phone": "+1425XXXXXXX" }
```
```
HTTP/1.1 202 Accepted
Content-Type: application/json; charset=utf-8
Location: https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={SystemKey}
RetryAfter: 10

{"id": "f6893f25acf64df2ab53a35c09d52635", "statusQueryGetUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "sendEventPostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/raiseEvent/{eventName}?taskHub=SampleHubVS&connection=Storage&code={systemKey}", "terminatePostUri": "https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason={text}&taskHub=SampleHubVS&connection=Storage&code={systemKey}"}
```

A instância **E3_Monitor** inicia e consulta as condições meteorológicas atuais para a localização solicitada. Se as condições climáticas estiverem boas, a instância chama uma função de atividade para enviar um alerta; caso contrário, define um temporizador. Quando o temporizador expirar, a orquestração será retomada.

É possível visualizar a atividade da orquestração, observando os logs de função no Portal do Azure Functions.

```
2018-03-01T01:14:41.649 Function started (Id=2d5fcadf-275b-4226-a174-f9f943c90cd1)
2018-03-01T01:14:42.741 Started orchestration with ID = '1608200bb2ce4b7face5fc3b8e674f2e'.
2018-03-01T01:14:42.780 Function completed (Success, Id=2d5fcadf-275b-4226-a174-f9f943c90cd1, Duration=1111ms)
2018-03-01T01:14:52.765 Function started (Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb)
2018-03-01T01:14:52.890 Received monitor request. Location: Redmond, WA. Phone: +1425XXXXXXX.
2018-03-01T01:14:52.895 Instantiating monitor for Redmond, WA. Expires: 3/1/2018 7:14:52 AM.
2018-03-01T01:14:52.909 Checking current weather conditions for Redmond, WA at 3/1/2018 1:14:52 AM.
2018-03-01T01:14:52.954 Function completed (Success, Id=b1b7eb4a-96d3-4f11-a0ff-893e08dd4cfb, Duration=189ms)
2018-03-01T01:14:53.226 Function started (Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859)
2018-03-01T01:14:53.808 Function completed (Success, Id=80a4cb26-c4be-46ba-85c8-ea0c6d07d859, Duration=582ms)
2018-03-01T01:14:53.967 Function started (Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c)
2018-03-01T01:14:53.996 Next check for Redmond, WA at 3/1/2018 1:44:53 AM.
2018-03-01T01:14:54.030 Function completed (Success, Id=561d0c78-ee6e-46cb-b6db-39ef639c9a2c, Duration=62ms)
```

A orquestração [terminará](durable-functions-instance-management.md#terminating-instances) quando o tempo limite for alcançado ou as condições climáticas favoráveis forem detectadas. Também é possível usar `TerminateAsync` dentro de outra função ou invocar o webhook **terminatePostUri** HTTP POST referenciados na resposta 202 acima, substituindo `{text}` pelo motivo da terminação:

```
POST https://{host}/admin/extensions/DurableTaskExtension/instances/f6893f25acf64df2ab53a35c09d52635/terminate?reason=Because&taskHub=SampleHubVS&connection=Storage&code={systemKey}
```

## <a name="visual-studio-sample-code"></a>Código de exemplo do Visual Studio

Esta é a orquestração como um único arquivo em C# em um projeto do Visual Studio:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/Monitor.cs)]

## <a name="next-steps"></a>Próximas etapas

Este exemplo demonstrou como usar as Funções Duráveis para monitorar o status de uma fonte externa usando [temporizadores duráveis](durable-functions-timers.md) e lógica condicional. O próximo exemplo mostra como usar eventos externos e [temporizadores duráveis](durable-functions-timers.md) para lidar com interação humana.

> [!div class="nextstepaction"]
> [Executar o exemplo de interação humana](durable-functions-phone-verification.md)
