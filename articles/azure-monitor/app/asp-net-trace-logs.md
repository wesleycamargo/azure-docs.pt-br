---
title: Explorar os logs de rastreamento do .NET no Application Insights
description: Pesquisar logs gerados pelo rastreamento, NLog ou Log4Net.
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 0c2a084f-6e71-467b-a6aa-4ab222f17153
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/08/2019
ms.author: mbullwin
ms.openlocfilehash: d366f363b7bd1d5306d598c9b38258eb78076b7c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/09/2019
ms.locfileid: "65472062"
---
# <a name="explore-netnet-core-trace-logs-in-application-insights"></a>Explorar os logs de rastreamento do .NET/.NET Core no Application Insights

Enviar logs de rastreamento de diagnóstico para seu aplicativo ASP.NET/ASP.NET Core de ILogger, NLog, log4Net ou Trace para [Azure Application Insights][start]. Em seguida, você pode explorar e pesquisá-los. Esses logs são mesclados com os outros arquivos de log do seu aplicativo, para que você possa identificar os rastreamentos que estão associados com cada solicitação de usuário e correlação-los com outros eventos e relatórios de exceção.

> [!NOTE]
> Você precisa do módulo de captura de log? Ele é um adaptador útil para agentes de terceiros. Mas se você ainda não usa o NLog, log4Net ou Trace, convém chamar apenas [ **tracktrace () do Application Insights** ](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace) diretamente.
>
>
## <a name="install-logging-on-your-app"></a>Instalar o log no seu aplicativo
Instale sua estrutura de registros escolhida no seu projeto, que deve resultar em uma entrada no App. config ou Web. config.

```XML
    <configuration>
      <system.diagnostics>
    <trace autoflush="true" indentsize="0">
      <listeners>
        <add name="myAppInsightsListener" type="Microsoft.ApplicationInsights.TraceListener.ApplicationInsightsTraceListener, Microsoft.ApplicationInsights.TraceListener" />
      </listeners>
    </trace>
  </system.diagnostics>
   </configuration>
```

## <a name="configure-application-insights-to-collect-logs"></a>Configurar o Application Insights para coletar logs
[Adicione o Application Insights ao seu projeto](../../azure-monitor/app/asp-net.md) se você ainda não tiver feito isso. Você verá uma opção para incluir o coletor de logs.

Ou clique em seu projeto no Gerenciador de soluções **configurar o Application Insights**. Selecione o **configurar a coleta de rastreamento** opção.

> [!NOTE]
> Nenhuma opção Application Insights menu ou de log de coletor? Experimente [Solucionar problemas](#troubleshooting).

## <a name="manual-installation"></a>Instalação manual
Use este método se o tipo de projeto não tiver suporte no instalador do Application Insights (por exemplo, um projeto de Área de Trabalho do Windows).

1. Se você planeja usar o log4Net ou NLog, instale-o em seu projeto.
2. No Gerenciador de soluções, clique em seu projeto e selecione **gerenciar pacotes NuGet**.
3. Pesquise "Application Insights".
4. Selecione um dos seguintes pacotes:

   - Para o ILogger: [Microsoft.Extensions.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.Extensions.Logging.ApplicationInsights.svg)](https://www.nuget.org/packages/Microsoft.Extensions.Logging.ApplicationInsights/)
   - Para o NLog: [Microsoft.ApplicationInsights.NLogTarget](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.NLogTarget.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.NLogTarget/)
   - Para o Log4Net: [Microsoft.ApplicationInsights.Log4NetAppender](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.Log4NetAppender.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Log4NetAppender/)
   - Para o System.Diagnostics: [Microsoft.ApplicationInsights.TraceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.TraceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.TraceListener/)
   - [Microsoft.ApplicationInsights.DiagnosticSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.DiagnosticSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener/)
   - [Microsoft.ApplicationInsights.EtwCollector](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
[![NuGet](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EtwCollector.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EtwCollector/)
   - [Microsoft.ApplicationInsights.EventSourceListener](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)
[![Nuget](https://img.shields.io/nuget/vpre/Microsoft.ApplicationInsights.EventSourceListener.svg)](https://www.nuget.org/packages/Microsoft.ApplicationInsights.EventSourceListener/)

O pacote NuGet instala os assemblies necessários e modifica Web. config ou App. config, se for aplicável.

## <a name="ilogger"></a>ILogger

Para obter exemplos de como usar a implementação de ILogger de Insights de aplicativo com aplicativos de console e o ASP.NET Core, consulte [ApplicationInsightsLoggerProvider para .NET Core ILogger logs](ilogger.md).

## <a name="insert-diagnostic-log-calls"></a>Inserir chamadas de log de diagnóstico
Se você usa System.Diagnostics.Trace, uma chamada típica é semelhante a:

    System.Diagnostics.Trace.TraceWarning("Slow response - database01");

Se você preferir log4net ou NLog, use:

    logger.Warn("Slow response - database01");

## <a name="use-eventsource-events"></a>Usar eventos EventSource
É possível configurar eventos [System.Diagnostics.Tracing.EventSource](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EventSourceListener`. Depois, edite a seção `TelemetryModules` do arquivo [ApplicationInsights.config](../../azure-monitor/app/configuration-with-applicationinsights-config.md).

```xml
    <Add Type="Microsoft.ApplicationInsights.EventSourceListener.EventSourceTelemetryModule, Microsoft.ApplicationInsights.EventSourceListener">
      <Sources>
        <Add Name="MyCompany" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **Nome** Especifica o nome do EventSource para coletar.
 * **Nível** Especifica o nível de log para coletar: *Críticos*, *erro*, *informativa*, *LogAlways*, *detalhado*, ou *aviso*.
 * **Palavras-chave** (opcional) Especifique o valor inteiro das combinações de palavra-chave para usar.

## <a name="use-diagnosticsource-events"></a>Usar eventos DiagnosticSource
É possível configurar eventos [System.Diagnostics.DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) para que eles sejam enviados para o Application Insights como rastreamentos. Primeiro, instale o pacote NuGet [`Microsoft.ApplicationInsights.DiagnosticSourceListener`](https://www.nuget.org/packages/Microsoft.ApplicationInsights.DiagnosticSourceListener). Em seguida, edite a seção "TelemetryModules" a [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) arquivo.

```xml
    <Add Type="Microsoft.ApplicationInsights.DiagnosticSourceListener.DiagnosticSourceTelemetryModule, Microsoft.ApplicationInsights.DiagnosticSourceListener">
      <Sources>
        <Add Name="MyDiagnosticSourceName" />
      </Sources>
    </Add>
```

Para cada DiagnosticSource que você deseja rastrear, adicione uma entrada com o **nome** atributo definido como o nome do seu DiagnosticSource.

## <a name="use-etw-events"></a>Usar eventos ETW
Você pode configurar eventos de Event Tracing for Windows (ETW) a serem enviados ao Application Insights como rastreamentos. Primeiro, instale o pacote NuGet `Microsoft.ApplicationInsights.EtwCollector`. Em seguida, edite a seção "TelemetryModules" a [applicationinsights. config](../../azure-monitor/app/configuration-with-applicationinsights-config.md) arquivo.

> [!NOTE] 
> Eventos ETW só podem ser coletados se o processo que hospeda o SDK é executado sob uma identidade que é um membro de administradores ou usuários de Log de desempenho.

```xml
    <Add Type="Microsoft.ApplicationInsights.EtwCollector.EtwCollectorTelemetryModule, Microsoft.ApplicationInsights.EtwCollector">
      <Sources>
        <Add ProviderName="MyCompanyEventSourceName" Level="Verbose" />
      </Sources>
    </Add>
```

Para cada fonte, você pode definir os seguintes parâmetros:
 * **ProviderName** é o nome do provedor do ETW para coletar.
 * **ProviderGuid** Especifica o GUID do provedor do ETW para coletar. Ele pode ser usado em vez de `ProviderName`.
 * **Nível** define o nível de log para coletar. Ele pode ser *crítica*, *erro*, *informativo*, *LogAlways*, *detalhado*, ou *Aviso*.
 * **Palavras-chave** (opcional) defina o valor de inteiro das combinações de palavra-chave para usar.

## <a name="use-the-trace-api-directly"></a>Usar a API de rastreamento diretamente
Você pode chamar a API de rastreamento do Application Insights diretamente. Os adaptadores de log usam essa API.

Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow response - database01");

Uma vantagem de TrackTrace é que você pode colocar dados relativamente compridos na mensagem. Por exemplo, você pode codificar dados POST.

Você também pode adicionar um nível de severidade à mensagem. E, como outros dados de telemetria, você pode adicionar valores de propriedade para ajudar a filtrar ou pesquisar diferentes conjuntos de rastreamentos. Por exemplo:

    var telemetry = new Microsoft.ApplicationInsights.TelemetryClient();
    telemetry.TrackTrace("Slow database response",
                   SeverityLevel.Warning,
                   new Dictionary<string,string> { {"database", db.ID} });

Isso permite que você filtre facilmente na [pesquisa] [ diagnostic] todas as mensagens de um nível de severidade específico relacionadas a um determinado banco de dados.

## <a name="explore-your-logs"></a>Explorar seus logs
Execute o aplicativo no modo de depuração ou implantá-lo em tempo real.

No painel de visão geral do seu aplicativo no [portal do Application Insights][portal], selecione [pesquisa][diagnostic].

Por exemplo, você pode:

* Filtrar rastreamentos de log ou itens com propriedades específicas.
* Inspecionar um item específico em detalhes.
* Localizar outros dados de log do sistema que se relaciona com a mesma solicitação de usuário (tem a mesma OperationId).
* Salve a configuração de uma página como um favorito.

> [!NOTE]
>Se seu aplicativo enviar muitos dados e você estiver usando o SDK do Application Insights para ASP.NET versão 2.0.0-Beta3 ou posterior, o *amostragem adaptável* recurso poderá operar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)
>

## <a name="troubleshooting"></a>solução de problemas
### <a name="how-do-i-do-this-for-java"></a>Como faço isso no Java?
Use os [adaptadores de log Java](../../azure-monitor/app/java-trace-logs.md).

### <a name="theres-no-application-insights-option-on-the-project-context-menu"></a>Não há nenhuma opção do Application Insights no menu de contexto do projeto
* Certifique-se de que o Developer Analytics Tools está instalado na máquina de desenvolvimento. No Visual Studio **ferramentas** > **extensões e atualizações**, procure **Developer Analytics Tools**. Se não estiver no **Installed** guia, abra o **Online** guia e instalá-lo.
* Isso pode ser um tipo de projeto não dá suporte a ferramentas de análise de Devloper. Use a [instalação manual](#manual-installation).

### <a name="theres-no-log-adapter-option-in-the-configuration-tool"></a>Não há nenhuma opção de adaptador de log na ferramenta de configuração
* Instale primeiro a estrutura de registros.
* Se você estiver usando Trace, certifique-se de que você tenha [configurado no *Web. config*](https://msdn.microsoft.com/library/system.diagnostics.eventlogtracelistener.aspx).
* Certifique-se de que você tenha a versão mais recente do Application Insights. No Visual Studio, acesse **ferramentas** > **extensões e atualizações**e abra o **atualizações** guia. Se **Developer Analytics Tools** está lá, selecione-o para atualizá-lo.

### <a name="emptykey"></a>Recebo a mensagem de erro "chave de instrumentação não pode ser vazia"
Provavelmente, você instalou o pacote de Nuget de adaptador de registro em log sem instalar o Application Insights. No Gerenciador de soluções, clique com botão direito *applicationinsights. config*e selecione **atualizar o Application Insights**. Você será solicitado a entrar no Azure e criar um recurso do Application Insights ou reutilizar um existente. Isso deve resolver o problema.

### <a name="i-can-see-traces-but-not-other-events-in-diagnostic-search"></a>Posso ver rastreamentos, mas não outros eventos na pesquisa de diagnóstico
Ele pode levar algum tempo para que todos os eventos e solicitações percorram o pipeline.

### <a name="limits"></a>Que quantidade de dados é mantida?
Vários fatores afetam a quantidade de dados retidos. Para obter mais informações, consulte o [limites](../../azure-monitor/app/api-custom-events-metrics.md#limits) seção da página de métricas de evento do cliente.

### <a name="i-dont-see-some-log-entries-that-i-expected"></a>Não consigo ver algumas entradas de log que eu esperava
Se seu aplicativo envia grandes quantidades de dados e você estiver usando o SDK do Application Insights para ASP.NET versão 2.0.0-Beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma parte da sua telemetria. [Saiba mais sobre amostragem.](../../azure-monitor/app/sampling.md)

## <a name="add"></a>Próximas etapas

* [Diagnosticar falhas e exceções no ASP.NET][exceptions]
* [Saiba mais sobre a pesquisa][diagnostic]
* [Configurar testes de disponibilidade e capacidade de resposta][availability]
* [Solução de problemas][qna]

<!--Link references-->

[availability]: ../../azure-monitor/app/monitor-web-app-availability.md
[diagnostic]: ../../azure-monitor/app/diagnostic-search.md
[exceptions]: asp-net-exceptions.md
[portal]: https://portal.azure.com/
[qna]: ../../azure-monitor/app/troubleshoot-faq.md
[start]: ../../azure-monitor/app/app-insights-overview.md