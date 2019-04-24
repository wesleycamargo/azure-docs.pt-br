---
title: Monitorando uso e desempenho de aplicativos de área de trabalho do Windows
description: Analise o uso e o desempenho de seu aplicativo da área de trabalho do Windows com o Application Insights.
services: application-insights
documentationcenter: windows
author: mrbullwinkle
manager: carmonm
ms.assetid: 19040746-3315-47e7-8c60-4b3000d2ddc4
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/15/2018
ms.author: mbullwin
ms.openlocfilehash: 95ff8d1a70325357fee4bc24fd96c1a1c7a73845
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60371471"
---
# <a name="monitoring-usage-and-performance-in-classic-windows-desktop-apps"></a>Monitorando uso e desempenho de aplicativos de área de trabalho clássica do Windows

Aplicativos hospedados localmente, no Azure e em outras nuvens podem tirar proveito do Application Insights. A única limitação é a necessidade de [permitir a comunicação](../../azure-monitor/app/ip-addresses.md) para o serviço do Application Insights. Para monitorar os aplicativos da Plataforma Universal do Windows (UWP), recomendamos o [Visual Studio App Center](../../azure-monitor/learn/mobile-center-quickstart.md).

## <a name="to-send-telemetry-to-application-insights-from-a-classic-windows-application"></a>Para enviar telemetria ao Application Insights a partir de um aplicativo do Windows clássico
1. No [portal do Azure](https://portal.azure.com), [crie um recurso Application Insights](../../azure-monitor/app/create-new-resource.md ). Para o tipo de aplicativo, escolha o aplicativo ASP.NET.
2. Faça uma cópia da chave de instrumentação. Localize a chave no menu suspenso Essentials do novo recurso que você acabou de criar. 
3. No Visual Studio, edite os pacotes NuGet do seu projeto de aplicativo e adicione Microsoft.ApplicationInsights.WindowsServer. (Ou escolha Microsoft.ApplicationInsights se você quiser apenas a API básica, sem os módulos de coleta da telemetria padrão.)
4. Defina a chave de instrumentação no seu código:
   
    `TelemetryConfiguration.Active.InstrumentationKey = "` *sua chave* `";`
   
    ou em ApplicationInsights.config (se tiver instalado um dos pacotes de telemetria padrão):
   
    `<InstrumentationKey>`*sua chave*`</InstrumentationKey>` 
   
    Se você usar ApplicationInsights.config, verifique se suas propriedades no Gerenciador de Soluções estão definidas como **Ação de Compilação = Conteúdo, Copiar para Diretório de Saída = Copiar**.
5. [Use a API](../../azure-monitor/app/api-custom-events-metrics.md) para enviar telemetria.
6. Execute o aplicativo e veja a telemetria no recurso criado no Portal do Azure.

## <a name="telemetry"></a>Código de exemplo
```csharp

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Próximas etapas
* [Criar um painel](../../azure-monitor/app/app-insights-dashboards.md)
* [Pesquisa de Diagnóstico](../../azure-monitor/app/diagnostic-search.md)
* [Explorar métricas](../../azure-monitor/app/metrics-explorer.md)
* [Escrever consultas do Analytics](../../azure-monitor/app/analytics.md)

