---
title: "Guia de solução de problemas do Depurador de Instantâneo de aplicativo do Azure Application Insights | Microsoft Docs"
description: "Perguntas frequentes sobre o depurador de Instantâneo do Azure Application Insights."
services: application-insights
documentationcenter: .net
author: mrbullwinkle
manager: carmonm
ms.assetid: 
ms.service: application-insights
ms.workload: 
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mbullwin
ms.openlocfilehash: 5d6a2fe4c3ee373172f5324b6c7a39e4f94f4652
ms.sourcegitcommit: 29bac59f1d62f38740b60274cb4912816ee775ea
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/29/2017
---
# <a name="snapshot-debugger-troubleshooting-guide"></a>Depurador de Instantâneo: Guia de solução de problemas

O Depurador de Instantâneo do Application Insights permite que você colete automaticamente um instantâneo de depuração de aplicativos Web dinâmicos. O instantâneo mostra o estado do código-fonte e variáveis no momento em que uma exceção foi lançada. Se você tiver dificuldade em colocar o depurador de instantâneo do Application Insights para funcionar, este artigo explica como funciona o depurador e apresenta soluções para cenários de solução de problemas comuns. 

## <a name="how-does-application-insights-snapshot-debugger-work"></a>Como funciona o depurador de instantâneo do Application Insights

O Depurador de Instantâneo do Application Insights faz parte do pipeline de telemetria do Application Insights (uma instância de ITelemetryProcessor); o coletor de instantâneo monitora as exceções geradas em seu código (AppDomain.FirstChanceException) e as exceções que são controladas pelo pipeline de telemetria de exceção do Application Insights. Depois e adicionar o coletor de instantâneo ao seu projeto com êxito e ele detectar uma exceção no pipeline de telemetria do Application Insights, um evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e ' SnapshotCollectorEnabled' em Dados Personalizados será enviado. Ao mesmo tempo, ele iniciará um processo com o nome de 'MinidumpUploader.exe' para carregar os arquivos de dados de instantâneo coletados no Application Insights.  Quando o processo 'MinidumpUploader.exe' inicia, um evento personalizado com o nome 'UploaderStart' é enviado. Após as etapas anteriores, o coletor de instantâneos entra em seu comportamento de monitoramento normal.

Enquanto o coletor de instantâneos está monitorando telemetria de exceção do Application Insights, ele usa os parâmetros (por exemplo, ThresholdForSnapshotting, MaximumSnapshotsRequired, MaximumCollectionPlanSize, ProblemCounterResetInterval) definidos na configuração para determinar quando coletar um instantâneo. Quando todas as regras forem atendidas, o coletor solicitará um instantâneo para a próxima exceção lançada no mesmo local. Simultaneamente, um evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e 'RequestSnapshots' é enviado. Como o compilador otimizará o código de 'Versão', as variáveis locais poderão não ser vistas no instantâneo coletado. O coletor de instantâneos tentará desotimizar o método que gerou a exceção quando ele solicitar instantâneos. Durante esse período, um evento personalizado do Application Insights com nome 'AppInsightsSnapshotCollectorLogs' e 'ProductionBreakpointsDeOptimizeMethod' nos dados personalizados será enviado.  Quando o instantâneo da próxima exceção é coletado, as variáveis locais ficam disponíveis. Depois que o instantâneo é coletado, ele reotimiza o código para garantir o desempenho. 

> [!NOTE]
> A desotimização requer a instalação da extensão de site do Application Insights.

Quando um instantâneo é solicitado para uma exceção específica, o coletor de instantâneo inicia o monitoramento do pipeline (AppDomain.FirstChanceException) de manipulação de exceção do seu aplicativo. Quando a exceção ocorre novamente, o coletor inicia um instantâneo (evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e 'SnapshotStart' nos dados personalizados). Em seguida, é feita uma cópia de sombra do processo em execução (a tabela de página será duplicada). Normalmente, isso pode demorar 10 a 20 milissegundos. Depois disso, um evento personalizado do Application Insights com o nome 'AppInsightsSnapshotCollectorLogs' e 'SnapshotStop' nos dados personalizados é enviado. Quando o processo bifurcado é criado, o total de memória paginável aumenta na mesma quantidade da memória paginável do seu aplicativo em execução (o conjunto de trabalho será muito menor). Enquanto o processo de aplicativo está funcionando normalmente, a memória da cópia de sombra do processo é despejada no disco e carregada no Application Insights. Depois que o instantâneo é carregado, um evento personalizado do Application Insights com o nome 'UploadSnapshotFinish' é enviado.

## <a name="is-the-snapshot-collector-working-properly"></a>O coletor de instantâneos está funcionando corretamente?

### <a name="how-to-find-snapshot-collector-logs"></a>Como localizar os logs do coletor de instantâneos
Os logs do coletor de instantâneos são enviados à sua conta do Application Insight se o [pacote NuGet do coletor de instantâneos](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector) versão 1.1.0 ou posterior está instalado. Verifique se o *ProvideAnonymousTelemetry* não está definido como false (o valor é true por padrão).

* Navegue até o recurso Application Insights no portal do Azure.
* Clique em *Pesquisar* na seção Visão geral.
* Digite a seguinte cadeia de caracteres na caixa de pesquisa:
    ```
    AppInsightsSnapshotCollectorLogs OR AppInsightsSnapshotUploaderLogs OR UploadSnapshotFinish OR UploaderStart OR UploaderStop
    ```
* Observação: altere o *intervalo de tempo* se necessário.

![Captura de tela de Pesquisar logs do coletor de instantâneos](./media/app-insights-troubleshoot-snapshot-debugger/001.png)


### <a name="examine-snapshot-collector-logs"></a>Examinar os logs do coletor de instantâneos
Ao pesquisar logs do coletor de instantâneos, deve haver eventos 'UploadSnapshotFinish' no intervalo de tempo procurado. Se você não vir o botão 'Abrir Instantâneo de Depuração' para abrir o instantâneo, envie um email para snapshothelp@microsoft.com com chave de instrumentação do Application Insights.

![Captura de tela de Examinar logs do coletor de instantâneos](./media/app-insights-troubleshoot-snapshot-debugger/005.png)

## <a name="i-cannot-find-a-snapshot-to-open"></a>Não consigo localizar um instantâneo para abri-lo
Se as etapas a seguir não ajudarem a solucionar o problema, envie um email para snapshothelp@microsoft.com com chave de instrumentação do Application Insights.

### <a name="step-1-make-sure-your-application-is-sending-telemetry-data-and-exception-data-to-application-insights"></a>Etapa 1: Verificar se que seu aplicativo envia dados de exceção e dados de telemetria ao Application Insights
Navegue até o recurso do Application Insights, verifique se há dados enviados de seu aplicativo.

### <a name="step-2-make-sure-snapshot-collector-is-added-correctly-to-your-applications-application-insights-telemetry-pipeline"></a>Etapa 2: Verificar se o coletor de instantâneo foi adicionado corretamente ao pipeline do Application Insights Telemetry do aplicativo
Se você encontrar logs na etapa 'Como encontrar logs do coletor de instantâneos', o coletor de instantâneos foi adicionado ao seu projeto corretamente; ignore esta etapa.

Se não houver nenhum log do coletor de instantâneos, verifique o seguinte:
* Para aplicativos ASP.NET clássicos, verifique esta linha  *<Add Type="Microsoft.ApplicationInsights.SnapshotCollector.SnapshotCollectorTelemetryProcessor, Microsoft.ApplicationInsights.SnapshotCollector">*  no arquivo *Applicationinsights*.

* Para aplicativos do ASP.NET Core, verifique se o *ITelemetryProcessorFactory* com *SnapshotCollectorTelemetryProcessor* foi adicionado aos serviços *IServiceCollection*.

* Verifique também se você está usando a chave de instrumentação correta em seu aplicativo publicado.

* O coletor de instantâneos não dá suporte a várias chaves de instrumentação em um mesmo aplicativo; ele enviará instantâneos para a chave de instrumentação da primeira exceção observada.

* Se você definir o *InstrmentationKey* manualmente no seu código, atualize o elemento *InstrumentationKey* do *Applicationinsights*.

### <a name="step-3-make-sure-the-minidump-uploader-is-started"></a>Etapa 3: Verificar se que o carregador de minidespejo foi iniciado
Nos logs do coletor de instantâneos, procure *UploaderStart* (digite UploaderStart na caixa de texto de pesquisa). Deve haver um evento de quando o coletor de instantâneo monitorou a primeira exceção. Se esse evento não existir, verifique outros logs para obter detalhes. Uma maneira possível de resolver esse problema é reiniciando seu aplicativo.

### <a name="step-4-make-sure-snapshot-collector-expressed-its-intent-to-collect-snapshots"></a>Etapa 4: Verificar se o que coletor de instantâneos expressou sua intenção de coletar instantâneos
Nos logs do coletor de instantâneos, procure *RequestSnapshots* (digite ```RequestSnapshots``` na caixa de texto de pesquisa).  Se não houver nenhum, verifique a configuração, por exemplo, *ThresholdForSnapshotting*, que indica o número de um exceções específicas que podem ocorrer antes do início da coleta de instantâneos.

### <a name="step-5-make-sure-that-snapshot-is-not-disabled-due-to-memory-protection"></a>Etapa 5: Verificar se o instantâneo não está desabilitado devido à proteção de memória
Para proteger o desempenho do aplicativo, um instantâneo somente será capturado quando houver um buffer de memória em boas condições. Nos logs do coletor de instantâneos, procure 'CannotSnapshotDueToMemoryUsage'. Nos dados personalizados do evento, ele indicará um motivo detalhado. Se seu aplicativo estiver em execução em um aplicativo Web do Azure, a restrição poderá ser rígida. O aplicativo Web do Azure reiniciará seu aplicativo quando certas regras de memória forem atendidas. Você pode tentar aumentar seu plano de serviço para máquinas com mais memória a fim de resolver esse problema.

### <a name="step-6-make-sure-snapshots-were-captured"></a>Etapa 6: Verificar se instantâneos foram capturados
Nos logs do coletor de instantâneos, procure ```RequestSnapshots```.  Se nenhum estiver presente, verifique a configuração, por exemplo, ```ThresholdForSnapshotting```, que indica o número de exceções específicas antes de coletar um instantâneo.

### <a name="step-7-make-sure-snapshots-are-uploaded-correctly"></a>Etapa 7: Verificar se os instantâneos foram carregados corretamente
Nos logs do coletor de instantâneos, procure ```UploadSnapshotFinish```.  Se ele não estiver presente, envie um email para snapshothelp@microsoft.com com chave de instrumentação do Application Insights. Se o evento existir, abra um dos logs e copie o valor de 'SnapshotId' em Dados Personalizados. Em seguida, procure o valor. Isso localizará a exceção correspondente ao instantâneo. Clique na exceção e abra o instantâneo de depuração. (Se não houver nenhuma exceção correspondente, a telemetria de exceção poderá ser de amostra, devido ao alto volume; tente outra snapshotId.)

![Captura de tela de Localizar SnapshotId](./media/app-insights-troubleshoot-snapshot-debugger/002.png)

![Captura de tela de Abrir exceção](./media/app-insights-troubleshoot-snapshot-debugger/004b.png)

![Captura de tela de Abrir instantâneo de depuração](./media/app-insights-troubleshoot-snapshot-debugger/003.png)

## <a name="snapshot-view-local-variables-are-not-complete"></a>As variáveis de local de exibição de instantâneo não estão concluídas

Algumas das variáveis de local estão ausentes. Se seu aplicativo estiver executando o código da versão, o compilador otimizará algumas variáveis imediatamente. Por exemplo:

```csharp
    const int a = 1; // a will be discarded by compiler and the value 1 will be inline.
    Random rand = new Random();
    int b = rand.Next() % 300; // b will be discarded and the value will be directly put to the 'FindNthPrimeNumber' call stack.
    long primeNumber = FindNthPrimeNumber(b);
```

Se seu caso for diferente, pode ser um bug. Envie um email para snapshothelp@microsoft.com com a chave de instrumentação do Application Insights junto com o trecho de código.

## <a name="snapshot-view-cannot-obtain-value-of-the-local-variable-or-argument"></a>Exibição de instantâneo: não é possível obter o valor da variável de local ou argumento
Verifique se a [extensão de site do Application Insights](https://www.siteextensions.net/packages/Microsoft.ApplicationInsights.AzureWebSites/) está instalada. Se o problema persistir, envie um email para snapshothelp@microsoft.com com chave de instrumentação do Application Insights.
