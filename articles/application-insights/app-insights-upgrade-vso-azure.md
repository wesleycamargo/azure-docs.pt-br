<properties 
	pageTitle="Atualizar da antiga versão do Visual Studio Team Services do Application Insights" 
	description="Atualizar projetos existentes"
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="awills"/>
 
# Atualizar da antiga versão do Visual Studio Team Services do Application Insights

Este documento só será de seu interesse caso você tenha um projeto que ainda esteja usando a versão mais antiga do Application Insights, que fazia parte do Visual Studio Team Services. Essa versão será desativada eventualmente, então nós o encorajamos a atualizar para a nova versão, que é um serviço contido no Microsoft Azure.

## Qual versão eu tenho?

Se você adicionou o Application Insights ao seu projeto usando o Visual Studio 2013 atualização 3 ou posterior, provavelmente ele usa a nova versão do Azure.

Abra o ApplicationInsights.config. Se ele tem os nós `ActiveProfile` e `Profiles`, ele é da versão antiga e você deve atualizá-lo.

Ou então, examine seu projeto no Gerenciador de Soluções do Visual Studio e, em referências, selecione Microsoft.ApplicationInsights. Na janela Propriedades, localize a Versão. Se ela for menor que 0.12, você deve atualizá-la.

## Se você tiver um projeto do Visual Studio...

1. Abra o projeto no Visual Studio 2013 Atualização 3 ou posterior.
2. Exclua o ApplicationInsights.config 
3. Remova do projeto os pacotes NuGet do Application Insights. Para fazer isso, clique com o botão direito do mouse no projeto no Gerenciador de Soluções e escolha Gerenciar Pacotes NuGet.

    ![](./media/app-insights-upgrade-vso-azure/nuget.png)
4. Exclua a pasta AppInsightsAgent e os arquivos que ela contém.

    ![](./media/app-insights-upgrade-vso-azure/folder.png)

5. Remova todos os nomes de configurações de Microsoft.AppInsights e os valores Servicedefinition e ServiceConfiguration.csfg

    ![](./media/app-insights-upgrade-vso-azure/csdef.png)
4. SDK: clique com o botão direito do mouse no projeto e [selecione Adicionar Application Insights][greenbrown]. Isso adiciona o SDK ao seu projeto e também cria um novo recurso do Application Insights no Azure.
5. Registro: se seu código inclui chamadas para a antiga API, como um LogEvent(), você as descobrirá quando tentar compilar a solução. Atualize-os para [usar a nova API][track].
6. Páginas da Web: se o seu projeto inclui páginas da Web, substitua os scripts nas seções <head>. Normalmente há apenas uma cópia em uma página mestra, como Views\\Shared\_Layout.cshtml. [Obtenha o novo script na folha Início Rápido em seu recurso Application Insights, no Azure][usage]. Se suas páginas da Web incluem chamadas de telemetria no corpo como logEvent ou logPage, [atualize-as para usar a nova API][api].
7. Monitor do servidor: se o seu aplicativo é um serviço em execução no IIS, desinstale o Microsoft Monitoring Agent do servidor e então [instale o Monitor de Status do Application Insights][redfield].
8. Testes da Web: se você estava usando testes de disponibilidade da Web, [recrie-os no novo portal][availability], com seus alertas.
9. Alertas: defina [alertas em métricas][alerts] no portal do Azure.


## Se você tiver um serviço Web Java...

1. No computador do servidor, desabilite o agente antigo removendo as referências para o agente APM do arquivo de inicialização de serviço Web. Em um servidor TomCat, edite Catalina.bat. Em um servidor JBoss, edite Run.bat. 
2. Reinicie o serviço Web.
3. No portal do Microsoft Azure, [adicione um novo recurso do Application Insights][java]. No computador de desenvolvimento, adicione [o Java SDK][java] ao seu projeto Web.
4. Substitua os scripts nas seções <head> das páginas da Web. (Pode haver apenas uma cópia incluída no lado do servidor.) [Obtenha o novo script na folha Início Rápido em seu novo recurso Application Insights, no Azure][usage]. Se suas páginas da Web incluem chamadas de telemetria no corpo como logEvent ou logPage, [atualize-as para usar a nova API][track].
8. Testes da Web: se você estava usando testes de disponibilidade da Web, [recrie-os no novo portal][availability], com seus alertas.
9. Alertas: defina [alertas em métricas][alerts] no portal do Azure.



<!--Link references-->

[alerts]: app-insights-alerts.md
[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=Nov15_HO4-->