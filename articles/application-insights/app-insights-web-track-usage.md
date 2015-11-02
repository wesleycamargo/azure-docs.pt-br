<properties 
	pageTitle="Análise de uso para aplicativos Web com o Application Insights" 
	description="Visão geral da análise de uso de aplicativos Web com o Application Insights" 
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
	ms.date="08/24/2015" 
	ms.author="awills"/>
 
# Análise de uso para aplicativos Web com o Application Insights

Saber como as pessoas usam seu aplicativo permite a você concentrar o trabalho de desenvolvimento nos cenários que são mais importantes para elas, além de adquirir percepção sobre quais objetivos elas têm maior ou menor dificuldade para atingir.

O Visual Studio Application Insights fornece dois níveis de rastreamento de uso:

* **Dados de usuário, sessão e exibição de página**: fornecidos pronto para uso.  
* **Telemetria personalizado** - você [escrever código][api] para rastrear os usuários por meio da experiência do usuário do seu aplicativo. 

## Configurando

Dados de uso de um aplicativo Web são fornecidos no navegador do cliente.

#### Configurar um recurso do Application Insights 

Um recurso do Application Insights é um local no Microsoft Azure em que os dados de telemetria do seu aplicativo são analisados e exibidos. Você pode já ter definido um para exibir dados do lado do servidor do aplicativo em [ASP.NET][greenbrown] ou [J2EE][java]. Caso contrário, talvez você queira fazer isso agora.

É melhor exibir os dados de uso do cliente da web no mesmo recurso que os dados do servidor. Dessa forma, você pode facilmente correlacionar diagnósticos e métricas das duas extremidades. Portanto, se você já tiver um recurso, pule para a próxima etapa.

#### Insira o código nas suas páginas da Web

Em seu recurso no [Portal do Azure][portal], abra o Quick Start obtenha o trecho de código para monitorar as páginas da Web.

![](./media/app-insights-web-track-usage/02-monitor-web-page.png)

Coloque o código em uma página mestra como \_Layout.cshtml, (em .NET) ou em um arquivo de inclusão para garantir que ele seja incluído em todas as suas páginas.

O trecho de código inclui a chave de instrumentação (iKey) que identifica o recurso. Para enviar dados para um recurso diferente - por exemplo, durante testes - você precisa apenas substituir a iKey.

Publique suas páginas da Web ou use-as no modo de depuração para gerar alguns dados de telemetria.


## Quão popular é o meu aplicativo Web?

Entre no [Portal do Azure][portal], navegue até seu recurso de aplicativo e clique em Utilização:

![](./media/app-insights-web-track-usage/14-usage.png)

* **Usuários:** a contagem de usuários ativos distintos ao longo do intervalo de tempo do gráfico. 
* **Sessões:** a contagem de sessões ativas
* **Exibições de página** conta o número de chamadas para trackPageView(), geralmente chamado uma vez em cada página da Web.

Clique em qualquer um dos gráficos para ver mais detalhes. Observe que você pode alterar o intervalo de tempo dos gráficos.

### Onde os meus usuários vivem?

Na folha Utilização, clique no gráfico Usuários para ver mais detalhes:

![Na folha Uso, clique no gráfico Usuários](./media/app-insights-web-track-usage/02-sessions.png)
 
### Quais navegadores ou sistemas operacionais eles usam?

Dados de grupo (segmento) segundo uma propriedade, como Navegador, Sistema Operacional ou Cidade:

![Selecionar um gráfico que exibe uma única métrica, ativar Agrupamento e escolher uma propriedade](./media/app-insights-web-track-usage/03-browsers.png)


## Sessões

Sessão é um conceito fundamental no Application Insights, que se esforça para associar cada evento de telemetria - como solicitações, exibições de página, exceções ou eventos personalizados que você codificar por conta própria - a uma sessão de usuário específica.

Informações de contexto avançadas são coletadas sobre cada sessão, como características de dispositivo, localização geográfica, sistema operacional e assim por diante.

Se você instrumentar tanto o cliente quanto o servidor ([ASP.NET][greenbrown] ou [J2EE][java]), os SDKs propagarão a id da sessão entre o cliente e o servidor, de modo que os eventos em ambos os lados possam ser correlacionados.

Ao [diagnosticar problemas][diagnostic], você pode localizar toda a telemetria relacionada à sessão em que ocorreu um problema, incluindo todas as solicitações e todos os eventos, exceções ou rastreamentos registrados em log.

Sessões fornecem uma boa medida de popularidade dos contextos, como dispositivo, sistema operacional ou local. Por exemplo, mostrando a contagem de sessões agrupada por dispositivo, você obter uma contagem mais precisa da frequência com que esse dispositivo é usado com seu aplicativo do que pela contagem de exibições de página. Essa seria um dado útil para fazer a triagem de qualquer problema específico do dispositivo.


#### O que é uma sessão?

Uma sessão representa uma ocorrência única entre o usuário e o aplicativo. Em sua forma mais simples, a sessão começa quando o usuário inicia o aplicativo e termina quando o usuário sai do aplicativo. Para aplicativos Web, por padrão, a sessão é encerrada após 30 minutos de inatividade ou após 24 horas de atividade.

Você pode alterar esses padrões editando o trecho de código:

    <script type="text/javascript">
        var appInsights= ... { ... }({
            instrumentationKey: "...",
            sessionRenewalMs: 3600000,
            sessionExpirationMs: 172800000
        });

* `sessionRenewalMs` : o tempo, em milissegundos, para expirar a sessão devido a inatividade do usuário. Padrão: 30 minutos.
* `sessionExpirationMs` : o comprimento máximo da sessão, em milissegundos. Se o usuário permanecer ativo após esse período, outra sessão será contada. Padrão: 24 horas.

**Duração da sessão** é uma [métrica][metrics] que registra o período de tempo entre o primeiro e o último item de telemetria da sessão. (Não inclui o período de tempo limite).

**Contagem de sessões** em um determinado intervalo é definida como o número de sessões exclusivas com alguma atividade durante esse intervalo. Quando você examina um intervalo de tempo longo, como uma contagem de sessão diária para a semana anterior, isso normalmente é equivale ao número total de sessões.

No entanto, ao explorar intervalos de tempo menores, como granulação por hora, uma sessão longa englobando várias horas será contada para cada hora em que a sessão esteve ativa.

## Usuários e contagens de usuário


Cada sessão de usuário é associada uma id de usuário exclusiva.

Por padrão, o usuário é identificado colocando um cookie. Um usuário que use vários navegadores ou dispositivos será contado mais de uma vez. (Mas veja [usuários autenticados](#authenticated-users)


A métrica de **contagem de usuários** em um certo intervalo é definida como o número de usuários exclusivos com atividade registrada durante esse intervalo. Como resultado, usuários com sessões longas podem ser contados várias vezes quando você define um intervalo de tempo em que o detalhamento é menor que cerca de uma hora.

**Novos usuários** conta os usuários cujas primeiras sessões com o aplicativo ocorreram durante esse intervalo. Se for usado o método padrão de contagem de usuários por cookies, isso também incluirá os usuários que tenham apagado seus cookies ou que estejam usando um novo dispositivo ou navegador para acessar o aplicativo pela primeira vez. ![Na folha de uso, clique no gráfico Usuários para examinar Novos Usuários.](./media/app-insights-web-track-usage/031-dual.png)

### Usuários autenticados

Se seu aplicativo Web permite que os usuários entrem, você pode obter uma contagem mais precisa fornecendo ao Application Insights um identificador de usuário exclusivo. Não precisa ser o nome ou a mesma ID que você usa em seu aplicativo. Assim que seu aplicativo tiver identificado o usuário, use este código:


*JavaScript no cliente*

      appInsights.setAuthenticatedUserContext(userId);

Se seu aplicativo agrupa os usuários em contas, você também pode passar um identificador para a conta.

      appInsights.setAuthenticatedUserContext(userId, accountId);

As IDs de usuário e de conta não devem conter espaços nem caracteres `,;=|`


No [metrics explorer](app-insights-metrics-explorer.md), você pode criar um gráfico de **Usuários Autenticados** e **Contas**.

## Tráfego sintético

Tráfego sintético inclui solicitações de testes de carga e disponibilidade, rastreadores de mecanismo de pesquisa e outros agentes.

O Application Insights tenta determinar e classificar automaticamente o tráfego sintético e marcá-lo adequadamente. Na maioria dos casos, tráfego sintético não invoca o SDK do JavaScript, de modo que essa atividade é excluída da contagem do usuário e de sessão.

No entanto, para [testes da web][availability] do Application Insights, a id de usuário é automaticamente definida com base na localização de POP e a id de sessão é definida com base na id de execução de teste. Em relatórios padrão, o tráfego sintético é filtrado por padrão, o que excluirá esses usuários e sessões. No entanto, quando tráfego sintético for incluído, ele pode causar um pequeno aumento nas contagens gerais de usuários e sessões.
 
## Uso da página

Clique no gráfico de modos de exibição de página para obter uma versão mais ampliada juntamente com uma análise das suas páginas mais populares:


![Na folha Visão Geral, clicar no gráfico Visualizações de página](./media/app-insights-web-track-usage/05-games.png)
 
O exemplo acima é de um site de jogos. Nele, podemos ver instantaneamente:

* O uso não melhorou na última semana. Talvez devamos pensar em otimização do mecanismo de pesquisa?
* O número de pessoas que consultam as páginas de jogos é muito menor do que aquelas que consultam a home page. Por que nossa página inicial não estimula as pessoas a jogar?
* “Palavras Cruzadas” é o jogo mais popular. Lá, devemos dar prioridade a novas ideias e aprimoramentos.

## Acompanhamento personalizado

Vamos supor que, em vez de implementar cada jogo em uma página da Web separada, você decida refatorá-los todos no mesmo aplicativo de página única, com a maioria das funcionalidades codificadas como Javascript na página da Web. Isso permite que o usuário alterne rapidamente entre um jogo e outro, ou até mesmo entre vários jogos em uma única página.

Mas você ainda gostaria que o Application Insights registrasse o número de vezes que cada jogo é aberto, exatamente do mesmo modo que fazia quando elas estavam em páginas da Web separadas. Isso é fácil: basta inserir uma chamada para o módulo de telemetria em seu JavaScript no qual você deseja registrar que uma nova “página” foi aberta:

	appInsights.trackPageView(game.Name);

## Eventos personalizados

Use eventos personalizados para . Você pode enviá-los por meio de aplicativos de dispositivos, páginas da Web ou um servidor Web:

*JavaScript*

    appInsights.trackEvent("GameEnd");

*C#*

    var tc = new Microsoft.ApplicationInsights.TelemetryClient(); 
    tc.TrackEvent("GameEnd");

*VB*

    Dim tc = New Microsoft.ApplicationInsights.TelemetryClient()
    tc.TrackEvent("GameEnd")


Os eventos personalizados mais frequentes são listados na folha visão geral.

![Na folha Visão Geral, role para baixo e clique em Eventos Personalizados.](./media/app-insights-web-track-usage/04-events.png)

Clique no cabeçalho da tabela para ver o número total de eventos. Você pode segmentar o gráfico por diversos atributos, como o nome do evento:

![Selecione um gráfico que mostra apenas uma métrica. Ative o Agrupamento. Escolha uma propriedade. Nem todas as propriedades estão disponíveis.](./media/app-insights-web-track-usage/06-eventsSegment.png)

O recurso particularmente útil encontrado nas linhas do tempo é que você pode correlacionar alterações com outras métricas e eventos. Por exemplo, nos momentos em que mais jogos são executados, você esperaria ver um aumento também nos jogos abandonados. Mas o aumento nos jogos abandonados é desproporcional, e você gostaria de saber se a carga elevada está causando problemas que os usuários consideram inaceitáveis.

## Analisar eventos específicos

Para obter um melhor entendimento de como uma sessão típica decorre, convém concentrar-se na sessão de um usuário específico que contenha um determinado tipo de evento.

Neste exemplo, codificamos um evento personalizado "NoGame", que é chamado se o usuário faz logoff sem chegar realmente a iniciar um jogo. Por que um usuário faria isso? Talvez, se analisarmos detalhadamente algumas ocorrências específicas, obteremos uma pista.

Os eventos personalizados recebidos do aplicativo são listados por nome na folha visão geral:


![Na lâmina Visão Geral, clique em um dos tipos de evento personalizado.](./media/app-insights-web-track-usage/07-clickEvent.png)
 
Clique nos eventos de interesse e selecione uma ocorrência específica recente:


![Na lista sob o gráfico de resumo, clicar em um evento](./media/app-insights-web-track-usage/08-searchEvents.png)
 
Vamos examinar todas a telemetria para a sessão na qual o evento NoGame ocorreu.


![Clicar em “toda a telemetria para a sessão”](./media/app-insights-web-track-usage/09-relatedTelemetry.png)
 
Não havia nenhuma exceção, portanto, o usuário não foi impedido de jogar devido a alguma falha.
 
É possível filtrar todos os tipos de telemetria exceto visualizações de página para esta sessão:


![](./media/app-insights-web-track-usage/10-filter.png)
 
E agora podemos ver que este usuário fez logon simplesmente para verificar os resultados mais recentes. Talvez devamos pensar em desenvolver uma história de usuário que torne isso mais fácil de fazer. (E devemos implementar um evento personalizado ao relatório quando essa história específica ocorrer.)

## Filtre, pesquise e segmente seus dados com propriedades
Você pode anexar marcas arbitrárias e valores numéricos aos eventos.
 

*JavaScript no cliente*

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

*JavaScript no cliente*

```JS

    appInsights.trackPageView("Win", 
        url,
        {Game: currentGame.Name}, 
        {Score: currentGame.Score});
```

Na Pesquisa de Diagnóstico, exiba as propriedades clicando por uma ocorrência individual de um evento.


![Na lista de eventos, abrir um evento e, em seguida, clicar em '...' para ver mais propriedades](./media/app-insights-web-track-usage/11-details.png)
 
Use o campo Pesquisa para ver as ocorrências de eventos com um valor da propriedade específico.


![Digitar um valor no campo Pesquisa](./media/app-insights-web-track-usage/12-searchEvents.png)


## Testando A | B

Se você não souber qual variante de um recurso terá mais êxito, libere ambas, tornando cada uma acessível para diferentes usuários. Avalie o sucesso de cada uma e então parta para uma versão unificada.

Para essa técnica, você pode anexar marcas distintas a todas as telemetrias enviadas por cada versão do seu aplicativo. Você pode fazer isso definindo propriedades no TelemetryContext ativo. Essas propriedades padrão são adicionadas a todas as mensagens de telemetria que o aplicativo envia - não apenas a suas mensagens personalizadas, mas também à telemetria padrão.

No portal do Application Insights, você então poderá filtrar e agrupar (segmentar) seus dados nas marcas, para comparar as diferentes versões.

*C# no servidor*

```C#

    using Microsoft.ApplicationInsights.DataContracts;

    var context = new TelemetryContext();
    context.Properties["Game"] = currentGame.Name;
    var telemetry = new TelemetryClient(context);
    // Now all telemetry will automatically be sent with the context property:
    telemetry.TrackEvent("WinGame");
```

*VB no servidor*

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
    public class MyTelemetryInitializer : IContextInitializer
    {
        public void Initialize (TelemetryContext context)
        {
            context.Properties["AppVersion"] = "v2.1";
        }
    }
```

No inicializador de aplicativo como Global.asax.cs:

```C#

    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.ContextInitializers
        .Add(new MyTelemetryInitializer());
    }
```


## Compilar - Medir - Aprender

Quando você usa análise, ela se torna parte integrante de seu ciclo de desenvolvimento, não apenas algo a respeito do que você pensa para ajudar a resolver problemas. Aqui estão algumas dicas:

* Determine a métrica principal do seu aplicativo. Você deseja a maior quantidade de usuários possível ou prefere um conjunto pequeno de usuários muito contentes? Você deseja maximizar visitas ou vendas?
* Planeje medir cada história. Quando você faz o esboço de um novo recurso ou história de usuário, ou quando planeja atualizar um existente, pense sempre em como você medirá o sucesso da alteração. Antes da codificação começar, pergunte "Que efeito isso terá em nossas métricas, se funcionar? Devemos acompanhar quaisquer novos eventos?" E é claro, quando o recurso estiver ativo, certifique-se de examinar as análises e agir segundo os resultados. 
* Relacione outras métricas à métrica principal. Por exemplo, se você adicionar um recurso "favoritos", você desejará saber com que frequência os usuários adicionam favoritos. Mas talvez é mais interessante saber com que frequência eles retornam aos seus Favoritos. E o mais importante, os clientes que usam Favoritos compram mais produtos?
* Teste de canário. Configure um comutador de recurso que permita a você tornar um novo recurso visível apenas para alguns usuários. Use o Application Insights para ver se o novo recurso está sendo usado do modo que você previu. Faça ajustes, então libere-o para um público maior.
* Fale com seus usuários! A análise não é suficiente por si só, mas sim complementar de se manter um bom relacionamento com o cliente.


## Referências

* [Usando a API: visão geral][api]
* [Referência da API de JavaScript](https://github.com/Microsoft/ApplicationInsights-JS/blob/master/API-reference.md)

## Vídeo

> [AZURE.VIDEO usage-monitoring-application-insights]


<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[availability]: app-insights-monitor-web-app-availability.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[portal]: http://portal.azure.com/
[windows]: app-insights-windows-get-started.md

 

<!---HONumber=Oct15_HO4-->