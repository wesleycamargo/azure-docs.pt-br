<properties
   pageTitle="Diagnóstico de Reliable Services com estado | Microsoft Azure"
   description="Funcionalidade de diagnóstico para Reliable Services com estado"
   services="service-fabric"
   documentationCenter=".net"
   authors="AlanWarwick"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/03/2015"
   ms.author="alanwar"/>

# Funcionalidade de diagnóstico para Reliable Services com estado
A classe StatefulServiceBase de Reliable Services com estado emite eventos [EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) que podem ser usados para depurar o serviço, fornecer informações sobre como o tempo de execução está funcionando e ajudar a solucionar problemas.

## Eventos EventSource
O nome do EventSource da classe StatefulServiceBase de Reliable Services com estado é "Microsoft-ServiceFabric-Services". Os eventos dessa origem de eventos aparecem na janela [Eventos de Diagnóstico](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando o serviço está sendo [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a coletar e/ou visualizar eventos EventSource são [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnóstico do Microsoft Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) e [Microsoft TraceEvent Library](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

## Eventos

|Nome do evento|ID do evento|Nível|Descrição do evento|
|----------|--------|-----|-----------------|
|StatefulRunAsyncInvocation|1|Informativo|Emitido quando a tarefa RunAsync do serviço é iniciada|
|StatefulRunAsyncCancellation|2|Informativo|Emitido quando a tarefa RunAsync do serviço é cancelada|
|StatefulRunAsyncCompletion|3|Informativo|Emitido quando a tarefa RunAsync do serviço é concluída|
|StatefulRunAsyncSlowCancellation|4|Aviso|Emitido quando a tarefa RunAsync do serviço leva muito tempo para concluir o cancelamento|
|StatefulRunAsyncFailure|5|Erro|Emitido quando a tarefa RunAsync do serviço lança uma exceção|

## Interpretar eventos

Os eventos StatefulRunAsyncInvocation, StatefulRunAsyncCompletion e StatefulRunAsyncCancellation são úteis para o gravador de serviço para entender o ciclo de vida de um serviço, bem como o tempo em que um serviço é iniciado, cancelado ou concluído. Isso pode ser útil ao depurar problemas de serviço ou entender o ciclo de vida do serviço.

Gravadores de serviço devem prestar muita atenção nos eventos StatefulRunAsyncSlowCancellation e StatefulRunAsyncFailure, pois eles indicam problemas com o serviço.

StatefulRunAsyncFailure é emitido sempre que a tarefa RunAsync() do serviço lança uma exceção. Geralmente uma exceção lançada indica um erro ou um bug no serviço. Além disso, a exceção faz com que o serviço falhe e seja movido para um nó diferente. Essa pode ser uma operação dispendiosa e pode atrasar as solicitações recebidas enquanto o serviço é movido. Gravadores de serviço devem determinar a causa da exceção e atenuá-la se possível.

StatefulRunAsyncSlowCancellation é emitido sempre que uma solicitação de cancelamento da tarefa RunAsync leva mais de quatro segundos. Quando um serviço leva muito tempo para concluir o cancelamento, ele afeta a capacidade de o serviço ser reiniciado rapidamente em outro nó. Isso pode afetar a disponibilidade geral do serviço.

<!---HONumber=AcomDC_0211_2016-->