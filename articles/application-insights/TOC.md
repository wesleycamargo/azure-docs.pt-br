# Visão geral
## [O que é o Application Insights?](app-insights-overview.md)
## [Monitoramento do desempenho em um ciclo do devOps](app-insights-detect-triage-diagnose.md)

# Introdução
## Monitorar o Azure
### [Aplicativos Web do Azure](app-insights-azure-web-apps.md)
### [Serviços de Nuvem do Azure](app-insights-cloudservices.md)

## Monitorar Aaplicativos ASP.NET
### [Aplicativos Web](app-insights-asp-net.md)
### [Aplicativos Web já ativos](app-insights-monitor-performance-live-website-now.md)
### [Serviços do Windows](app-insights-windows-services.md)
### [Área de trabalho do Windows](app-insights-windows-desktop.md)

## Monitorar aplicativos do Java
### [Aplicativos Web](app-insights-java-get-started.md)
### [Aplicativos Web - tempo de execução](app-insights-java-live.md)
### [Aplicativos Docker](app-insights-docker.md)

## Monitorar aplicativos do Node.js
### [Node.js](app-insights-nodejs.md)

## Monitorar páginas da Web
### [JavaScript](app-insights-javascript.md)
 
## Monitorar outras plataformas
### [Sites do SharePoint](app-insights-sharepoint.md)
### [Mais plataformas](app-insights-platforms.md)


# Como
## Planejar e projetar
### [Diagnósticos detalhados para aplicativos Web e serviços](app-insights-devops.md)
### [Monitorar o desempenho em aplicativos Web](app-insights-web-monitor-performance.md)
### [Separar recursos do Application Insights](app-insights-separate-resources.md)
### [Como ... no Application Insights?](app-insights-how-do-i.md)


## Configurar
### As tabelas
#### [Criador de perfil](app-insights-profiler.md)
#### [Diagnostics](app-insights-azure-diagnostics.md)

### ASP.NET
#### [Obter mais telemetria](app-insights-asp-net-more.md)
#### [Exceções](app-insights-asp-net-exceptions.md)
#### [Rastreamentos de log](app-insights-asp-net-trace-logs.md)
#### [Contadores de desempenho](app-insights-performance-counters.md)
#### [Dependências](app-insights-asp-net-dependencies.md)
#### [Anotações da versão](app-insights-annotations.md)
#### [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md)
#### [Depurador de instantâneo](app-insights-snapshot-debugger.md)

### J2EE
#### [Rastreamentos de log](app-insights-java-trace-logs.md)
#### [Métricas de UNIX](app-insights-java-collectd.md)
#### [Dependências](app-insights-java-agent.md)
#### [Filtrar telemetria](app-insights-java-filter-telemetry.md)

### Alertas

#### [Disponibilidade](app-insights-monitor-web-app-availability.md)
#### [Alertas de métricas](app-insights-alerts.md)

### [Detecção Inteligente](app-insights-proactive-diagnostics.md)
#### [Anomalias de falha](app-insights-proactive-failure-diagnostics.md)
#### [Anomalias de desempenho](app-insights-proactive-performance-diagnostics.md)

### [Criar um recurso](app-insights-create-new-resource.md)

## Analise

### Portal do Application Insights

#### [Painéis](app-insights-dashboards.md)
#### [Pesquisar](app-insights-diagnostic-search.md)
#### [Métricas](app-insights-metrics-explorer.md)
#### [Fluxo ao vivo](app-insights-live-stream.md)

#### [Mapa do aplicativo](app-insights-app-map.md)
#### [Dados do HockeyApp](app-insights-hockeyapp-bridge-app.md)

### Visual Studio

#### [Informações sobre o F5](app-insights-visual-studio.md)
#### [Tendências](app-insights-visual-studio-trends.md)
#### [CodeLens](app-insights-visual-studio-codelens.md)

### Uso
#### [Visão geral](app-insights-usage-overview.md)
#### [Retenção](app-insights-usage-retention.md)
#### [Usuários, Sessões, Eventos](app-insights-usage-segmentation.md)

### Análise

#### [Visão geral](app-insights-analytics.md)
#### [Tour](app-insights-analytics-tour.md)
#### [Usar](app-insights-analytics-using.md)
#### [Importaçãoação](app-insights-analytics-import.md)
#### [Diagnostics](app-insights-analytics-diagnostics.md)

## Automatizar

### [Configuração do PowerShell](app-insights-powershell.md)
### [Criar recursos](app-insights-powershell-script-create-resource.md)
### [Definir Alertas](app-insights-powershell-alerts.md)
### [Obter diagnóstico do Azure](app-insights-powershell-azure-diagnostics.md)

## Desenvolver

### [API para eventos e métricas personalizados](app-insights-api-custom-events-metrics.md)
### [Filtrar e pré-processar a telemetria](app-insights-api-filtering-sampling.md)
### [Amostragem](app-insights-sampling.md)
### [ASP.NET Core](app-insights-asp-net-core.md)

## Gerenciar
### [Gerenciar cotas e preços](app-insights-pricing.md)
### [Monitoramento de desempenho de aplicativos usando o Application Insights para SCOM](app-insights-scom.md)

## Exportação
### [Exportação contínua](app-insights-export-telemetry.md)
### [Exportar modelo de dados](app-insights-export-data-model.md)
### [Exportar para o Power BI](app-insights-export-power-bi.md)

## Segurança
### [Coleta de dados, retenção e armazenamento](app-insights-data-retention-privacy.md)
### [Recursos, funções e controle de acesso](app-insights-resources-roles-access-control.md)
### [Endereços IP](app-insights-ip-addresses.md)

## Solucionar problemas
### [Nenhum dado para .NET](app-insights-asp-net-troubleshoot-no-data.md)
### [Analytics](app-insights-analytics-troubleshooting.md)
### [Java](app-insights-java-troubleshoot.md)

# Referência
## [Analytics](app-insights-analytics-reference.md)
## [.NET](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights)
## [Java](/java/api/com.microsoft.applicationinsights)
## [JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)
## [REST](https://dev.applicationinsights.io/)
## [Modelo de dados](application-insights-data-model.md)
### [Solicitação](application-insights-data-model-request-telemetry.md)
### [Dependência](application-insights-data-model-dependency-telemetry.md)
### [Exceção](application-insights-data-model-exception-telemetry.md)
### [Rastreamento](application-insights-data-model-trace-telemetry.md)
### [Evento](application-insights-data-model-event-telemetry.md)
### [Métrica](application-insights-data-model-metric-telemetry.md)
## [Correlação de telemetria](application-insights-correlation.md)

# Recursos

## [Idiomas e Plataformas](app-insights-platforms.md)

## [Preços](https://azure.microsoft.com/pricing/details/application-insights/)  

## Notícias

### [Blog](https://azure.microsoft.com/blog/tag/application-insights/)
### [Atualizações de serviço](https://azure.microsoft.com/updates/?product=application-insights) 
### [Notas de versão do SDK](app-insights-release-notes.md)
### [Notas de versão para ferramentas de análise do desenvolvedor](app-insights-release-notes-vsix.md)

## [Perguntas frequentes](app-insights-troubleshoot-faq.md)

## Ajuda
### [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=ApplicationInsights)  
### [Stack Overflow](http://stackoverflow.com/questions/tagged/az-application-insights)
### [Voz do Usuário](https://visualstudio.uservoice.com/forums/357324-application-insights)
### [Suporte](https://azure.microsoft.com/support/)

## [Vídeos](https://azure.microsoft.com/documentation/videos/index/?services=application-insights)


