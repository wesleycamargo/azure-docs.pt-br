<properties title="Monitor your web app's availability and responsiveness" pageTitle="Web tests in Application Insights" description="Make sure your web application is available and responsive. Get alerts if it becomes unavailable or responds slowly." metaKeywords="analytics web test" authors="awills"  />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="awills"></tags>

# Monitorar a disponibilidade e a capacidade de resposta do seu aplicativo da Web

Após ter implantado seu aplicativo Web, você pode configurar testes na Web para monitorar sua disponibilidade e capacidade de resposta. O Application Insights envia solicitações em intervalos regulares por meio de pontos no mundo inteiro, e pode alertá-lo se o seu aplicativo responder lentamente ou simplesmente não responder.

Você pode configurar testes na Web para qualquer ponto de extremidade HTTP que for acessível da Internet pública.

1.  [Criar um novo aplicativo?][Criar um novo aplicativo?]

-   [Configurar um teste na Web][Configurar um teste na Web]
-   [Ver relatórios de monitoramento][Ver relatórios de monitoramento]
-   [Editar ou desabilitar um teste][Editar ou desabilitar um teste]
-   [Próximas etapas][Próximas etapas]

## <a name="create"></a>1. Criar um novo aplicativo?

Ignore esta etapa se já estiver [monitorando o uso e a integridade][monitorando o uso e a integridade] deste aplicativo da Web e quiser ver os dados de disponibilidade no mesmo local.

Mas se quiser manter os resultados separados, faça logon no portal de visualização do Microsoft Azure e crie um novo aplicativo no Application Insights.

![Novo \> Application Insights][Novo \> Application Insights]

## <a name="setup"></a>2. Configurar um teste na Web

Na lâmina inicial do aplicativo no Application Insights, clique no bloco de testes da Web vazios.

![Clique no teste de disponibilidade vazio][Clique no teste de disponibilidade vazio]

> *Já tem testes na Web? Continue clicando no bloco de testes da Web e selecione Adicionar teste da Web.*

Forneça um nome para o teste e o URL que deseja testar. O URL precisa estar visível para a Internet pública.

![Preencha pelo menos o URL do seu site][Preencha pelo menos o URL do seu site]

-   Os locais de teste são os locais por meio dos quais nossos servidores enviam solicitações da Web para seu URL. Escolha dois ou três para que você possa diferenciar problemas no site de problemas da rede. Não é possível selecionar mais de três.

-   Critérios de sucesso - Especifique os códigos de retorno HTTP aceitáveis - 200 é o comum.

    Você também pode especificar uma cadeia de caracteres que deve ser encontrada em todas as respostas corretas. É necessário que seja uma cadeia de caracteres simples, sem curingas. Lembre-se de que se o seu conteúdo for alterado, talvez seja necessário atualizá-lo.

-   Alertas - Por padrão, alertas são enviados para você se houver falhas repetidas por 15 minutos. Você pode alterar a configuração para torná-la mais sensível, e pode alterar também o endereço de email para notificação.

### Testar mais URLs

Você pode adicionar mais testes para quantas URLs quiser. Por exemplo, além de testar a página inicial, você poderia se certificar de que o banco de dados está sendo executado testando o URL para uma pesquisa.

![Na lâmina de testes na Web, selecione Adicionar][Na lâmina de testes na Web, selecione Adicionar]

## <a name="monitor"></a>3. Ver relatórios de monitoramento

Após 1 ou 2 minutos, feche e abra a lâmina do aplicativo novamente. (Nesta versão, ela não é atualizada automaticamente.)

![Resumo dos resultados na lâmina inicial][Resumo dos resultados na lâmina inicial]

Trata-se de um resumo. Se você tiver definido diversos testes na Web para este aplicativo, todos eles são combinados aqui.

Continue clicando na lâmina de Testes na Web para ver uma lista de seus testes.

Abra um teste na Web específico.

![Clique em um teste da Web específico][Clique em um teste da Web específico]

Na lâmina do teste na Web específico, role para baixo até **Testes com falha** e selecione um resultado.

![Clique em um teste da Web específico][1]

O resultado mostra o motivo da falha.

![Resultado do teste da Web][Resultado do teste da Web]

Para obter mais detalhes, baixe o arquivo de resultado e inspecione-o no Visual Studio.

## <a name="edit"></a>4. Editar ou desabilitar um teste

Abra um teste individual para editá-lo ou desabilitá-lo.

![Editar ou desabilitar um teste na Web][Editar ou desabilitar um teste na Web]

Talvez você deseje desabilitar testes na Web quando estiver fazendo a manutenção de seu serviço.

## <a name="next"></a>Próximas etapas

[Pesquisar logs de diagnóstico][Pesquisar logs de diagnóstico]

[Solucionar problemas][Solucionar problemas]

## <a name="next"></a>Saiba mais

-   [Application Insights][Application Insights]
-   [Adicionar o Application Insights ao seu projeto][monitorando o uso e a integridade]
-   [Monitorar um servidor Web online agora][Monitorar um servidor Web online agora]
-   [Explorar métricas no Application Insights][Explorar métricas no Application Insights]
-   [Pesquisa de log de diagnóstico][Pesquisar logs de diagnóstico]
-   [Acompanhamento de disponibilidade com testes na Web][Acompanhamento de disponibilidade com testes na Web]
-   [Acompanhamento de uso com eventos e métricas][Acompanhamento de uso com eventos e métricas]
-   [Perguntas e respostas e solução de problemas][Solucionar problemas]

<!--Link references-->

  [Criar um novo aplicativo?]: #create
  [Configurar um teste na Web]: #setup
  [Ver relatórios de monitoramento]: #monitor
  [Editar ou desabilitar um teste]: #edit
  [Próximas etapas]: #next
  [monitorando o uso e a integridade]: ../app-insights-monitor-application-health-usage/
  [Novo \> Application Insights]: ./media/appinsights/appinsights-11newApp.png
  [Clique no teste de disponibilidade vazio]: ./media/appinsights/appinsights-12avail.png
  [Preencha pelo menos o URL do seu site]: ./media/appinsights/appinsights-13availChoices.png
  [Na lâmina de testes na Web, selecione Adicionar]: ./media/appinsights/appinsights-16anotherWebtest.png
  [Resumo dos resultados na lâmina inicial]: ./media/appinsights/appinsights-14availSummary.png
  [Clique em um teste da Web específico]: ./media/appinsights/appinsights-15webTestList.png
  [1]: ./media/appinsights/appinsights-17-availViewDetails.png
  [Resultado do teste da Web]: ./media/appinsights/appinsights-18-availDetails.png
  [Editar ou desabilitar um teste na Web]: ./media/appinsights/appinsights-19-availEdit.png
  [Pesquisar logs de diagnóstico]: ../app-insights-search-diagnostic-logs/
  [Solucionar problemas]: ../app-insights-troubleshoot-faq/
  [Application Insights]: ../app-insights-get-started/
  [Monitorar um servidor Web online agora]: ../app-insights-monitor-performance-live-website-now/
  [Explorar métricas no Application Insights]: ../app-insights-explore-metrics/
  [Acompanhamento de disponibilidade com testes na Web]: ../app-insights-monitor-web-app-availability/
  [Acompanhamento de uso com eventos e métricas]: ../app-insights-track-usage-custom-events-metrics/
