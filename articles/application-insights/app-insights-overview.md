---
title: Gerenciamento de desempenho de aplicativos com o Azure Application Insights | Microsoft Docs
description: "Acompanhe o uso e o desempenho de seu aplicativo Web ativo.  Detecte, faça triagem e diagnostique problemas."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: douge
ms.assetid: 379721d1-0f82-445a-b416-45b94cb969ec
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 8c5324742e42a1f82bb3031af4380fc5f0241d7f
ms.openlocfilehash: c719a82e6c2ae46080811190f9ca7783414f38f4


---
# <a name="application-performance-management-with-application-insights"></a>Gerenciamento de desempenho de aplicativos com o Application Insights
O Application Insights é um serviço de Gerenciamento de Desempenho de Aplicativos (APM) extensível para desenvolvedores da Web. Use-o para monitorar seu aplicativo Web online. Ele detectará anomalias de desempenho automaticamente. Ele inclui ferramentas de análise avançadas para ajudar você a diagnosticar problemas e entender o que os usuários realmente fazem com seu aplicativo.  Ele foi projetado para ajudar você a aprimorar continuamente o desempenho e a usabilidade do seu aplicativo. Ele funciona com aplicativos em uma ampla variedade de plataformas incluindo .NET e J2EE, hospedados localmente ou na nuvem. Ele também se integra ao seu processo de devOps e oferece pontos de conexão para uma ampla variedade de outras ferramentas.

![Disponha em gráficos as estatísticas de atividade do usuário ou analise detalhadamente eventos específicos.](./media/app-insights-overview/00-sample.png)

[Confira a animação de introdução](https://www.youtube.com/watch?v=fX2NtGrh-Y0).

## <a name="how-does-it-work"></a>Como ele funciona?
Você instala um pacote de instrumentação pequeno em seu aplicativo e configura um recurso Application Insights no portal do Microsoft Azure. A instrumentação monitora seu aplicativo e envia os dados de telemetria ao portal. O portal mostra gráficos estatísticos e fornece ferramentas de pesquisa poderosas para ajudá-lo a diagnosticar quaisquer problemas.

![A instrumentação do Application Insights em seu aplicativo envia telemetria ao recurso do Application Insights no portal do Azure.](./media/app-insights-overview/01-scheme.png)

O Application Insights tem vários [módulos de instrumentação padrão](app-insights-configuration-with-applicationinsights-config.md) que coletam tipos diferentes de telemetria, como chamadas de dependência, exceções e tempos de resposta de solicitação. Você também pode [escrever código para enviar telemetria personalizada](app-insights-api-custom-events-metrics.md) ao portal.

### <a name="whats-the-overhead"></a>Qual é a sobrecarga?
O impacto sobre o desempenho do aplicativo é muito pequeno. As chamadas de acompanhamento não são bloqueadas, além de serem colocadas em lote e enviadas em um thread separado.

## <a name="what-does-it-do"></a>O que ela faz?
O Application Insights é indicado para a equipe de desenvolvimento, para ajudá-lo a compreender como está o desempenho de seu aplicativo e como ele está sendo usado. Ela oferece:

Tipos de telemetria:

* Taxas de solicitação HTTP, tempos de resposta, taxas de êxito.
* TAxas de chamadas de dependência (HTTP & SQL), tempos de resposta, taxas de êxito.
* Rastreamentos de exceção de servidor e cliente.
* Rastreamentos de log de diagnóstico.
* Contagens de exibição, contagens de usuário e de sessão, tempos de carregamento do navegador, exceções.
* Taxas de chamada AJAX, tempos de resposta e taxas de êxito.
* Contadores de desempenho de servidor.
* Telemetria personalizada de cliente e servidor.
* Segmentação pelo local do cliente, versão do navegador, versão do sistema operacional, instância de servidor, dimensões personalizadas e muito mais.
* Testes de disponibilidade

Ferramentas de diagnóstico e análise:

* Alertas inteligentes e manuais sobre taxas de falha e outras métricas e sobre disponibilidade.
* Gráficos ao longo do tempo das métricas agregadas.
* Pesquisa de diagnóstico em instâncias de solicitações, exceções, eventos personalizados, chamadas AJAX e de dependência, rastreamentos de log e visualizações de página.
* Analytics - uma poderosa linguagem de consulta em sua telemetria
* Painéis - componha os gráficos de que você precisa para monitorar todos os componentes do aplicativo.

## <a name="how-do-i-use-it"></a>Como usá-lo?
### <a name="monitor"></a>Monitoramento
Instale o Application Insights em seu aplicativo Web, configure os testes de disponibilidade da Web e:

* Configure um painel para seu ambiente de equipe para ficar atento à carga, à capacidade de resposta e ao desempenho de suas dependências, aos carregamentos de página e a chamadas AJAX.
* Descubra quais são as solicitações mais lentas e com mais falhas.
* Assista ao Live Stream quando implantar uma nova versão, para apontar imediatamente qualquer degradação.

### <a name="diagnose"></a>Diagnosticar
Quando você recebe um alerta ou descobre um problema:

* Correlacione falhas a exceções, a chamadas de dependência e a rastreamentos.
* Examine os logs de rastreamento e despejos de pilha.

### <a name="assess"></a>Avaliar
Meça a eficácia de cada novo recurso que você implanta.

* Planeje medir como os clientes usam a nova experiência do usuário ou as funcionalidades de negócios.
* Escreva telemetria personalizada em seu código para registrar o uso em log.
* Cada ciclo de desenvolvimento de base na evidência rígida da telemetria.

## <a name="get-started"></a>Introdução
Application Insights é um dos muitos serviços hospedados no Microsoft Azure e a telemetria é enviada para ele para análise e apresentação. Portanto, antes de fazer qualquer outra coisa, você precisará de uma assinatura do [Microsoft Azure](http://azure.com). A inscrição é gratuita, e se você optar pelo [plano de preços](https://azure.microsoft.com/pricing/details/application-insights/) básico do Application Insights, não haverá cobrança até que seu aplicativo apresente uso substancial. Se sua organização já tiver uma assinatura, sua conta da Microsoft poderá ser adicionada a ela.

Existem várias maneiras de começar. Comece com o que funciona melhor para você. Você pode adicionar outras posteriormente.

* **Em tempo de execução: instrumente seu aplicativo Web no servidor.**  Evita qualquer atualização no código. Você precisa de acesso de administrador para seu servidor.
  * [**IIS local ou em uma VM**](app-insights-monitor-performance-live-website-now.md)
  * [**Aplicativo Web ou VM do Azure**](app-insights-monitor-performance-live-website-now.md)
  * [**J2EE**](app-insights-java-live.md)
* **Em tempo de desenvolvimento: adicione o Application Insights ao seu código.**  Permite que você escreva telemetria personalizada e instrumente aplicativos de back-end e de área de trabalho.
  * [Visual Studio](app-insights-asp-net.md) 2013 atualização 2 ou posterior.
  * Java no [Eclipse](app-insights-java-eclipse.md) ou em [outras ferramentas](app-insights-java-get-started.md)
  * [Node.js](app-insights-nodejs.md)
  * [Outras plataformas](app-insights-platforms.md)
* **[Instrumentar suas páginas da Web](app-insights-javascript.md)** para exibição de página, AJAX e outras telemetrias do lado do cliente.
* **[Testes de disponibilidade](app-insights-monitor-web-app-availability.md)** - execute o ping de seu site regularmente de nossos servidores.

> [!NOTE]
> Você deve estar querendo apenas prosseguir e experimentar. Mas se quiser ver o que o Application Insights pode fazer, continue lendo...
>
>

## <a name="explore-metrics"></a>Explorar Métricas
Execute seu aplicativo - no modo de depuração no computador de desenvolvimento, ou implantando-o em um servidor - e use-o por um tempo. Em seguida, entre no [portal do Azure](https://portal.azure.com).

Navegue até a folha de visão geral do Application Insights do aplicativo:

![A folha de visão geral](./media/app-insights-overview/01-find.png)

A visão geral permite que você veja imediatamente o desempenho do aplicativo. Você pode comparar carga (em termos de taxa de solicitações) com o tempo que seu aplicativo levou para responder às solicitações. Se houver um aumento desproporcional no tempo de resposta quando a carga aumentar, talvez você queira alocar mais recursos para seu aplicativo. Se ele mostrar mais respostas com falha logo após a implantação de um novo build, talvez você queira reverter o processo.

#### <a name="get-more-detail"></a>Obter mais detalhes
Clique em qualquer gráfico para obter um conjunto de gráficos mais detalhado. Por exemplo, o gráfico de tempo de resposta do servidor leva para gráficos que mostram taxas de solicitação, tempos de resposta e tempos de resposta de dependências (ou seja, os serviços chamados pelo aplicativo).  

![Folha de servidores](./media/app-insights-overview/04.png)

O gráfico de dependências é útil porque ajuda a verificar se os bancos de dados e APIs REST usados pelo aplicativo estão respondendo bem ou estão causando atrasos.

#### <a name="customize-a-chart"></a>Personalizar um gráfico
Tente editar um destes gráficos. Por exemplo, se seu aplicativo Web for executado em uma coleção de instâncias de servidor, você poderá comparar tempos de resposta em diferentes instâncias de servidor:

![editar o gráfico](./media/app-insights-overview/05.png)

1. Passe o mouse sobre o gráfico e clique em Editar.
2. Escolha uma métrica. Várias métricas podem ser exibidas em um gráfico, mas somente em determinadas combinações: talvez seja necessário anular a seleção de uma métrica antes de selecionar a que deseja.
3. Use Agrupar Por para segmentar uma métrica por uma propriedade. Neste exemplo, podemos exibir linhas separadas para diferentes tempos de resposta.

    Observe que você deve selecionar uma propriedade válida para a métrica, caso contrário o gráfico não mostrará nenhum dado.
4. Selecione um tipo de gráfico. Gráficos de barras e de área mostram uma exibição empilhada adequada quando o tipo de agregação é 'Soma'.

[Mais sobre exploração de métricas](app-insights-metrics-explorer.md).

## <a name="search-instance-data"></a>Dados da instância da Pesquisa
Para investigar um problema, é útil inspecionar instâncias de evento específicas.

Clique em um gráfico de métrica para pesquisar dados de instância com filtros e intervalo de tempo relevantes. Por exemplo, clique em contagens de solicitação do servidor para ver relatórios de solicitação individuais.

Ou você pode acessar diretamente os dados da instância da Pesquisa, na página Visão geral:

![Pesquisar instâncias](./media/app-insights-overview/06.png)

Use filtros para se concentrar em tipos específicos de eventos e nos valores de propriedade escolhidos:

![Filtrar pelas propriedades](./media/app-insights-overview/07.png)

Clique em "…" para ver uma lista completa das propriedades ou abra outros eventos associados à mesma solicitação. Neste exemplo, a solicitação com falha tem um relatório de exceções associadas:

![Itens relacionados e detalhes de propriedade](./media/app-insights-overview/08.png)

Abra um evento - neste exemplo, a exceção relacionada – e você poderá criar um item de trabalho (se você usar o Visual Studio Team Services para acompanhar tarefas).

![Criar um item de trabalho](./media/app-insights-overview/09.png)

## <a name="analytics"></a>Análise
[Análise](app-insights-analytics.md) é um recurso de pesquisa e análise ainda mais eficiente, no qual você pode escrever consultas SQL sobre os dados de telemetria para procurar problemas específicos ou então para compilar informações estatísticas.

![Análise](./media/app-insights-overview/10.png)

Abra a janela do tutorial para ver e executar os exemplos de consultas sobre os dados ou leia o [tutorial passo a passo](app-insights-analytics-tour.md)mais longo. O IntelliSense lhe mostrará as consultas que podem ser usadas e há uma [referência da linguagem completa](app-insights-analytics-reference.md).

Consultas normalmente começam com o nome de um fluxo de telemetria como solicitações, exceções ou dependências. Abra a barra de esquema à esquerda para ver uma lista dos fluxos de telemetria disponíveis. A consulta é um pipeline de [operações de consulta](app-insights-analytics-reference.md#queries-and-operators) como `where` - um filtro booliano - ou `project` -que calcula as novas propriedades. `summarize` agrega instâncias, agrupando-as por funções que você define e aplicando funções de agregação pelos dados agrupados.

Os resultados podem ser [renderizados em tabelas ou em vários tipos de gráfico](app-insights-analytics-tour.md#charting-the-results).

## <a name="custom-telemetry"></a>Telemetria personalizada
A telemetria interna que você obtém com a instalação do Application Insights permite que você analise contagens, taxas de êxito e tempos de resposta para solicitações da Web para seu aplicativo e para dependências - ou seja, realiza chamadas de seu aplicativo para APIs REST, SQL. Você também obtém rastreamentos de exceção e (com Status Monitor no servidor) contadores de desempenho do sistema. Se você adicionar o trecho de código do cliente a suas páginas da Web, você obterá tempos de carregamento e contagens de exibição de página, exceções de cliente e taxas de êxito de chamada e de resposta do AJAX.

Analisar toda essa telemetria pode dizer muito sobre desempenho e uso do aplicativo. Mas, às vezes, isso não é suficiente. Convém monitorar o comprimento de uma fila de modo que você possa ajustar o desempenho ou então fazer a contagem de vendas e segmentá-las por localização, ou ainda, no lado do cliente, descobrir com que frequência os usuários clicam em um botão específico para que você possa ajustar a experiência do usuário.

A [API do Application Insights](app-insights-api-custom-events-metrics.md) fornece chamadas `TrackEvent(name)` e `TrackMetric(name, value)` para que você possa enviar suas próprias métricas e eventos personalizados. Há chamadas equivalentes para o lado do cliente.

Por exemplo, se sua página da Web é um aplicativo de jogo de página única, você pode inserir linhas nos locais apropriados para registrar em log quando o usuário ganha ou perde um jogo:

    appInsights.trackEvent("WinGame");
    ...
    appInsights.trackEvent("LoseGame");

Em seguida, podemos traçar o gráfico de contagens de evento personalizadas, segmentando-as pelo nome do evento:

![Análise](./media/app-insights-overview/11.png)

### <a name="log-traces"></a>Rastreamentos de log
Para fins de diagnóstico, há um evento personalizado `TrackTrace(message)` que você pode usar para rastreamentos de execução. Nos recursos de Pesquisa e Análise, você pode pesquisar no conteúdo da mensagem, que pode ser mais longo que um nome de evento.

Se você já usar uma estrutura de registros como Log4Net, NLog, Log4J ou System.Diagnostic.Trace, essas chamadas de rastreamento poderão ser capturadas pelo Application Insights e aparecerão ao lado de outra telemetria. As ferramentas do Visual Studio adicionam automaticamente o módulo SDK apropriado.

## <a name="profiling-your-live-site"></a>Criação de perfil do seu site ativo

Não sabe para onde o tempo vai? O criador de perfil do Application Insights rastreará chamadas HTTP para seu site ativo e mostrará quais são as funções mais demoradas em seu código. O criador de perfil está atualmente no modo preview limitado - você pode [se inscrever para experimentá-lo](https://aka.ms/AIProfilerPreview).

## <a name="dashboards"></a>Painéis
Muitos aplicativos consistem de vários componentes como um serviço Web e um ou mais processadores de back-end. Cada componente será monitorado por um recurso do Application Insights separado. Se o sistema for executado no Azure, você poderá estar usando – e monitorando – serviços como hubs de eventos e também de aprendizado de máquina.

Para monitorar seu sistema inteiro, você pode selecionar os gráficos mais interessantes de diferentes aplicativos e fixá-los em um [painel](app-insights-dashboards.md)do Azure, permitindo que você fique de olho em todo o sistema, continuamente.

![Painéis](./media/app-insights-overview/12.png)

Na verdade, você pode criar vários painéis - por exemplo, um painel de sala da equipe para monitorar a integridade geral do sistema; um painel de design que se concentra no uso de recursos diferentes, um painel separado para componentes em teste e assim por diante.  

Painéis, assim como recursos, podem ser compartilhados entre os membros da equipe.

## <a name="development-in-visual-studio"></a>Desenvolvimento no Visual Studio
Se você estiver usando o Visual Studio para desenvolver seu aplicativo, você encontrará várias ferramentas do Application Insights incorporadas.

### <a name="diagnostic-search"></a>Pesquisa de diagnóstico
A janela Pesquisa mostra eventos que foram registrados. (Se tiver entrado no Azure ao configurar o Application Insights, você poderá pesquisar os mesmos eventos no portal.)

![Clique com o botão direito no projeto e escolha Application Insights, Pesquisar.](./media/app-insights-overview/34.png)

A pesquisa de texto livre funciona em todos os campos dos eventos. Por exemplo, pesquise por parte da URL de uma página; ou pelo valor de uma propriedade, como cidade do cliente; ou por palavras específicas em um log de rastreamento.

Clique em qualquer evento para ver suas propriedades detalhadas.

Você também pode abrir a guia Itens Relacionados para ajudar a diagnosticar as solicitações com falha ou as exceções.

![](./media/app-insights-overview/41.png)

### <a name="diagnostics-hub"></a>Hub de diagnósticos
O Hub de diagnósticos (no Visual Studio 2015 ou posterior) mostra a telemetria do servidor do Application Insights à medida que ela é gerada. Isso funciona mesmo se você optou por instalar apenas o SDK, sem conectar-se a um recurso no Portal do Azure.

![Abra a janela Ferramentas de Diagnóstico e inspecione os eventos do Application Insights.](./media/app-insights-overview/31.png)

### <a name="exceptions"></a>Exceções
Se você [configurar o monitoramento de exceção](app-insights-asp-net-exceptions.md), relatórios de exceção serão mostrados na janela Pesquisar.

Clique em uma exceção para obter um rastreamento de pilha. Se o código do aplicativo for aberto no Visual Studio, você poderá clicar desde o rastreamento de pilha até a linha relevante no código.

![Rastreamento de pilha de exceção](./media/app-insights-overview/17.png)

Além disso, na linha de Código Lens acima de cada método, você verá uma contagem das exceções registradas pelo Application Insights nas últimas 24 horas.

![Rastreamento de pilha de exceção](./media/app-insights-overview/21.png)

### <a name="local-monitoring"></a>Monitoramento local
(Do Visual Studio 2015 Atualização 2) Se você não tiver configurado o SDK para enviar telemetria ao portal do Application Insights (de modo que não haja nenhuma chave de instrumentação em ApplicationInsights.config), então a janela de diagnóstico exibirá a telemetria da sua sessão de depuração mais recente.

Isso será desejável se você já tiver publicado uma versão anterior do seu aplicativo. Você não deseja que a telemetria de suas sessões de depuração se misturem à telemetria no portal do Application Insights do aplicativo publicado.

Também será particularmente útil se você tiver [telemetria personalizada](app-insights-api-custom-events-metrics.md) que queira depurar antes de enviar a telemetria ao portal.

* *Primeiro, configurei totalmente o Application Insights para enviar a telemetria ao portal. Mas agora eu quero ver a telemetria apenas no Visual Studio.*

  * Nas Configurações da janela Pesquisar, há uma opção para pesquisar o diagnóstico local, mesmo se o seu aplicativo enviar telemetria para o portal.
  * Para que a telemetria deixe de ser enviada ao portal, comente a linha `<instrumentationkey>...` de ApplicationInsights.config. Quando estiver pronto para enviar novamente a telemetria ao portal, remova os comentários.

### <a name="trends"></a>Tendências
Tendências é uma ferramenta do Visual Studio para visualizar como o seu aplicativo se comporta ao longo do tempo.

Escolha **Explorar Tendências de Telemetria** no botão de barra de ferramentas do Application Insights ou da janela Pesquisa do Application Insights. Escolha uma das cinco consultas comuns para começar. Você pode analisar conjuntos de dados diferentes com base em tipos de telemetria, intervalos de tempo e outras propriedades.

Para encontrar anomalias em seus dados, escolha uma das opções de anomalias na lista suspensa "Tipo de Exibição". As opções de filtragem na parte inferior da janela facilitam o aprimoramento de subconjuntos específicos de sua telemetria.

![Tendências](./media/app-insights-overview/51.png)

## <a name="releasing-a-new-build"></a>Liberando um novo build
### <a name="live-metrics-stream"></a>Live Metrics Stream
O Live Metrics Stream mostra as métricas do aplicativo no momento exato, com uma latência quase de tempo real de 1 segundo. Isso é muito útil quando você está lançando um novo build e deseja ter certeza de que tudo está funcionando como esperado ou investigar um incidente em tempo real.

![Na folha Visão Geral, clique em Live Stream](./media/app-insights-overview/live-stream.png)

Ao contrário do Metrics Explorer, o Live Metrics Stream exibe um conjunto fixo de métricas. Os dados persistem somente durante o período em que estão no gráfico e, em seguida, são descartados.

### <a name="annotations"></a>Anotações
[Metrics Explorer](app-insights-annotations.md) mostram onde você implantou um novo build. Elas facilitam ver se suas alterações tinham qualquer efeito sobre o desempenho do aplicativo. Elas podem ser criadas automaticamente pelo [sistema de build do Visual Studio Team Services](https://www.visualstudio.com/en-us/get-started/build/build-your-app-vs) e você também pode [criá-las do PowerShell](#create-annotations-from-powershell).

![Exemplo de anotações com correlação visível com tempo de resposta do servidor](./media/app-insights-overview/00.png)

As anotações de versão são um recurso de compilação baseado em nuvem e serviço de versão do Visual Studio Team Services.



## <a name="alerts"></a>Alertas
Se algo der errado com seu aplicativo, você desejará saber imediatamente.

Application Insights oferece três tipos de alerta, que são entregues por email.

### <a name="proactive-diagnostics"></a>Diagnóstico proativo
O [diagnóstico proativo](app-insights-proactive-failure-diagnostics.md) é configurado automaticamente - você não precisa configurá-lo. Desde que seu site tenha tráfego suficiente, você obterá um email se houver um aumento nas solicitações com falha incomum para a hora do dia ou solicitação de taxa. O alerta contém informações de diagnóstico.

Veja a seguir um exemplo do alerta.

![Exemplo de alerta inteligente mostrando a análise de cluster sobre a falha](./media/app-insights-overview/proactive-alert.png)

Um segundo tipo de detecção proativa descobre correlações entre falhas e fatores como localização, sistema operacional do cliente ou tipo de navegador.

### <a name="metric-alerts"></a>Alertas de métricas
Você pode configurar [alertas de métrica](app-insights-alerts.md) para informá-lo quando qualquer métrica ultrapassa um valor limite durante determinado período, como contagens de falhas, memória ou exibições de páginas.

![No Metrics Explorer, escolher Regras de Alerta, Adicionar Alerta](./media/app-insights-overview/appinsights-413setMetricAlert.png)

### <a name="availability"></a>Disponibilidade
[testes de disponibilidade da Web](app-insights-monitor-web-app-availability.md) enviam solicitações para o site de nossos servidores em vários locais em todo o mundo. Eles informam quando seu site está indisponível na Internet ou está respondendo lentamente.

![Exemplo de teste da Web](./media/app-insights-overview/appinsights-10webtestresult.png)

## <a name="export-and-api"></a>Exportar e API
Há várias maneiras pelas quais você pode obter os dados de telemetria fora do portal do Application Insights:

* A [API REST do Acesso a Dados](https://dev.applicationinsights.io/) pode ser usada para pesquisar e extrair dados, incluindo a execução de consultas do Analytics. 
* Exporte os painéis de [consultas do Analytics para o Power BI](app-insights-export-power-bi.md) e exiba os resultados nas visualizações do Power BI, que podem ser atualizadas automaticamente.
* [Exportação Contínua](app-insights-export-telemetry.md) será ideal se você quiser manter grandes partes da sua telemetria por mais tempo que o período de retenção padrão.
* As tabelas, os resultados da pesquisa das [Métricas](app-insights-metrics-explorer.md#export-to-excel) e os resultados do [Analytics](app-insights-analytics.md) podem ser exportados para uma planilha do Excel.

![Exibindo dados no Power BI](./media/app-insights-overview/210.png)

## <a name="data-management"></a>Gerenciamento de dados
Há limites para o uso do Application Insights, que dependem até um certo ponto do esquema de preços que você escolher. Os limites principais são para:

* Taxa de telemetria por minuto
* Contagem de pontos de dados por mês
* Período de retenção para dados

[amostragem](app-insights-sampling.md) é um mecanismo para reduzir o custo e evitar a limitação. Ela descarta uma proporção de sua telemetria, mantendo uma amostra representativa. Itens associados (como exceções e as solicitações que as causaram) são mantidos ou descartados juntos. Para aplicativos ASP.NET, a amostragem é automática e é aplicada ao aplicativo; caso contrário, você pode defini-lo para ser aplicado à ingestão no portal.

## <a name="next-steps"></a>Próximas etapas
Introdução ao tempo de execução com:

* [Servidor IIS](app-insights-monitor-performance-live-website-now.md)
* [Servidor J2EE](app-insights-java-live.md)

Introdução ao tempo de desenvolvimento com:

* [ASP.NET](app-insights-asp-net.md)
* [Java](app-insights-java-get-started.md)
* [Node.js](app-insights-nodejs.md)

## <a name="support-and-feedback"></a>Suporte e comentários
* Perguntas e problemas:
  * [Solução de problemas][qna]
  * [Fórum do MSDN](https://social.msdn.microsoft.com/Forums/vstudio/home?forum=ApplicationInsights)
  * [StackOverflow](http://stackoverflow.com/questions/tagged/ms-application-insights)
  * [Obter suporte do desenvolvedor](app-insights-get-dev-support.md)
* Suas sugestões:
  * [UserVoice](https://visualstudio.uservoice.com/forums/357324)
* Blog:
  * [Blog do Application Insights](https://azure.microsoft.com/blog/tag/application-insights)

## <a name="videos"></a>Vídeos
[![Introdução animada](./media/app-insights-overview/video-front-1.png)](https://www.youtube.com/watch?v=fX2NtGrh-Y0)

> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/218/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/231/player]
>
> [!VIDEO https://channel9.msdn.com/Series/ConnectOn-Demand/222/player]
>
> [Animação de introdução](https://www.youtube.com/watch?v=fX2NtGrh-Y0)
>
>

<!--Link references-->

[android]: https://github.com/Microsoft/ApplicationInsights-Android
[azure]: ../insights-perf-analytics.md
[client]: app-insights-javascript.md
[desktop]: app-insights-windows-desktop.md
[detect]: app-insights-detect-triage-diagnose.md
[greenbrown]: app-insights-asp-net.md
[ios]: https://github.com/Microsoft/ApplicationInsights-iOS
[java]: app-insights-java-get-started.md
[knowUsers]: app-insights-overview-usage.md
[plataformas]: app-insights-platforms.md
[portal]: http://portal.azure.com/
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md



<!--HONumber=Nov16_HO4-->


