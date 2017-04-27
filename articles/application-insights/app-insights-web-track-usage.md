---
title: "Análise de uso para aplicativos Web com o Azure Application Insights | Microsoft Docs"
description: "Visão geral da análise de uso com o Application Insights"
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.assetid: bbdb8e58-7115-48d8-93c0-f69a1beeea6e
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 2715207e548fc57b1896f5736731be3881256cbf
ms.lasthandoff: 04/13/2017


---
# <a name="usage-analysis-for-web-applications-with-application-insights"></a>Análise de uso para aplicativos Web com o Application Insights
Saber como as pessoas usam seu aplicativo permite a você concentrar o trabalho de desenvolvimento nos cenários que são mais importantes para seus usuários, além de adquirir percepção sobre quais objetivos elas têm maior ou menor dificuldade para atingir.

O [Azure Application Insights](app-insights-overview.md) fornece dois níveis de rastreamento de uso:

* **Dados de usuário, sessão e exibição de página** : fornecidos pronto para uso.  
* **Telemetria personalizada** : [escreva código](app-insights-api-custom-events-metrics.md) para rastrear os usuários por meio da experiência de usuário do aplicativo. 


## <a name="get-started"></a>Introdução

A melhor experiência é obtida, instalando o Application Insights tanto no código do servidor de aplicativos como nas suas páginas da Web. Os componentes do servidor e cliente do aplicativo retornam telemetria aoo portal Azure para análise.

1. **Código do servidor:** Instale o módulo apropriado para o [ASP.NET](app-insights-asp-net.md), [Azure](app-insights-azure.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md) ou [outro](app-insights-platforms.md) aplicativo.

    * *Não quer instalar o código do servidor? Apenas [crie um recurso do Azure Application Insights](app-insights-create-new-resource.md).*

2.  **Código de página da Web:** Abra o [Portal do Azure](https://portal.azure.com), abra o recurso do Application Insights para seu aplicativo e abra o **Introdução > Monitorar e diagnosticar aplicativos do lado do cliente**. 

    ![Copie o script no cabeçalho da página da web mestra.](./media/app-insights-web-track-usage/02-monitor-web-page.png)


3. **Obter telemetria:** Execute seu projeto no modo de depuração por alguns minutos e, em seguida, procure resultados na folha Visão Geral em Application Insights.

    Publique seu aplicativo para monitorar o desempenho do aplicativo e descobrir o que seus usuários estão fazendo com o aplicativo.

## <a name="usage-analysis-out-of-the-box"></a>Análise de uso imediata
Abra a folha Uso.

![Usuários, seções e gráficos de exibições de página](./media/app-insights-web-track-usage/14-usage.png)

Passe o mouse na parte em branco acima de um gráfico para ver as contagens em um ponto específico. Caso contrário, os números mostram o valor acumulado ao longo do período, como uma média, um total ou uma contagem de usuários distintos.

O que esses gráficos mostram?

* **Usuários:** a contagem de usuários ativos distintos ao longo do intervalo de tempo do gráfico. Em aplicativos da Web, os usuários são contados usando cookies. Uma pessoa que usa vários navegadores, que limpa os cookies ou usa o recurso de privacidade será contada várias vezes.
* Os **Usuários Autenticados** serão contados se você inseriu uma chamada para [setAuthenticatedUser()](app-insights-api-custom-events-metrics.md#authenticated-users) no seu código.
* **Sessões:** contagem de sessões ativas. Uma sessão da Web é contada após 30 minutos de inatividade. 
* **Exibições de página** conta o número de chamadas para trackPageView(), geralmente chamado uma vez em cada página da Web.

Clique em qualquer um dos gráficos para ver mais detalhes. Observe que você pode alterar o intervalo de tempo dos gráficos.

### <a name="where-do-my-users-live"></a>Onde os meus usuários vivem?
Clique em uma parte em branco do gráfico Usuários para ver outra folha aberta que exibe mais detalhes:

![Na folha Uso, clique no gráfico Usuários](./media/app-insights-web-track-usage/02-sessions.png)

### <a name="what-browsers-or-operating-systems-do-they-use"></a>Quais navegadores ou sistemas operacionais eles usam?

Clique em **Editar** no gráfico de usuários e, dados de Grupo (segmento) segundo uma propriedade como Navegador, Sistema Operacional ou Cidade: 

![Selecionar um gráfico que exibe uma única métrica, ativar Agrupamento e escolher uma propriedade](./media/app-insights-web-track-usage/03-browsers.png)

Para ver o conjunto de contagens completo, alterne o tipo de gráfico para **Grade**. Também é possível optar por exibir métricas adicionais:

![Gráfico de grade multicoluna](./media/app-insights-web-track-usage/multi-column-grid.png)

Para tipos de quadros gráficos é possível agrupar através de uma propriedade ou selecionar várias métricas, mas não ambas. Este gráfico compara duas métricas, usuários e [usuários autenticados](app-insights-api-custom-events-metrics.md#authenticated-users). 

![Selecione um gráfico, procure por métricas e marque-as ou desmarque-as.](./media/app-insights-web-track-usage/031-dual.png)



## <a name="page-views"></a>Visualizações de página
A partir da folha Uso, clique no gráfico de exibições de página para obter uma versão mais detalhada juntamente com um detalhamento de suas páginas mais populares:

![Na folha Visão Geral, clicar no gráfico Visualizações de página](./media/app-insights-web-track-usage/05-games.png)

O exemplo acima é de um site da Web. A partir dos gráficos, imediatamente podemos ver:

* O uso não melhorou na última semana. Talvez devamos pensar em otimização do mecanismo de pesquisa?
* Tênis é a página de jogo mais popular. Vamos manter o foco em melhorias adicionais para esta página.
* Em média, os usuários visitam a página Tênis aproximadamente três vezes por semana. (Há cerca de três vezes mais sessões do que usuários.)
* A maioria dos usuários visita o site durante a semana de trabalho dos EUA e, em horário de trabalho. Talvez devêssemos fornecer um botão "ocultar rápido" na página da Web.
* As [anotações](app-insights-annotations.md) no gráfico mostram quando novas versões do site foram implantadas. Nenhuma das implantações recentes teve um efeito notável no uso.

## <a name="custom-tracking"></a>Acompanhamento personalizado
Vamos supor que, em vez de implementar cada jogo em uma página da Web separada, você decida refatorá-los todos no mesmo aplicativo de página única, com a maioria das funcionalidades codificadas como Javascript na página da Web. Isso permite que o usuário alterne rapidamente entre um jogo e outro, ou até mesmo entre vários jogos em uma única página.

Mas você ainda gostaria que o Application Insights registrasse o número de vezes que cada jogo é aberto, exatamente do mesmo modo que fazia quando elas estavam em páginas da Web separadas. Isso é fácil: basta inserir uma chamada para o módulo de telemetria em seu JavaScript no qual você deseja registrar que uma nova “página” foi aberta:

```JavaScript
    telemetryClient.trackPageView(game.Name);
```
Essa chamada simula a telemetria que registra uma exibição de página.  No entanto, você não deseja sempre misturar as mensagens com exibições de página. Em vez disso, use eventos personalizados. Você pode enviá-los de páginas da Web ou de um servidor Web:


```JavaScript

    telemetryClient.trackEvent("GameEnd");
```

```C#
    var tc = new Microsoft.ApplicationInsights.TelemetryClient();
    tc.TrackEvent("GameEnd");
```

```VB

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")
```

[A telemetria personalizada inserida no seu código do servidor ou Web](app-insights-api-custom-events-metrics.md#trackevent) pode ser usada de muitas maneiras para entender como seu aplicativo está sendo usado.

Para exibir os eventos enviados pelo TrackEvent(): abra o Metrics Explorer, adicione um novo gráfico e edite-o. As métricas são exibidas em Métricas personalizadas. 

![Um gráfico mostrando eventos personalizados.](./media/app-insights-web-track-usage/06-eventsSegment.png)

Se você definir os [valores de propriedade](app-insights-api-custom-events-metrics.md#properties) (também chamados de dimensões) em seus eventos, será possível agrupar e filtrar pelos mesmo.

Crie vários gráficos para correlacionar as alterações em outras métricas e eventos. Por exemplo, nos momentos em que mais jogos são executados, você esperaria ver um aumento também nos jogos abandonados. Mas o aumento nos jogos abandonados é desproporcional, e você gostaria de saber se a carga elevada está causando problemas que os usuários consideram inaceitáveis.

## <a name="drill-into-specific-events"></a>Analisar eventos específicos
Para obter um melhor entendimento de como uma sessão típica decorre, convém concentrar-se na sessão de um usuário específico que contenha um determinado tipo de evento.

A partir de uma grade de eventos, clique nos eventos de interesse e selecione uma ocorrência específica recente:

![Na lista sob o gráfico de resumo, clicar em um evento](./media/app-insights-web-track-usage/08-searchEvents.png)

Vamos examinar todas a telemetria para a sessão na qual o evento NoGame ocorreu.

![Clicar em “toda a telemetria para a sessão”](./media/app-insights-web-track-usage/09-relatedTelemetry.png)

Não havia nenhuma exceção, portanto, o usuário não foi impedido de jogar devido a alguma falha.

É possível filtrar todos os tipos de telemetria exceto visualizações de página para esta sessão:

![](./media/app-insights-web-track-usage/10-filter.png)

E agora podemos ver que este usuário fez logon simplesmente para verificar os resultados mais recentes. Talvez devamos pensar em desenvolver uma história de usuário que torne isso mais fácil de fazer. (E devemos implementar um evento personalizado ao relatório quando essa história específica ocorrer.)

## <a name="filter-search-and-segment-your-data-with-properties"></a>Filtre, pesquise e segmente seus dados com propriedades
Você pode anexar marcas arbitrárias e valores numéricos aos eventos.

*JavaScript em página da Web*

```JavaScript

    appInsights.trackEvent("WinGame",
        // String properties:
        {Game: currentGame.name, Difficulty: currentGame.difficulty},
        // Numeric measurements:
        {Score: currentGame.score, Opponents: currentGame.opponentCount}
    );
```

*C# no servidor*

```C#

    // Set up some properties:
    var properties = new Dictionary <string, string>
        {{"game", currentGame.Name}, {"difficulty", currentGame.Difficulty}};
    var measurements = new Dictionary <string, double>
        {{"Score", currentGame.Score}, {"Opponents", currentGame.OpponentCount}};

    // Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements);
```

*VB no servidor*

```VB

    ' Set up some properties:
    Dim properties = New Dictionary (Of String, String)
    properties.Add("game", currentGame.Name)
    properties.Add("difficulty", currentGame.Difficulty)

    Dim measurements = New Dictionary (Of String, Double)
    measurements.Add("Score", currentGame.Score)
    measurements.Add("Opponents", currentGame.OpponentCount)

    ' Send the event:
    telemetry.TrackEvent("WinGame", properties, measurements)
```

Anexe propriedades a visualizações de página do mesmo modo:

*JavaScript em página da Web*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

Na Pesquisa de Diagnóstico, exiba as propriedades clicando por uma ocorrência individual de um evento.

![Na lista de eventos, abra um evento e clique em '...' para ver mais propriedades](./media/app-insights-web-track-usage/11-details.png)

Use o campo Pesquisa para ver as ocorrências de eventos com um valor da propriedade específico.

![Digitar um valor no campo Pesquisa](./media/app-insights-web-track-usage/12-searchEvents.png)

## <a name="edit-and-create-queries-over-your-telemetry"></a>Editar e criar consultas de análise sobre a telemetria

Clique no ícone do Azure Analytics em qualquer gráfico para abrir uma consulta equivalente que seja possível editar.
Role para baixo para ver se há mais de uma consulta gerada. Posicione o cursor em qualquer consulta e clique em **Ir**. 

Como alternativa, abra o Analytics a partir do ícone na folha Visão Geral e grave suas próprias consultas ou experimente algumas das consultas de amostra na guia Página Inicial do Analytics.


![Janela do Analytics com consulta gerada](./media/app-insights-web-track-usage/open-analytics.png)

[Saiba mais sobre a linguagem de consulta do Azure Analytics](app-insights-analytics.md).

Para análise de uso, é possível que você esteja particularmente interessado nessas tabelas:

* `customEvents` - Resultados de chamadas [TrackEvent()](app-insights-api-custom-events-metrics.md#trackevent).
* `pageViews` - Contagem de páginas abertas em navegadores do cliente ou chamadas para [trackPageView()](app-insights-api-custom-events-metrics.md#page-views).


## <a name="a--b-testing"></a>Testando A | B
Se você não souber qual variante de um recurso terá mais êxito, libere ambas, tornando cada uma acessível para diferentes usuários. Avalie o sucesso de cada uma e então parta para uma versão unificada.

Para essa técnica, você pode anexar marcas distintas a todas as telemetrias enviadas por cada versão do seu aplicativo. Você pode fazer isso definindo propriedades no TelemetryContext ativo. Essas propriedades padrão são adicionadas a todas as mensagens de telemetria que o aplicativo envia - não apenas a suas mensagens personalizadas, mas também à telemetria padrão.

No portal do Application Insights, você então poderá filtrar e agrupar (segmentar) seus dados nas marcas, para comparar as diferentes versões.


```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```


```VB

    Dim context = New TelemetryContext
    context.Properties("Game") = currentGame.Name
    Dim telemetry = New TelemetryClient(context)
    ' Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame")
```

A telemetria individual pode substituir os valores padrão.

Você pode configurar um inicializador universal para que todos os novos clientes de telemetria usem automaticamente seu contexto.

```C#


    // Telemetry initializer class
    public class MyTelemetryInitializer : ITelemetryInitializer
    {
        public void Initialize (ITelemetry telemetry)
        {
            telemetry.Properties["AppVersion"] = "v2.1";
        }
    }
```

No inicializador de aplicativo como Global.asax.cs:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## <a name="build---measure---learn"></a>Compilar - Medir - Aprender
Quando você usa análise, ela se torna parte integrante de seu ciclo de desenvolvimento, não apenas algo a respeito do que você pensa para ajudar a resolver problemas. Aqui estão algumas dicas:

* Determine a métrica principal do seu aplicativo. Você deseja a maior quantidade de usuários possível ou prefere um conjunto pequeno de usuários muito contentes? Você deseja maximizar visitas ou vendas?
* Planeje medir cada história. Quando você faz o esboço de um novo recurso ou história de usuário, ou quando planeja atualizar um existente, pense sempre em como você medirá o sucesso da alteração. Antes da codificação começar, pergunte "Que efeito isso terá em nossas métricas, se funcionar? Devemos acompanhar quaisquer novos eventos?"
  E é claro, quando o recurso estiver ativo, certifique-se de examinar as análises e agir segundo os resultados.
* Relacione outras métricas à métrica principal. Por exemplo, se você adicionar um recurso "Favoritos", você gostaria de saber com que frequência os usuários adicionam Favoritos. Mas talvez é mais interessante saber com que frequência eles retornam aos seus Favoritos. E o mais importante, os clientes que usam Favoritos compram mais produtos?
* Teste de canário. Configure um comutador de recurso que permita a você tornar um novo recurso visível apenas para alguns usuários. Use o Application Insights para ver se o novo recurso está sendo usado do modo que você previu. Faça ajustes, então libere-o para um público maior.
* Fale com seus usuários! A análise não é suficiente por si só, mas sim complementar de se manter um bom relacionamento com o cliente.

## <a name="learn-more"></a>Saiba mais
* [Detecte, realize triagem e diagnostique as falhas e problemas de desempenho em seu aplicativo](app-insights-detect-triage-diagnose.md)
* [Introdução ao Application Insights em várias plataformas.](app-insights-detect-triage-diagnose.md)
* [Usar a API : visão geral](app-insights-api-custom-events-metrics.md)
* [Referência da API de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)



