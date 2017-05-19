---
title: "Depurador de Instantâneos do Application Insights do Azure para aplicativos .NET | Microsoft Docs"
description: "Depure instantâneos coletados automaticamente quando exceções forem geradas na produção de aplicativos .NET"
services: application-insights
documentationcenter: 
author: qubitron
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 04/30/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 94b073df00b690f1347e246eeb7e12fdd1bd7cc1
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="debug-snapshots-on-exceptions-in-net-apps"></a>Depurar instantâneos em exceções em aplicativos .NET

*Esse recurso está em visualização.*

Colete automaticamente um Instantâneo de Depuração de seu aplicativo Web ativo quando ocorrer uma exceção. O Instantâneo mostra o estado do código-fonte e variáveis no momento em que a exceção foi lançada. O Depurador de Instantâneo no [Application Insights](app-insights-overview.md) monitora a telemetria de exceção de seu aplicativo Web. Ele coleta Instantâneos em suas exceções com mais lançamentos para que você tenha as informações necessárias para diagnosticar problemas na produção. Inclua o [Pacote de coletor de instantâneos do NuGet](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo e, opcionalmente, configure parâmetros de coleta em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md). Os instantâneos aparecem nas [exceções](app-insights-asp-net-exceptions.md) no portal do Application Insights.

Você pode exibir Instantâneos de Depuração no portal para ver a pilha de chamadas e inspecionar variáveis em cada quadro da pilha de chamadas. Para obter uma experiência de depuração mais poderosa com o código-fonte, abra os Instantâneos com o Visual Studio 2017 Enterprise [baixando a extensão do Depurador de Instantâneos para o Visual Studio](https://aka.ms/snapshotdebugger).

A coleta de Instantâneos está disponível para aplicativos Web do ASP.NET em execução no .NET Framework 4.6 e posterior, hospedados no IIS na Computação do Azure ou no Serviço de Aplicativo do Azure.

## <a name="configure-snapshot-collection"></a>Configurar a coleta de Instantâneos

1. Se ainda não tiver feito isso, [habilite o Application Insights em seu aplicativo Web](app-insights-asp-net.md).

2. Inclua o pacote do NuGet [Microsoft.ApplicationInsights.SnapshotCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) em seu aplicativo. 

3. Examine as opções padrão que o pacote adicionou a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md).

```xml
  <TelemetryProcessors>
    <Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">
      <!-- The default is true, but you can disable Snapshot Debugging by setting it to false -->
      <IsEnabled>true</IsEnabled>
      <!-- Snapshot Debugging is usually disabled in developer mode, but you can enable it by setting this to true. -->
      <!-- DeveloperMode is a property on the active TelemetryChannel. -->
      <IsEnabledInDeveloperMode>false</IsEnabledInDeveloperMode>
      <!-- How many times we need to see an exception before we ask for snapshots. -->
      <ThresholdForSnapshotting>5</ThresholdForSnapshotting>
      <!-- The maximum number of examples we create for a single problem. -->
      <MaximumSnapshotsRequired>3</MaximumSnapshotsRequired>
      <!-- The maximum number of problems that we can be tracking at any time. -->
      <MaximumCollectionPlanSize>50</MaximumCollectionPlanSize>
      <!-- How often to reset problem counters. -->
      <ProblemCounterResetInterval>06:00:00</ProblemCounterResetInterval>
      <!-- The maximum number of snapshots allowed in one minute. -->
      <SnapshotsPerMinuteLimit>2</SnapshotsPerMinuteLimit>
      <!-- The maximum number of snapshots allowed per day. -->
      <SnapshotsPerDayLimit>50</SnapshotsPerDayLimit>
    </Add>
  </TelemetryProcessors>
```

São coletados apenas Instantâneos de exceções que são visíveis para o SDK do Application Insights. Em alguns casos, talvez seja necessário [configurar a coleta de exceções](app-insights-asp-net-exceptions.md#exceptions) para ver as exceções com Instantâneos que aparecem no portal.

## <a name="debugging-snapshots-in-the-application-insights-portal"></a>Depuração de Instantâneos no portal do Application Insights

Se um Instantâneo estiver disponível para uma determinada ID de problema ou exceção, um link *Abrir Instantâneo de Depuração* aparecerá na [exceção](app-insights-asp-net-exceptions.md) no portal do Application Insights.

![Botão Abrir Instantâneo de Depuração na exceção](./media/app-insights-snapshot-debugger/snapshot-on-exception.png)

Na exibição do Instantâneo de Depuração, você verá uma pilha de chamadas e um painel de variáveis. Selecionar quadros da pilha de chamadas no painel de pilha de chamadas permite exibir as variáveis locais e os parâmetros da chamada de função no painel de variáveis.

![Exibir Instantâneo de Depuração no portal](./media/app-insights-snapshot-debugger/open-snapshot-portal.png)

Instantâneos podem conter informações confidenciais e, por padrão, não são visíveis. Para exibir os Instantâneos, você deve ter a função `Application Insights Snapshot Debugger` atribuída a você no portal para a assinatura ou recurso. Atualmente, essa função só pode ser atribuída por proprietários de assinatura a cada usuário. Atualmente, não há suporte para atribuir a função a grupos do Azure Active Directory.

## <a name="debugging-snapshots-with-visual-studio-2017-enterprise"></a>Depuração de Instantâneos com o Visual Studio 2017 Enterprise
Você pode clicar no botão *Baixar Instantâneo* para baixar um arquivo `.diagsession`, que pode ser aberto pelo Visual Studio 2017 Enterprise. Atualmente, abrir o arquivo `.diagsession` requer que primeiro você [baixe e instale a extensão do Depurador de Instantâneo para o Visual Studio](https://aka.ms/snapshotdebugger).

Após abrir o arquivo de Instantâneo, você será direcionado para a página de Depuração de Minidespejo do Visual Studio, na qual pode iniciar a depuração do Instantâneo clicando em *Depurar Código Gerenciado*. Você é levado à linha de código em que a exceção foi lançada e pode depurar o estado atual do processo.

![Exibir instantâneo de depuração no Visual Studio](./media/app-insights-snapshot-debugger/open-snapshot-visualstudio.png)

O Instantâneo baixado contém os arquivos de símbolo que foram encontrados no servidor de aplicativos Web. Esses arquivos de símbolo são necessários para associar dados do Instantâneo ao código-fonte. Para aplicativos do Serviço de Aplicativo do Azure, certifique-se de habilitar a implantação de símbolos ao publicar seus aplicativos Web.

## <a name="how-snapshots-work"></a>Como os Instantâneos funcionam

Quando seu aplicativo é iniciado, é criado um processo separado de carregador de Instantâneos que monitora seu aplicativo quanto a solicitações de Instantâneos. Quando um Instantâneo é solicitado, é feita uma cópia de sombra do processo em execução em cerca de 10 a 20 ms. O processo de sombra é analisado e um Instantâneo é criado enquanto o processo principal continua sendo executado e fornecendo tráfego para os usuários. O Instantâneo, então, é carregado para o Application Insights em conjunto com os arquivos de símbolo (.pdb) relevantes necessários para exibir o Instantâneo.

## <a name="current-limitations"></a>Limitações atuais

### <a name="publishing-symbols"></a>Símbolos de publicação
O Depurador de Instantâneo requer que os arquivos de símbolo estejam presentes no servidor de produção para decodificar variáveis e fornecer uma experiência de depuração no Visual Studio. A versão 15.2 do Visual Studio 2017 publica símbolos para builds de Versão por padrão ao publicar no Serviço de Aplicativo do Azure. Em versões anteriores, você precisa adicionar a seguinte linha ao arquivo `.pubxml` de seu perfil de publicação para que os símbolos sejam publicados no modo de versão.

```xml
    <ExcludeGeneratedDebugSymbol>False</ExcludeGeneratedDebugSymbol>
```

Para a Computação do Azure e outros tipos, certifique-se de que os arquivos de símbolo estejam na mesma pasta que o arquivo .dll do aplicativo principal (geralmente, `wwwroot/bin`) ou que estejam disponíveis no caminho atual.

### <a name="optimized-builds"></a>Builds otimizados
Em alguns casos, variáveis locais não são visíveis em builds de versão devido a otimizações aplicadas durante o processo de compilação. Essa limitação será corrigida em uma versão futura do pacote do NuGet.

## <a name="next-steps"></a>Próximas etapas

* [Diagnosticar exceções em seus aplicativos Web](app-insights-asp-net-exceptions.md) explica como deixar as exceções mais visíveis para o Application Insights. 
* A [Detecção Inteligente](app-insights-proactive-diagnostics.md) descobre automaticamente anomalias de desempenho.

