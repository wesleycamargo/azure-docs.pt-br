<properties
	pageTitle="Diagnosticar problemas de desempenho em um site em execução | Microsoft Azure"
	description="Monitore o desempenho do site sem implantá-lo novamente. Use um autônomo ou o SDK do Application Insights para obter a telemetria de dependência."
	services="application-insights"
    documentationCenter=".net"
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/10/2015"
	ms.author="awills"/>


# Instalar o Monitor de Status do Aplicativo Insights para monitorar o desempenho do site

*O Application Insights está em modo de visualização.*

O Status do Monitor do Visual Studio Application Insights permite diagnosticar exceções e problemas de desempenho em aplicativos Web executados em qualquer servidor do IIS. Basta instalá-lo no servidor Web IIS e ele instruirá os aplicativos Web do ASP.NET encontrados, enviando dados para o portal do Application Insights para pesquisar e analisar. Você pode instalá-lo em qualquer servidor físico ou virtual ao qual você tem acesso de administrador.

![gráficos de exemplo](./media/app-insights-monitor-performance-live-website-now/10-intro.png)

Você tem três maneiras de aplicar o Application Insights em seus aplicativos Web do IIS:

* **Tempo de compilação:** [Adicionar o SDK do Application Insights][greenbrown] ao código do aplicativo Web. Isso fornece:
 * Um intervalo de telemetria de uso e de diagnóstico padrão.
 * E você pode usar o [API do Application Insights][api] se você quiser escrever sua própria telemetria para rastrear o uso ou diagnosticar problemas.
* **Tempo de execução:** use o Monitor de Status para instrumentar seu aplicativo Web no servidor.
 * Monitorar aplicativos Web que já estão em execução: não é necessário recriá-los ou republicá-los.
 * Um intervalo de telemetria de uso e de diagnóstico padrão.
 * Diagnóstico de dependência &#151; localizar falhas ou baixo desempenho onde seu aplicativo usa outros componentes, como bancos de dados, APIs REST ou outros serviços.
 * Solucionar qualquer problema com telemetria.
* **Ambos:** compile o SDK em seu código de aplicativo Web e execute o Monitor de Status em seu servidor Web. O melhor dos dois mundos:
 * telemetria de uso e de diagnóstico padrão.
 * Diagnóstico de dependência.
 * Escreva telemetria personalizada usando a API.
 * Solucione qualquer problema com o SDK e telemetria.



> [AZURE.TIP]Seu aplicativo é um [aplicativo Web do Serviço de Aplicativo do Azure](../app-service-web/websites-learning-map.md)? [Adicionar o SDK do Application Insights][greenbrown] e [Adicionar a extensão do Application Insights](../insights-perf-analytics.md) do painel de controle do aplicativo no Microsoft Azure.


## Instalar o Application Insights Status Monitor em seu servidor Web do IIS

1. É necessário ter uma assinatura do [Microsoft Azure](http://azure.com).

1. No servidor Web IIS, faça logon com as credenciais de administrador.
2. Baixe e execute o [instalador do Monitor de Status](http://go.microsoft.com/fwlink/?LinkId=506648).

4. No assistente de instalação, entre no Microsoft Azure.

    ![Entre no Azure com suas credenciais de Conta da Microsoft.](./media/app-insights-monitor-performance-live-website-now/appinsights-035-signin.png)

    *Erros de conexão? Consulte [Solucionar problemas](#troubleshooting).*

5. Selecione o aplicativo Web ou o site que deseja monitorar e configure o recurso no qual você deseja ver os resultados no Portal do Application Insights.

    ![Escolha um aplicativo e um recurso.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-configAIC.png)

    Normalmente, você opta por configurar um novo recurso e [grupo de recursos][roles].

    Caso contrário, use um recurso existente se você já configurou [testes da Web][availability] para seu site ou o [monitoramento de clientes Web][client].

6. Reinicie o IIS.

    ![Escolha Reiniciar na parte superior da caixa de diálogo.](./media/app-insights-monitor-performance-live-website-now/appinsights-036-restart.png)

    O serviço Web será interrompido por um período curto.

6. Observe que ApplicationInsights.config foi inserido nos sites que você deseja monitorar.

    ![Localize o arquivo .config junto com os arquivos de código do aplicativo Web.](./media/app-insights-monitor-performance-live-website-now/appinsights-034-aiconfig.png)

   Há também algumas mudanças em web.config.

### Deseja (re)configurar mais tarde?

Após concluir o assistente, você pode reconfigurar o agente quando desejar. É possível também usá-lo se você instalou o agente, mas ocorreu algum problema na configuração inicial.

![Clique no ícone do Application Insights na barra de tarefas](./media/app-insights-monitor-performance-live-website-now/appinsights-033-aicRunning.png)

## Exibir desempenho de telemetria

Entre no [Portal de Visualização do Azure](http://portal.azure.com), procure o Application Insights e abra o recurso que você criou.

![Selecione Navegar, Application Insights e selecione seu aplicativo.](./media/app-insights-monitor-performance-live-website-now/appinsights-08openApp.png)

Abra a folha de Desempenho para ver as dependências e outros dados.

![Desempenho](./media/app-insights-monitor-performance-live-website-now/21-perf.png)

Clique em qualquer gráfico para ver mais detalhes.


![](./media/app-insights-monitor-performance-live-website-now/appinsights-038-dependencies.png)

#### Dependências

Os gráficos rotulados como HTTP, SQL e AZUREBLOB mostram os tempos de resposta e as contagens de chamadas para as dependências, ou seja, os serviços externos que o aplicativo usa.



#### Contadores de desempenho

Clique em qualquer gráfico do contador de desempenho para alterar o que ele faz. Você pode ainda adicionar um novo gráfico.

#### Exceções

![Clique no gráfico de exceções do servidor](./media/app-insights-monitor-performance-live-website-now/appinsights-039-1exceptions.png)

Você pode fazer busca detalhada até exceções específicas (dos últimos sete dias) e obter rastreamentos de pilha e dados de contexto.


## Solucionar problemas

### Erros de conexão

Você precisa abrir algumas portas de saída no firewall do servidor para permitir que o Monitor de Status funcione:

+ Telemetria — elas são necessárias o tempo todo:
 +	`dc.services.visualstudio.com:80`
 +	`f5.services.visualstudio.com:80`
 +	`dc.services.visualstudio.com:443`
 +	`f5.services.visualstudio.com:443`
 +	`dc.services.vsallin.net:443`
+ Configuração — necessária apenas ao fazer alterações:
 -	`management.core.windows.net:443`
 -	`management.azure.com:443`
 -	`login.windows.net:443`
 -	`login.microsoftonline.com:443`
 -	`secure.aadcdn.microsoftonline-p.com:443`
 -	`auth.gfx.ms:443`
 -	`login.live.com:443`
+ Instalação:
 +	`packages.nuget.org:443`
 +	`appinsightsstatusmonitor.blob.core.windows.net:80`

Esta lista pode mudar de tempos em tempos.

### Sem telemetria?

  * Use seu site para gerar alguns dados.
  * Aguarde alguns minutos até que os dados cheguem e clique em **Atualizar**.
  * Abrir a Pesquisa de Diagnóstico (o bloco de Pesquisa) para ver os eventos individuais. Eventos geralmente são visíveis na Pesquisa de Diagnóstico antes de os dados agregados aparecem nos gráficos.
  * Abra o Monitor de Status e selecione seu aplicativo no painel esquerdo. Verifique se há mensagens de diagnóstico para este aplicativo na seção "Configuração de notificações":

  ![](./media/app-insights-monitor-performance-live-website-now/appinsights-status-monitor-diagnostics-message.png)

  * Verifique se o firewall do servidor permite o tráfego de saída nas portas listadas acima.
  * No servidor, se você encontrar uma mensagem sobre "permissões insuficientes", tente fazer o seguinte:
    * No Gerenciador do IIS, selecione o pool de aplicativos, abra as **Configurações Avançadas** e, em **Modelo de Processo**, observe a identidade.
    * No painel de controle de gerenciamento do computador, adicione essa identidade ao grupo Usuários do Monitor de Desempenho.
  * Consulte [Solucionar problemas][qna].

## Requisitos do Sistema

Suporte de sistema operacional para Application Insights Status Monitor no servidor:

- Windows Server 2008
- Windows Server 2008 R2
- Windows Server 2012
- Windows Server 2012 R2

com o SP mais recente e .NET Framework 4.0 e 4.5

No lado do cliente, Windows 7, 8 e 8.1, novamente com o .NET Framework 4.0 e 4.5

Suporte ao IIS: IIS 7, 7,5, 8 e 8.5 (o IIS é obrigatório)

## <a name="next"></a>Próximas etapas

* [Criar testes da Web][availability] para assegurar seu site permaneça ativo.
* [Pesquise eventos e logs][diagnostic] para ajudar a diagnosticar problemas.
* [Adicionar telemetria do cliente Web][usage] para ver as exceções no código da página da Web e permitir que você insira chamadas de rastreamento.
* [Adicionar SDK do Application Insights ao seu código de serviço Web][greenbrown] para que você possa inserir o rastreamento e chamadas de log no código do servidor.

## Vídeo

#### Monitoramento de desempenho

[AZURE.VIDEO app-insights-performance-monitoring]

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[roles]: app-insights-resources-roles-access-control.md
[usage]: app-insights-web-track-usage.md

<!---HONumber=Sept15_HO3-->