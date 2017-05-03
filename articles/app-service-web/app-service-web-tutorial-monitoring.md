---
title: Monitorar um aplicativo Web | Microsoft Docs
description: Saiba como configurar o Monitoramento em seu aplicativo Web
services: App-Service
keywords: 
author: btardif
ms.author: byvinyal
ms.date: 04/04/2017
ms.topic: article
ms.service: app-service-web
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 8d9b4a4fa3b62659fc7e2aa1c6329fdc5e01fe39
ms.lasthandoff: 04/21/2017

---
# <a name="monitor-app-service"></a>Monitorar o Serviço de Aplicativo
Este tutorial orienta você por meio de monitoramento do aplicativo e usar as ferramentas de plataforma interno para resolver problemas quando eles ocorrerem.

Cada seção deste documento será um recurso específico. Usando os recursos juntos permitem que você:
- Identificar um problema em seu aplicativo.
- Determinando quando o problema é causado por seu código ou a plataforma.
- Restrinja a origem do problema em seu código.
- Depure e corrija o problema.

## <a name="before-you-begin"></a>Antes de começar
- Você precisa de um aplicativo Web para monitorar e seguir as etapas destacadas. 
    - É possível criar um aplicativo seguindo as etapas descritas no tutorial [Criar um aplicativo ASP.NET no Azure com Banco de dados SQL](app-service-web-tutorial-dotnet-sqldatabase.md).

- Caso queira testar a **Depuração Remota** do seu aplicativo, você precisará do Visual Studio. 
    - Se ainda não tiver o Visual Studio 2017 instalado, é possível baixar e usar o [Visual Studio 2017 Community Edition](https://www.visualstudio.com/downloads/) gratuito. 
    - Verifique se você habilitou o **desenvolvimento do Azure** durante a instalação do Visual Studio.

## <a name="metrics"></a>Etapa 1 - Métricas de exibição
**Métricas** são úteis para entender: 
- Integridade do aplicativo
- Desempenho do apliativo
- Consumo de recursos

Ao investigar um problema de aplicativo, revisar as métricas é um bom lugar para começar. O portal do Azure tem uma maneira rápida para inspecionar visualmente as métricas de seu aplicativo, usando o **Azure Monitor**.

Métricas fornecem uma exibição histórica entre várias agregações chave para seu aplicativo. Para qualquer aplicativo hospedado no serviço de aplicativo, será necessário monitorar o aplicativo Web e o plano de Serviço de Aplicativo.

> [!NOTE]
> Um plano do Serviço de Aplicativo representa a coleção de recursos físicos usados para hospedar seus aplicativos. Todos os aplicativos atribuídos a um plano do Serviço de Aplicativo compartilham os recursos definidos por ele, permitindo que você economize ao hospedar vários aplicativos.
>
> Os Planos do Serviço de Aplicativo definem:
> * Região: Norte da Europa, Leste dos EUA, Sudeste da Ásia, etc.
> * Tamanho da instância: Pequeno, médio, grande, etc.
> * Contagem da Escala: uma, duas ou três instâncias, etc.
> * SKU: Gratuito, Compartilhado, Básico, Standard, Premium, etc.

Para revisar as métricas para seu aplicativo Web, vá para a folha **Visão geral** do aplicativo que você deseja monitorar. A partir daqui, você pode exibir um gráfico de métricas do seu aplicativo como um **bloco de monitoramento**. Clique no bloco para editar e configurar as métricas e o intervalo de tempo a serem exibidos. 

Por padrão a folha de recursos fornece uma exibição para as solicitações do aplicativo e os erros de última hora.
![Monitorar o aplicativo](media/app-service-web-tutorial-monitoring/app-service-monitor.png)

Como você pode ver no exemplo, temos um aplicativo que está gerando muitos **erros do servidor HTTP**. Alto volume de erros é a indicação primeiro precisamos investigar esse aplicativo.

> [!TIP]
> Saiba mais sobre o Azure Monitor com os seguintes links:
> - [Introdução ao Azure Monitor](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Métricas do Azure](..\monitoring-and-diagnostics\monitoring-overview-metrics.md)
> - [Métricas compatíveis com o Azure Monitor](..\monitoring-and-diagnostics\monitoring-supported-metrics.md#microsoftwebsites-including-functions)
> - [Painéis do Azure](..\azure-portal\azure-portal-dashboards.md)

## <a name="alerts"></a>Etapa 2 - Configurar Alertas
**Alertas** podem ser configurados para serem acionados com condições específicas para seu aplicativo.

Em [etapa 1 - visualizar métricas](#metrics), vimos que o aplicativo tinha um grande número de erros. 

Permite configurar um alerta para ser notificado automaticamente quando ocorrem erros. Nesse caso, queremos que o alerta para enviar e email sempre que o número de erros HTTP 50 X fica acima de um determinado limite.

Para criar um alerta, navegue até **monitoramento** > **alertas** e clique em **[+] Adicionar alerta**.

![Alertas](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts.png)

Forneça valores para a configuração de alertas:
- **Recurso:** o site para monitorar com o alerta. 
- **Nome:** um nome para o alerta, nesse caso: *HTTP alta 50 X*.
- **Descrição:** explicação de texto sem formatação do que esse alerta está observando.
- **Alerta sobre:** alertas podem examinar eventos ou métricas, para este exemplo Estamos analisando as métricas.
- **Métrica:** que métrica para monitorar, nesse caso: *erros do servidor HTTP*.
- **Condição:** quando um alerta, selecione nesse caso o *maior* opção.
- **Limite:** o que é o valor a ser procurado, nesse caso: *400*.
- **Período:** alertas operam sobre o valor médio de uma métrica. Períodos menores geram alertas mais importantes. Neste caso temos *5 minutos*. 
- **Os proprietários e colaboradores de e-mail:** nesse caso: *Habilitado*.

Agora que o alerta foi criado um email é enviado sempre que o aplicativo fica acima do limite configurado. Alertas ativos também podem ser analisados no portal do Azure.

![Acionado alertas](media/app-service-web-tutorial-monitoring/app-service-monitor-alerts-triggered.png)


> [!TIP]
> Saiba mais sobre Alertas do Azure com os seguintes links:
> - [O que são alertas no Microsoft Azure?](..\monitoring-and-diagnostics\monitoring-overview-alerts.md)
> - [Executar uma Ação com Base nas Métricas](..\monitoring-and-diagnostics\monitoring-overview.md)
> - [Criar alertas de métricas](..\monitoring-and-diagnostics\insights-alerts-portal.md)

## <a name="companion"></a> Etapa 3 - Complemento do Serviço de Aplicativo
O **Complemento do Serviço de Aplicativo** oferece uma maneira conveniente para monitorar o aplicativo com uma experiência nativa no dispositivo móvel (iOS ou Android).

Use o Complemento do Serviço de Aplicativo para:
- Analisar as métricas de aplicativo
- Analisar e agir sobre alertas de aplicativos e recomendações
- Executar a solução de problemas básicos (procurar, iniciar, parar, reiniciar o aplicativo)
- Recebe notificações por push para eventos críticos.

![Complemento do Serviço de Aplicativo](media/app-service-web-tutorial-monitoring/app-service-companion.png)

[![Loja de Aplicativos do Complemento do Serviço de Aplicativo](media/app-service-web-tutorial-monitoring/app-service-companion-appStore.png)](https://itunes.apple.com/app/azure-app-service-companion/id1146659260)
[![Google Play do Complemento do Serviço de Aplicativo ](media/app-service-web-tutorial-monitoring/app-service-companion-googlePlay.png)](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

É possível instalar o complemento do Serviço de Aplicativo pela [App Store](https://itunes.apple.com/app/azure-app-service-companion/id1146659260) ou pelo [Google Play](https://play.google.com/store/apps/details?id=azureApps.AzureApps)

## <a name="diagnose"></a> Etapa 4 - Diagnosticar e resolver problemas
**Diagnosticar e resolver problemas** ajuda a separar questões de aplicativo forma a problemas de plataforma. Ele também pode sugerir possíveis atenuações para deixar seu aplicativo Web para íntegro.
 
![Diagnosticar e Resolver Problemas](media/app-service-web-tutorial-monitoring/app-service-monitor-diagnosis.png)

Continuar com as etapas anteriores do formulário de exemplo, podemos ver que o aplicativo tenha sido com disponível problemas. Por outro lado, a disponibilidade de plataforma não foi movida de 100%.

Quando o aplicativo está tendo problemas e a plataforma permaneça em funcionamento, é uma indicação clara de que estamos lidando com um problema de aplicativo.

## <a name="logging"></a> Etapa 5 - Registrar em Log
Agora que podemos ter limitados as falhas para um problema de aplicativo, podemos ver os logs de aplicativo e servidor para obter mais informações.

O registro em log permite coletar ambos os logs do **Diagnóstico de Aplicativos** e do **Diagnóstico de Servidor Web** para seu aplicativo Web.

### <a name="application-diagnostics"></a>Diagnóstico de Aplicativos
O diagnóstico de aplicativo permite capturar rastreamentos produzidos pelo aplicativo em tempo de execução. 

Adicionar rastreamento para seu aplicativo muito melhora a capacidade de depurar e problemas de Pinpoint.

No ASP.NET, é possível registrar rastreamentos de aplicativo usando [System.Diagnostics.Trace clase](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx) para gerar eventos que são capturados pela infraestrutura de log. Você também pode especificar a severidade do rastreamento para filtragem mais fácil.

```csharp
public ActionResult Delete(Guid? id)
{
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id);
    if (id == null)
    {
        System.Diagnostics.Trace.TraceError("/Todos/Delete/ failed, ID is null");
        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
    }
    Todo todo = db.Todoes.Find(id);
    if (todo == null)
    {
        System.Diagnostics.Trace.TraceWarning("/Todos/Delete/ failed, ID: " + id + " could not be found");
        return HttpNotFound();
    }
    System.Diagnostics.Trace.TraceInformation("GET /Todos/Delete/" + id + "completed successfully");
    return View(todo);
}
```
Para habilitar o log de aplicativo, vá para **monitoramento** > **Logs de diagnóstico** e habilitar o log de aplicativo usando a alterna.

![Monitorar o aplicativo](media/app-service-web-tutorial-monitoring/app-service-monitor-applogs.png)

Os Logs de Aplicativo podem ser armazenados em um sistema de arquivos do Aplicativo Web ou enviados para armazenamento de blobs. Para cenários de produção, é recomendável usar o armazenamento de blobs.

> [!IMPORTANT]
> Habilitar o registro no log tem um impacto sobre o desempenho do aplicativo e a utilização de recursos. Para cenários de produção, logs de erros são recomendados. Somente habilite o registro no log mais detalhado quando investigar problemas.

 ### <a name="web-server-diagnostics"></a>Diagnóstico de Servidor Web
Logs do servidor Web são gerados, mesmo que seu aplicativo não é instrumentado. O Serviço de Aplicativo pode coletar três tipos diferentes de logs do servidor:

- **Log do Servidor Web** 
    - Informações sobre transações HTTP usando o [formato do arquivo de log estendido do W3C](https://msdn.microsoft.com/library/windows/desktop/aa814385.aspx). 
    - Útil ao determinar métricas globais do site como o número de solicitações processadas ou a quantidade de solicitações de um endereço IP específico.
- **Logs de Erros Detalhados** 
    - Informações detalhadas de erros para códigos de status HTTP que indiquem uma falha (código de status 400 ou superior). 
    - [Saiba mais sobre o log de erro detalhado](https://www.iis.net/learn/troubleshoot/diagnosing-http-errors/how-to-use-http-detailed-errors-in-iis)
- **Rastreamento de Solicitações com Falha** 
    - Informações detalhadas sobre solicitações com falha, incluindo um rastreamento dos componentes IIS usados para processar a solicitação e o tempo levado em cada componente. 
    - O logs de solicitações com falha são úteis ao tentar isolar a causa de um erro HTTP específico.
    - [Saiba mais sobre falha de solicitação de rastreamento](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing/troubleshooting-failed-requests-using-tracing-in-iis)

Para habilitar o Log de Servidor:
- vá para **Monitoramento** > **Logs de Diagnóstico**. 
- Habilite os diferentes tipos de Diagnóstico de Servidor Web usando as alternâncias.

![Monitorar o aplicativo](media/app-service-web-tutorial-monitoring/app-service-monitor-serverlogs.png)

> [!IMPORTANT]
> Habilitar o registro no log tem um impacto sobre o desempenho do aplicativo e a utilização de recursos. Para Cenários de Produção, os Logs de Erros são recomendados, Somente habilite o registro no log mais detalhado quando investigar problemas.

### <a name="accessing-logs"></a>Acessar Logs
Os logs armazenados no armazenamento de blobs são acessados usando o Gerenciador de Armazenamento do Azure. Os logs armazenados no sistema de arquivos do aplicativo Web são acessados por meio de FTP nos seguintes caminhos:

- **Logs de aplicativos** - `%HOME%/LogFiles/Application/`.
    - Essa pasta contém um ou mais arquivos de texto que contêm informações produzidas pelo log do aplicativo.
- **Falha de solicitação de rastreamento** - `%HOME%/LogFiles/W3SVC#########/`. 
    - Esta pasta contém um arquivo XSL e um ou mais arquivos XML. 
- **Logs de erro detalhadas** - `%HOME%/LogFiles/DetailedErrors/`. 
    - Esta pasta contém um ou mais arquivos .htm com informações abrangentes sobre erros HTTP gerados pelo aplicativo.
- **Logs do Web Server** - `%HOME%/LogFiles/http/RawLogs`. 
    - Esta pasta contém um ou mais arquivos de texto formatados usando o formato do arquivo de log estendido do W3C.

## <a name="streaming"></a> Etapa 6 - Streaming de Log
Logs de streaming são convenientes durante a depuração de um aplicativo uma vez que ele economiza tempo em comparação comparado [acessar os logs de](#Accessing-Logs) por meio de FTP.

O Serviço de Aplicativo pode fazer streaming de **Logs do Aplicativo** e **Logs do Servidor Web** conforme eles são gerados. 

> [!TIP]
> Antes de tentar fazer streaming de logs certifique-se de que você habilitou a coleta de logs, conforme descrito na seção [Registrar em Log](#logging).

Para transmitir logs, vá para **monitoramento**> **fluxo de Log**. Selecione **Logs do Aplicativo** ou **Logs do Servidor Web**, dependendo de quais informações você está procurando. A partir daqui, também será possível pausar, reiniciar e limpar o buffer.

![Logs de streaming](media/app-service-web-tutorial-monitoring/app-service-monitor-logstream.png)

> [!TIP]
> Os logs são gerados apenas quando há tráfego no aplicativo, também é possível aumentar o nível de detalhes dos logs para obter mais eventos ou informações.

## <a name="remote"></a> Etapa 7 - Depuração Remota
Após você ter pin pontas a origem dos problemas de aplicativos, use **depuração remota** para percorrer o código.

A Depuração remota permite anexar um depurador ao seu aplicativo Web em execução na nuvem. É possível pode definir pontos de interrupção, manipular a memória diretamente, percorrer o código e até mesmo alterar o caminho do código, exatamente como para um aplicativo executado localmente.

Para anexar o depurador ao seu aplicativo em execução na nuvem:

- Usando o Visual Studio 2017, abra a solução para o aplicativo que deseja depurar 
- Defina alguns pontos de interrupção, exatamente como faria para desenvolvimento local.
- Abra o **cloud explorer** (ctr + /, ctrl + x).
- Faça logon com suas credenciais do Azure, conforme necessário.
- Localize o aplicativo que deseja depurar
- Selecione **Anexar Depurador** do painel **Ações**.

![Depuração Remota](media/app-service-web-tutorial-monitoring/app-service-monitor-vsdebug.png)

O Visual Studio configura seu aplicativo para depuração remota e inicia uma janela do navegador que navega até o aplicativo. Navegue pelo aplicativo para disparar pontos de interrupção e percorrer o código.

> [!WARNING]
> A execução em modo de depuração em produção não é recomendada. Se o aplicativo de produção não for dimensionado para várias instâncias de servidor, a depuração impedirá que o servidor Web responda a outras solicitações. Para solucionar problemas de produção, os melhores recursos são [configurar o registro em log](#logging) e [Application Insight](#insights).



## <a name="explorer"></a> Etapa 8 - Gerenciador de Processos
Quando seu aplicativo é dimensionado para mais de uma instância, **o process explorer** pode ajudar a identificar problemas específicos da instância.

Use **Gerenciador de Processos** para:

- Enumere todos os processos entre diferentes instâncias do seu plano de Serviço de Aplicativo.
- Analise detalhadamente e exiba os identificadores e módulos associados a cada processo. 
- Exibir CPU, Conjunto de trabalho e contagem de Threads no nível de processo para ajudá-lo a identificar processos sem controle
- Localize identificadores de arquivos abertos e, até mesmo, encerre uma instância de processo específico.

O Gerenciador de Processos pode estar em **Monitoramento** > **Explorador de Processos**.

![Gerenciador de Processos](media/app-service-web-tutorial-monitoring/app-service-monitor-processexplorer.png)


## <a name="insights"></a> Etapa 9 - Application Insights
O **Application Insights** fornece perfil de aplicativo e recursos avançados de monitoramento para seu aplicativo. 

Use o Insights de Aplicativos para detectar e diagnosticar exceções e problemas de desempenho no Aplicativo Web.

É possível habilitar o Application Insights para seu aplicativo Web em **Monitoramento** > **Application Insights** 

> [!NOTE]
> O Application Insights pode solicitar a instalação da extensão de site do Application Insights para iniciar a coleta de dados. Instalar a extensão de site causa o reinício do aplicativo.

![Application Insights](media/app-service-web-tutorial-monitoring/app-service-monitor-appinsights.png)

O Application Insights possui um avançado conjunto de recursos, para saber mais siga os links incluídos na seção [Próximas etapas](#next).

## <a name="next"></a> Próximas etapas

 - [O que é o Application Insights](..\application-insights\app-insights-overview.md)
 - [Monitorar o desempenho do aplicativo Web do Azure com o Application Insights](..\application-insights\app-insights-azure-web-apps.md)
 - [Monitorar a disponibilidade e capacidade de resposta de qualquer site da Web com o Application Insights](..\application-insights\app-insights-monitor-web-app-availability.md)
