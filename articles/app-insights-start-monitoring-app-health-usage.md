<properties title="Application Insights" pageTitle="Application Insights - start monitoring your app's health and usage" description="Analyze usage, availability and performance of your on-premises or Microsoft Azure web application with Application Insights." metaKeywords="analytics monitoring application insights" authors="awills"  manager="kamrani" />

<tags ms.service="application-insights" ms.workload="tbd" ms.tgt_pltfrm="ibiza" ms.devlang="na" ms.topic="article" ms.date="2014-09-24" ms.author="awills"></tags>

# Application Insights - Iniciar o monitoramento de integridade e uso do seu aplicativo

*O Application Insights está em visualizar.*

O Application Insights permite que você monitore seu aplicativo ao vivo para:

-   **Disponibilidade** - Testaremos suas URLs a cada poucos minutos em todo o mundo.
-   **Desempenho** - Detecte e diagnostique problemas de desempenho e exceções.
-   **Uso** - Descubra o que os usuários estão fazendo com o seu aplicativo, para que você possa fazer melhor do que eles.

A configuração é muito fácil, e você verá os resultados em minutos. Atualmente, suportamos aplicativos de web ASP.NET (em seus próprios servidores ou no Azure).

Você precisará de uma conta no [Microsoft Azure](http://azure.com) (existe um período de teste gratuito).

Existem duas maneiras de começar com o Application Insights:

-   (Recomendado) [Adicione o Application Insights ao seu projeto no Visual Studio](#add) para monitorar o desempenho e uso do aplicativo.
-   [Instale um agente no seu servidor sem a reimplantação][redfield] - Monitore um site da web ao vivo sem reimplantá-lo ou tocar no seu código-fonte. Isso lhe dá monitoramento de desempenho e exceção. É possível adicionar monitoramento de uso depois.

> [WACOM.NOTE] Existe uma [versão mais antiga do Application Insights](http://msdn.microsoft.com/pt-br/library/dn481095.aspx) no Visual Studio Online, que suporta um intervalo mais amplo dos tipos de aplicativos. Estamos recriando-o a partir do zero como parte do Microsoft Azure, e é a nova versão que você está lendo aqui.

## <a name="add"></a>Adicione o Application Insights ao seu projeto

Você precisa do [Visual Studio 2013 Atualização 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) (ou posterior).

### Se é um novo projeto...

Quando criar um novo projeto no Visual Studio 2013, certifique-se de que o Application Insights está selecionado.

![Criar um projeto ASP.NET](./media/appinsights/appinsights-01-vsnewp1.png)

Se esta é sua primeira vez, você será solicitado a fazer logon ou se inscrever na visualização do Microsoft Azure. (é separada de sua conta do Visual Studio Online)

Use **Configurar definições** se você quer o recurso do Azure tenha um nome diferente do que o seu projeto, ou se você quer o recurso para aparecer no mesmo grupo como um projeto diferente.

*Sem opção de Application Insights? Verifique se está usando o Visual Studio 2013 Atualização 3, se as Ferramentas do Application Insights estão habilitadas nas Extensões e Atualizações e se você está criando um projeto da web, Windows Store ou Windows Phone.*

### ... ou se é um projeto existente

Clique com o botão direito do mouse no projeto no Gerenciador de Soluções, e selecione Adicionar Application Insights.

![Escolher Adicionar Application Insights](./media/appinsights/appinsights-03-addExisting.png)

*Há mais uma etapa, se quiser configurar a análise de uso da web, mas vamos ver alguns dados primeiro...*

### <a name="run"></a>2. Execute seu projeto

Execute seu aplicativo com F5 e experimente - abra páginas diferentes.

No Visual Studio, você verá uma contagem de eventos que foram recebidas.

![](./media/appinsights/appinsights-09eventcount.png)

### <a name="monitor"></a>3. Veja os dados de monitoramento

Abrir Application Insights do seu projeto.

![Clique com o botão direito do mouse no seu projeto e abra o portal do Azure.](./media/appinsights/appinsights-04-openPortal.png)

Procure pelos dados nos mosaicos da **integridade do aplicativo**. Primeiro, você apenas verá um ou dois pontos. Por exemplo:

![Clique por mais dados](./media/appinsights/appinsights-41firstHealth.png)

Clique em qualquer mosaico para ver mais detalhes. Você pode alterar o que você vê nos relatórios. [Saiba mais sobre configuração de relatórios de Integridade do Aplicativo.][perf]

### <a name="webclient"></a>4. Configuração das análises de uso da web

Se você adicionou o Application Insights para um projeto de web *existente*, você não ainda não verá nada nos mosaicos de análise de uso. Há mais uma etapa que você precisa.

No Application Insights, escolha Início Rápido, instrumente seu site da web.

![No seu projeto no Application Insights, clique em Início Rápido, instrumente seu site da web e copie o código](./media/appinsights/appinsights-06webcode.png)

Copie o código JavaScript nas páginas da web que você quer monitorar, logo após a etiqueta \</head\> fechada. No projeto ASP.NET, uma boa forma de monitorar todas as páginas é colocar o código na página mestre, geralmente chamada de `_SiteLayout` ou `Views\Shared\_Layout`. Observe que o código contém a chave do Application Insights do seu aplicativo.

Execute seu aplicativo novamente e você verá os dados na **Análise de Uso**.

![Clique por mais dados](./media/appinsights/appinsights-05-usageTiles.png)

[Clique por gráficos para ver mais detalhes.][perf]

### <a name="deploy"></a>5. Implantar seu aplicativo

Implante seu aplicativo e assiata a acumulação de dados.

Uma vez que você tem um aplicativo ao vivo, [configure os testes de web][availability] para se certificar de mentê-lo ao vivo.

![Exemplo de monitoramento do aplicativo no Application Insights](./media/appinsights/appinsights-00-appblade.png)

### Deseja alterar o nome do seu aplicativo no portal?

Você pode alterar o recurso para o qual o seu projeto envia telemetria.

(O “recurso” é a folha de Ideias de Aplicativo nomeado, onde seus resultados aparecem. Você pode colocar mais do que um recurso em um grupo - por exemplo, se você tem vários projetos que formam parte de um aplicativo de negócio).

No Gerenciador de Soluções, clique com o botão direito do mouse em ApplicationInsights.config e escolha **Atualizar Application Insights**. Isso abre um assistente onde você pode escolher um recurso existente diferente, ou criar um novo.

Em seguida, volte para o portal e exclua o recurso antigo.

## <a name="video"></a>Vídeos

### Introdução

> [AZURE.VIDEO application-insights-introduction]

### Introdução

> [AZURE.VIDEO getting-started-with-application-insights]

## <a name="next"></a>Próximas etapas

[Acompanhar uso do seu aplicativo web][usage]

[Monitore o desempenho dos aplicativos web][perf]

[Capturar e pesquisar por logs de diagnóstico][diagnostic]

[Solucionar problemas][qna]

## Saiba mais

-   [Application Insights - introdução][start]
-   [Monitore um servidor de web ao vivo agora][redfield]
-   [Monitore o desempenho dos aplicativos web][perf]
-   [Pesquise por logs de diagnóstico][diagnostic]
-   [Disponibilidade de acompanhamento com os testes web][availability]
-   [Acompanhar uso][usage]
-   [Perguntas e respostas e solução de problemas][qna]

<!--Link references-->


[start]: ../app-insights-start-monitoring-app-health-usage/
[redfield]: ../app-insights-monitor-performance-live-website-now/
[perf]: ../app-insights-web-monitor-performance/
[diagnostic]: ../app-insights-search-diagnostic-logs/ 
[availability]: ../app-insights-monitor-web-app-availability/
[usage]: ../app-insights-web-track-usage/
[qna]: ../app-insights-troubleshoot-faq/

