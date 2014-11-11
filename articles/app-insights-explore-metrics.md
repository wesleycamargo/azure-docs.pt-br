<properties title="Explore your metrics" pageTitle="Explore your metrics" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills" />

# Explorar suas métricas

Caso ainda não tenha [configurado seu projeto da Web para o Application Insights][configurado seu projeto da Web para o Application Insights], faça-o agora.

Neste documento estão os relatórios que você pode esperar ver na lâmina do seu aplicativo no Application Insights. Para acessar a página a partir do Visual Studio, clique com o botão direito do mouse no projeto da Web e selecione Abrir Application Insights. Para acessá-la a partir do painel inicial da versão de visualização do Microsoft Azure, selecione Navegar, Application Insights e, depois, selecione seu projeto.

-   [Integridade do aplicativo][Integridade do aplicativo]
-   [Análise de uso][Análise de uso]
-   [Próximas etapas][Próximas etapas]

## <a name="health"></a>Integridade do aplicativo

A integridade do aplicativo é monitorada por meio da instrumentação de seu código.

![][0]

### Tempo médio de resposta

Mede o tempo decorrido entre a entrada de uma solicitação da Web em seu aplicativo e o retorno da resposta.

Os pontos mostram uma média móvel. Se houver muitas solicitações, pode haver algumas que desviam da média sem que haja um pico ou uma queda óbvia no gráfico.

Busque picos incomuns. De modo geral, espere que o tempo de reposta aumente quando as solicitações aumentarem. Se o aumento for desproporcional, seu aplicativo pode estar atingindo o limite de algum recurso, como a CPU ou a capacidade de um serviço utilizado.

### Solicitações

O número de solicitações recebidas em um período específico. Compare com o resultado de outros gráficos para ver como seu aplicativo se comporta conforme a carga varia.

### Testes da Web

![][1]

Os [testes da Web][testes da Web] mostram os resultados de solicitações da Web enviadas regularmente ao seu servidor por meio de servidores do Application Insights no mundo inteiro.

Verifique se os resultados variam de acordo com o número de solicitações.

[Como configurar testes da Web][testes da Web].

### Solicitações mais lentas

![][2]

Mostra quais solicitações podem precisar de um ajuste de desempenho.

### Pesquisa de diagnóstico

![][3]

Se você tiver [configurado logs de diagnóstico][configurado logs de diagnóstico], continue clicando para ver os eventos mais recentes.

### Solicitações com falha

![][4]

O número de solicitações que acionaram exceções não capturadas.

## <a name="usage"></a>Análise de uso

![][5]

Os dados de uso vêm parcialmente do servidor e parcialmente dos [scripts nas páginas da Web][configurado seu projeto da Web para o Application Insights].

### Sessões por navegador

Uma *sessão* é um período iniciado quando um usuário abre qualquer página do seu site e que termina após o usuário não ter enviado nenhuma solicitação da Web por um período de tempo limite de 30 minutos.

Continue clicando para aproximar o gráfico.

### Principais visualizações de páginas

Mostra as contagens totais das últimas 24 horas.

Continue clicando para ver os gráficos de visualizações de página da última semana.

### Reorganizar os blocos

![Escolher configurações, personalizar][Escolher configurações, personalizar]

## <a name="next"></a>Próximas etapas

[Configurar testes da Web][testes da Web]

[Coletar e pesquisar logs de diagnóstico][configurado logs de diagnóstico]

[Solucionar problemas][Solucionar problemas]

## Saiba mais

-   [Application Insights][Application Insights]
-   [Adicionar o Application Insights ao seu projeto][configurado seu projeto da Web para o Application Insights]
-   [Monitorar um servidor Web on-line agora][Monitorar um servidor Web on-line agora]
-   [Explorar métricas no Application Insights][Explorar métricas no Application Insights]
-   [Pesquisa de log de diagnóstico][configurado logs de diagnóstico]
-   [Controle de disponibilidade com testes da Web][testes da Web]
-   [Controle de uso com eventos e métricas][Controle de uso com eventos e métricas]
-   [Perguntas e respostas e solução de problemas][Solucionar problemas]

<!--Link references-->

  [configurado seu projeto da Web para o Application Insights]: ../app-insights-monitor-application-health-usage/
  [Integridade do aplicativo]: #health
  [Análise de uso]: #usage
  [Próximas etapas]: #next
  [0]: ./media/appinsights/appinsights-42reqs.png
  [1]: ./media/appinsights/appinsights-43webtests.png
  [testes da Web]: ../app-insights-monitor-web-app-availability/
  [2]: ./media/appinsights/appinsights-44slowest.png
  [3]: ./media/appinsights/appinsights-45diagnostic.png
  [configurado logs de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [4]: ./media/appinsights/appinsights-46failed.png
  [5]: ./media/appinsights/appinsights-47usage.png
  [Escolher configurações, personalizar]: ./media/appinsights/appinsights-21-customizeblade.png
  [Solucionar problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorar um servidor Web on-line agora]: ../app-insights-monitor-performance-live-website-now/
  [Explorar métricas no Application Insights]: ../app-insights-explore-metrics/
  [Controle de uso com eventos e métricas]: ../app-insights-track-usage-custom-events-metrics/
