---
title: Contadores de desempenho no Application Insights | Microsoft Docs
description: Monitore o sistema e contadores de desempenho .NET personalizados no Application Insights.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b816f4c-a77a-4674-ae36-802ee3a2f56d
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: mbullwin
ms.openlocfilehash: d38a575af54f044d64efc67b5483a67ffcd2fcd6
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60256974"
---
# <a name="system-performance-counters-in-application-insights"></a>Contadores de desempenho do sistema no Application Insights

O Windows fornece uma ampla variedade de [contadores de desempenho](https://docs.microsoft.com/windows/desktop/PerfCtrs/about-performance-counters) como ocupação da CPU, memória, disco e uso da rede. Também é possível definir contadores de desempenho próprios. Desde que o aplicativo esteja em execução no IIS em um host local ou máquina virtual a qual você tem acesso administrativo.

## <a name="view-counters"></a>Visualizar contadores

O painel Métricas mostra o conjunto padrão de contadores de desempenho.

![Contadores de desempenho reportados no Application Insights](./media/performance-counters/performance-counters.png)

Os contadores padrão atuais coletados para aplicativos Web do .NET são:

         - % Process\\Processor Time
         - % Process\\Processor Time Normalized
         - Memory\\Available Bytes
         - ASP.NET Requests/Sec
         - .NET CLR Exceptions Thrown / sec
         - ASP.NET ApplicationsRequest Execution Time
         - Process\\Private Bytes
         - Process\\IO Data Bytes/sec
         - ASP.NET Applications\\Requests In Application Queue
         - Processor(_Total)\\% Processor Time

Para ver todos os gráficos mais úteis em um único lugar, crie um [painel](../../azure-monitor/app/app-insights-dashboards.md) e fixe-os a ele.

## <a name="add-counters"></a>Adicionar contadores

Se o contador de desempenho desejado não estiver incluído na lista de métricas, você poderá adicioná-lo.

1. Descubra quais contadores estão disponíveis no servidor usando este comando do PowerShell no servidor local:
   
    `Get-Counter -ListSet *`
   
    (Consulte [`Get-Counter`](https://technet.microsoft.com/library/hh849685.aspx).)
2. Abra o ApplicationInsights.config.
   
   * Se você adicionou o Application Insights ao seu aplicativo durante o desenvolvimento, edite ApplicationInsights.config no projeto e implante-o novamente nos servidores.
   * Se você usou o Status Monitor para instrumentar um aplicativo Web em tempo de execução, localize applicationinsights.config no diretório raiz do aplicativo no IIS. Atualize-o em cada instância de servidor.
3. Edite a diretiva do coletor de desempenho:
   
```XML
   
    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule, Microsoft.AI.PerfCounterCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(photo)\# Items Sold" ReportAs="Photo sales"/>
      </Counters>
    </Add>

```

É possível capturar os contadores padrão e os que você implementou sozinho. `\Objects\Processes` é um exemplo de um contador padrão, disponível em todos os sistemas Windows. `\Sales(photo)\# Items Sold` é um exemplo de um contador personalizado que pode ser implementado em um serviço Web. 

O formato é `\Category(instance)\Counter"`, ou apenas `\Category\Counter` para categorias que não têm instâncias.

`ReportAs` é necessário para os nomes de contador que não correspondem a `[a-zA-Z()/-_ \.]+` - isto é, eles contêm caracteres que não estão nos seguintes conjuntos: letras, colchetes, barra invertida, hífen, sublinhado, espaço, ponto.

Se você especificar uma instância, ela será coletada como uma dimensão "CounterInstanceName" da métrica reportada.

### <a name="collecting-performance-counters-in-code"></a>Coletando contadores de desempenho no código
Para coletar contadores de desempenho do sistema e enviá-los ao Application Insights, você pode adaptar o snippet a seguir:


``` C#

    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\.NET CLR Memory([replace-with-application-process-name])\# GC Handles", "GC Handles")));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```
Ou você pode fazer a mesma coisa com métricas personalizadas que você criou:

``` C#
    var perfCollectorModule = new PerformanceCollectorModule();
    perfCollectorModule.Counters.Add(new PerformanceCounterCollectionRequest(
      @"\Sales(photo)\# Items Sold", "Photo sales"));
    perfCollectorModule.Initialize(TelemetryConfiguration.Active);
```

## <a name="performance-counters-in-analytics"></a>Contadores de desempenho no Analytics
Você pode pesquisar e exibir relatórios do contador de desempenho no [Analytics](../../azure-monitor/app/analytics.md).

O esquema **performanceCounters** expõe o nome `category`, `counter` e o nome `instance` de cada contador de desempenho.  Na telemetria de cada aplicativo, você verá apenas os contadores para aquele aplicativo. Por exemplo, para ver quais contadores estão disponíveis: 

![Contadores de desempenho na análise do Application Insights](./media/performance-counters/analytics-performance-counters.png)

(Aqui, 'Instance' se refere à instância do contador de desempenho, e não à instância da máquina do servidor ou da função. O nome da instância do contador de desempenho normalmente segmenta os contadores, como tempo de processador, pelo nome do processo ou aplicativo.)

Para obter um gráfico da memória disponível no período recente: 

![Gráfico de tempo da memória na análise do Application Insights](./media/performance-counters/analytics-available-memory.png)

Como outras telemetrias, o **performanceCounters** também tem uma coluna `cloud_RoleInstance` que indica a identidade da instância do servidor host no qual seu aplicativo está sendo executado. Por exemplo, para comparar o desempenho do seu aplicativo em diferentes computadores: 

![Desempenho segmentado por instância de função na análise do Application Insights](./media/performance-counters/analytics-metrics-role-instance.png)

## <a name="aspnet-and-application-insights-counts"></a>Contadores do ASP.NET e do Application Insights
*Qual é a diferença entre a Taxa de exceções e as Métricas de exceções?*

* *Taxa de exceções* é um contador de desempenho do sistema. O CLR conta todas as exceções tratadas e sem tratamento que são lançadas, e divide o total em um intervalo de amostragem pela duração do intervalo. O SDK do Application Insights coleta esse resultado e o envia para o portal.

* *Exceções* é uma contagem dos relatórios TrackException recebida pelo portal no intervalo de amostragem do gráfico. Ele inclui apenas as exceções tratadas em que você tenha gravado chamadas TrackException em seu código e não inclui todas as [exceções sem tratamento](../../azure-monitor/app/asp-net-exceptions.md). 

## <a name="performance-counters-in-aspnet-core-applications"></a>Contadores de desempenho em aplicativos ASP.NET Core
Os contadores de desempenho são suportados apenas se o aplicativo tiver como destino o .NET Framework completo. Não há nenhuma capacidade para coletar contadores de desempenho para aplicativos .NET Core.

## <a name="alerts"></a>Alertas
Assim como ocorre com outras métricas, você pode [definir um alerta](../../azure-monitor/app/alerts.md) para avisar se um contador de desempenho fica fora de um limite especificado. Abra o painel Alertas e clique em Adicionar Alerta.

## <a name="next"></a>Próximas etapas
* [Acompanhamento de dependência](../../azure-monitor/app/asp-net-dependencies.md)
* [Acompanhamento de exceções](../../azure-monitor/app/asp-net-exceptions.md)

