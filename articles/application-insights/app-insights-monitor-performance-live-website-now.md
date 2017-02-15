---
title: "Diagnosticar problemas de desempenho em um site IIS em execução | Microsoft Docs"
description: "Monitore o desempenho do site sem implantá-lo novamente. Use um autônomo ou o SDK do Application Insights para obter a telemetria de dependência."
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: ee9ebc23ce805bb4665669077a4d3fddf4c43e32
ms.openlocfilehash: a190b1990a4ae4e7ad52cc1a7e802c8002522917


---
# <a name="instrument-web-apps-at-runtime-with-application-insights"></a>Instrumentar aplicativos Web no tempo de execução com o Application Insights


Você pode instrumentar um aplicativo Web ativo com o Application Insights do Azure, sem a necessidade de modificar ou reimplantar o código. Se seus aplicativos são hospedados por um servidor IIS local, instale o Monitor de Status. Se eles são aplicativos Web do Azure ou são executados em uma VM do Azure, você pode instalar a extensão do Application Insights. (Também há artigos separados sobre como instrumentar os [aplicativos Web J2EE dinâmicos](app-insights-java-live.md) e os [Serviços de Nuvem do Azure](app-insights-cloudservices.md).)

![gráficos de exemplo](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Você tem a opção de três rotas para aplicar o Application Insights nos aplicativos Web .NET:

* **Tempo de compilação:** [Adicionar o SDK do Application Insights] [ greenbrown] ao código do aplicativo Web.
* **Tempo de execução:** instrumente seu aplicativo Web no servidor, conforme descrito abaixo, sem recompilar e reimplantar o código.
* **Ambos:** crie o SDK em seu código de aplicativo Web e também aplique as extensões de tempo de execução. Obtenha o melhor de ambas as opções.

Aqui está um resumo do que você tem com cada rota:

|  | Tempo de compilação | Tempo de execução |
| --- | --- | --- |
| Solicitações e exceções |Sim |Sim |
| [Exceções mais detalhadas](app-insights-asp-net-exceptions.md) | |Sim |
| [Diagnóstico de dependência](app-insights-asp-net-dependencies.md) |No .NET 4.6+, mas menos detalhes |Sim, detalhes completos: códigos de resultado, texto do comando SQL, Verbo HTTP|
| [Contadores de desempenho do sistema](app-insights-performance-counters.md) |Sim |Sim |
| [API de telemetria personalizada][api] |Sim | |
| [Integração do log de rastreamento](app-insights-asp-net-trace-logs.md) |Sim | |
| [Exibição da página e dados do usuário](app-insights-javascript.md) |Sim | |
| Não há necessidade de recompilar o código |Não | |

## <a name="instrument-your-web-app-at-run-time"></a>Instrumentar seu aplicativo Web em tempo de execução
É necessário ter uma assinatura do [Microsoft Azure](http://azure.com) .

### <a name="if-your-app-is-an-azure-web-app-or-cloud-service"></a>Se seu aplicativo for um aplicativo Web do Azure ou Serviço de Nuvem
* Selecione o Application Insights no painel de controle do aplicativo no Azure.

    [Saiba mais](app-insights-azure.md).

### <a name="if-your-app-is-hosted-on-your-iis-server"></a>Se seu aplicativo for hospedado no servidor IIS
1. No servidor Web IIS, entre com as credenciais de administrador.
2. Baixe e execute o [instalador do Monitor de Status](http://go.microsoft.com/fwlink/?LinkId=506648).  
3. Selecione o aplicativo Web ou o site que deseja monitorar e configure o recurso no qual você deseja ver os resultados no Portal do Application Insights. Você deve estar conectado ao Microsoft Azure.

    ![Escolha um aplicativo e um recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalmente, você opta por configurar um novo recurso e um [grupo de recursos][roles].

    Ou então, use um recurso existente se você já configurou os [testes da Web] [ availability] para o seu site, ou o [monitoramento de cliente Web][client].
4. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web será interrompido por um período curto.
5. Observe que ApplicationInsights.config foi inserido nos sites que você deseja monitorar.

    ![Localize o arquivo .config junto com os arquivos de código do aplicativo Web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)
   

#### <a name="want-to-reconfigure-later"></a>Deseja (re)configurar mais tarde?
Após concluir o assistente, você pode reconfigurar o agente quando desejar. É possível também usá-lo se você instalou o agente, mas ocorreu algum problema na configuração inicial.

![Clique no ícone do Application Insights na barra de tarefas](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## <a name="view-performance-telemetry"></a>Exibir desempenho de telemetria
Entre no [portal do Azure](https://portal.azure.com), procure o Application Insights e abra o recurso que você criou.

![Selecione Navegar, Application Insights e selecione seu aplicativo.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Abra a folha de Desempenho para ver as solicitações, tempos de resposta, dependências e outros dados.

![Desempenho](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Clique em qualquer gráfico para abrir uma exibição mais detalhada.

Você pode [editar, reorganizar, salvar](app-insights-metrics-explorer.md) e fixar gráficos ou a folha inteira em um [painel](app-insights-dashboards.md).

## <a name="dependencies"></a>Dependências
O grafo de Duração da Dependência mostra o tempo gasto por chamadas do seu aplicativo para componentes externos, como bancos de dados, APIs REST ou Armazenamento de Blob do Azure.

Para segmentar o gráfico por chamadas para dependências diferentes: edite o gráfico, ative o Agrupamento e agrupe por Dependência, o Tipo de Dependência ou o Desempenho de Dependência.

![Dependência](./media/app-insights-monitor-performance-live-website-now/23-dep.png)

## <a name="performance-counters"></a>Contadores de desempenho
Clique em Servidores na folha de visão geral para ver gráficos de contadores de desempenho do servidor, como uso de memória e ocupação da CPU.

Se houver várias instâncias do servidor, convém editar gráficos para agrupar por instância de função.

![Servidores](./media/app-insights-monitor-performance-live-website-now/22-servers.png)

Você também pode alterar o conjunto de contadores de desempenho que são reportados pelo SDK. 

## <a name="exceptions"></a>Exceções
![Clique no gráfico de exceções do servidor](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Você pode fazer busca detalhada até exceções específicas (dos últimos sete dias) e obter rastreamentos de pilha e dados de contexto.

## <a name="sampling"></a>Amostragem
Se o aplicativo enviar muitos dados e se você estiver usando o SDK do Application Insights para o ASP.NET versão 2.0.0-beta3 ou posterior, o recurso de amostragem adaptável poderá operar e enviar apenas uma porcentagem de sua telemetria. [Saiba mais sobre amostragem.](app-insights-sampling.md)

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="connection-errors"></a>Erros de conexão
Você precisa abrir [algumas portas de saída](app-insights-ip-addresses.md#outgoing-ports) no firewall do servidor para permitir que o Monitor de Status funcione.

### <a name="no-telemetry"></a>Sem telemetria?
* Use seu site para gerar alguns dados.
* Aguarde alguns minutos até que os dados cheguem e clique em **Atualizar**.
* Abrir a Pesquisa de Diagnóstico (o bloco de Pesquisa) para ver os eventos individuais. Eventos geralmente são visíveis na Pesquisa de Diagnóstico antes de os dados agregados aparecem nos gráficos.
* Abra o Monitor de Status e selecione seu aplicativo no painel esquerdo. Verifique se há mensagens de diagnóstico para este aplicativo na seção "Configuração de notificações":

  ![Abra a folha de Desempenho para ver as solicitações, tempos de resposta, dependências e outros dados](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)
* Verifique se o firewall do servidor permite o tráfego de saída nas portas listadas acima.
* No servidor, se você encontrar uma mensagem sobre "permissões insuficientes", tente fazer o seguinte:
  * No Gerenciador do IIS, selecione o pool de aplicativos, abra **Configurações Avançadas** e no **Modelo de Processo**, anote a identidade.
  * No painel de controle de gerenciamento do computador, adicione essa identidade ao grupo Usuários do Monitor de Desempenho.
* Se você tiver o MMA/SCOM instalado em seu servidor, algumas versões poderão entrar em conflito. Desinstale o SCOM e o Monitor de Status e reinstale as versões mais recentes.
* Consulte [Solução de problemas][qna].

## <a name="system-requirements"></a>Requisitos do Sistema
Suporte de sistema operacional para Application Insights Status Monitor no servidor:

* Windows Server 2008
* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016

com o SP mais recente e o .NET Framework 4.5

No lado do cliente, Windows 7, 8, 8.1 e 10, novamente com o .NET Framework 4.5

Suporte ao IIS: IIS 7, 7,5, 8 e 8.5 (o IIS é obrigatório)

## <a name="automation-with-powershell"></a>Automação com o PowerShell
Você pode iniciar e interromper o monitoramento usando o PowerShell no servidor IIS.

Primeiro, importe o módulo do Application Insights:

`Import-Module 'C:\Program Files\Microsoft Application Insights\Status Monitor\PowerShell\Microsoft.Diagnostics.Agent.StatusMonitor.PowerShell.dll'`

Saiba quais aplicativos estão sendo monitorados:

`Get-ApplicationInsightsMonitoringStatus [-Name appName]`

* `-Name` (Opcional) O nome de um aplicativo Web.
* Exibe o status de monitoramento do Application Insights para cada aplicativo Web (ou o aplicativo nomeado) nesse servidor IIS.
* Retorna `ApplicationInsightsApplication` para cada aplicativo:

  * `SdkState==EnabledAfterDeployment`: o aplicativo está sendo monitorado e foi instrumentado em tempo de execução, pela ferramenta Monitor de Status ou pelo `Start-ApplicationInsightsMonitoring`.
  * `SdkState==Disabled`: o aplicativo não é instrumentado para o Application Insights. Ele nunca foi instrumentado ou o monitoramento em tempo de execução foi desabilitado com a ferramenta Monitor de Status ou com o `Stop-ApplicationInsightsMonitoring`.
  * `SdkState==EnabledByCodeInstrumentation`: o aplicativo foi instrumentado por meio da adição do SDK ao código-fonte. Seu SDK não pode ser atualizado ou interrompido.
  * `SdkVersion` mostra a versão em uso para o monitoramento do aplicativo.
  * `LatestAvailableSdkVersion`mostra a versão atualmente disponível na galeria do NuGet. Para atualizar o aplicativo para esta versão, use `Update-ApplicationInsightsMonitoring`.

`Start-ApplicationInsightsMonitoring -Name appName -InstrumentationKey 00000000-000-000-000-0000000`

* `-Name` O nome do aplicativo no IIS
* `-InstrumentationKey` O ikey do recurso Application Insights em que você deseja que os resultados sejam exibidos.
* Este cmdlet afeta apenas os aplicativos que ainda não estão instrumentados - ou seja, SdkState==NotInstrumented.

    O cmdlet não afeta um aplicativo que já esteja instrumentado, em tempo de compilação por meio da adição do SDK ao código, ou em tempo de execução por meio de um uso anterior desse cmdlet.

    A versão do SDK usada para instrumentar o aplicativo é a versão baixada mais recentemente para este servidor.

    Para baixar a versão mais recente, use Update-ApplicationInsightsVersion.
* Retorna `ApplicationInsightsApplication` se há êxito. Se ele falhar, registrará em log um rastreamento para stderr.

          Name                      : Default Web Site/WebApp1
          InstrumentationKey        : 00000000-0000-0000-0000-000000000000
          ProfilerState             : ApplicationInsights
          SdkState                  : EnabledAfterDeployment
          SdkVersion                : 1.2.1
          LatestAvailableSdkVersion : 1.2.3

`Stop-ApplicationInsightsMonitoring [-Name appName | -All]`

* `-Name` O nome de um aplicativo no IIS
* `-All` Para o monitoramento de todos os aplicativos desse servidor IIS para o qual `SdkState==EnabledAfterDeployment`
* Para o monitoramento de aplicativos especificados e remove a instrumentação. Ele só funciona para aplicativos que foram instrumentados em tempo de execução usando a ferramenta Monitoramento de Status ou Start-ApplicationInsightsApplication. (`SdkState==EnabledAfterDeployment`)
* Retorna ApplicationInsightsApplication.

`Update-ApplicationInsightsMonitoring -Name appName [-InstrumentationKey "0000000-0000-000-000-0000"`]

* `-Name`: o nome de um aplicativo Web no IIS.
* `-InstrumentationKey` (Opcional.) Use isso para alterar o recurso para o qual a telemetria do aplicativo é enviada.
* Este cmdlet:
  * Atualiza o aplicativo nomeado para a versão do SDK baixado mais recentemente para esta máquina. (Só funciona se `SdkState==EnabledAfterDeployment`)
  * Se você fornecer uma chave de instrumentação, o aplicativo nomeado será reconfigurado para enviar telemetria para o recurso com essa chave. (Funciona se `SdkState != Disabled`)

`Update-ApplicationInsightsVersion`

* Baixa o SDK mais recente do Application Insights para o servidor.

## <a name="a-namenextanext-steps"></a><a name="next"></a>Próximas etapas
* [Crie testes na Web][availability] para ter a certeza de que seu site continua ativo.
* [Pesquise eventos e logs][diagnostic] para ajudar a diagnosticar problemas.
* [Adicione telemetria de cliente Web][usage] para ver exceções de código de página Web e permitir que você insira rastreamento de chamadas.
* [Adicione SDK do Application Insights ao seu código de serviço Web][greenbrown] para que você possa inserir o rastreamento de chamadas de log no código do servidor.

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-asp-net.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md



<!--HONumber=Jan17_HO1-->


