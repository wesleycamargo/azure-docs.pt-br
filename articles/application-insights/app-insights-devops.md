<properties
	pageTitle="Diagnósticos detalhados para aplicativos Web e serviços com o Application Insights | Microsoft Azure"
	description="Como o Application Insights se encaixa no ciclo do devOps"
	services="application-insights"
    documentationCenter=""
	authors="alancameronwills"
	manager="douge"/>

<tags
	ms.service="application-insights"
	ms.workload="tbd"
	ms.tgt_pltfrm="ibiza"
	ms.devlang="multiple"
	ms.topic="article" 
	ms.date="06/09/2016"
	ms.author="awills"/>

# Diagnósticos detalhados para aplicativos Web e serviços com o Application Insights

## Por que eu preciso do Application Insights?

O Application Insights monitora seu aplicativo Web em execução. Ele informa sobre problemas de desempenho e falhas e ajuda você a analisar como os clientes usam seu aplicativo. Ele funciona para aplicativos executados em várias plataformas (ASP.NET, J2EE, Node.js,...) e é hospedado na nuvem ou localmente.

![](./media/app-insights-devops/010.png)

É essencial monitorar um aplicativo moderno enquanto ele está em execução. Acima de tudo, você deseja detectar falhas antes que a maioria de seus clientes o faça. Você também deseja descobrir e corrigir problemas de desempenho que, embora não sejam catastróficos, talvez deixem as coisas mais lentas ou causem alguma inconveniência para seus usuários. E quando o sistema estiver funcionando bem, você vai querer saber o que os usuários estão fazendo com ele: eles estão usando o recurso mais recente? Eles estão tendo êxito com ele?

Os aplicativos Web modernos são desenvolvidos em um ciclo de fornecimento contínuo: liberar um novo recurso ou aperfeiçoamento; observar como ele funciona para os usuários; planejar o próximo incremento de desenvolvimento com base nesse conhecimento. Uma parte importante disso é a fase de observação. O Application Insights fornece as ferramentas para monitorar um aplicativo Web quanto ao desempenho e ao uso.

O aspecto mais importante disso é o diagnóstico. Se o aplicativo falhar, negócios estarão sendo perdidos. A função principal de uma estrutura de monitoramento é, portanto, detectar falhas de forma confiável, notificá-lo imediatamente e apresentar a você as informações necessárias para diagnosticar o problema. Isso é exatamente o que o Application Insights faz.

### De onde vêm os bugs?

Falhas em sistemas da web geralmente surgem de problemas de configuração ou interações ruins entre seus vários componentes. A primeira tarefa ao lidar com um incidente de site ativo é, portanto, identificar o local do problema: qual componente ou relação é a causa?

Alguns de nós, aqueles com cabelos grisalhos, podem se lembrar de uma era mais simples na qual um programa de computador era executado em um computador. Os desenvolvedores o testavam exaustivamente antes de enviá-lo e após o envio, raramente viam ou pensavam sobre ele novamente. Os usuários precisavam suportar bugs residuais por muitos anos.

As coisas são muito diferentes agora. Seu aplicativo tem uma infinidade de diferentes dispositivos nos quais ser executado e pode ser difícil garantir o comportamento exatamente igual em cada um deles. Hospedar aplicativos na nuvem significa que os bugs podem ser corrigidos rapidamente, mas também significa uma competição contínua e a expectativa de novos recursos em intervalos frequentes.

Nessas condições, a única maneira de manter um controle firme sobre a contagem de bugs é a realização testes de unidade automatizados. Seria impossível testar tudo novamente de forma manual em cada entrega. O teste de unidade agora é uma parte comum do processo de build. Ferramentas como o Xamarin Test Cloud ajudam oferecendo o teste de interface do usuário automatizado a várias versões de navegador. Esses regimes de teste nos permitem esperar que a taxa de bugs encontrados dentro de um aplicativo possa ser mantida no mínimo.

Aplicativos Web típicos têm muitos componentes ativos. Além do cliente (em um aplicativo de navegador ou dispositivo) e o servidor Web, é provável que haja o processamento de back-end substancial, talvez um pipeline de componentes, ou uma coleção menos rígida de partes de colaboração. E muitos deles não estarão sob seu controle, são serviços externos dos quais você depende.

Em configurações como essas, pode ser difícil e antieconômico realizar o teste para, ou prever, todos os modos de falha que não o próprio sistema ativo.

### Perguntas...

Algumas perguntas que fazemos quando estamos desenvolvendo um sistema Web:

* Meu aplicativo está falhando? 
* O que aconteceu exatamente? Se ele apresentou falha em uma solicitação, quero saber como ele chegou lá. Precisamos de um rastreamento de eventos...
* Meu aplicativo é rápido o suficiente? Quanto tempo ele demora para responder a solicitações típicas?
* O servidor pode lidar com a carga? Ao aumentar a taxa de solicitações, o tempo de resposta se mantém constante?
* Quão responsivas são minhas dependências, as APIs REST, bancos de dados e outros componentes que o aplicativo chama. Em particular, caso o sistema esteja lento, é meu componente ou estou recebendo respostas lentas de outra pessoa?
* Meu aplicativo está ativo ou inativo? Ele pode ser visto em todo o mundo? Avise-me se ele parar...
* Qual é a causa raiz? A falha foi no meu componente ou em uma dependência? É um problema de comunicação?
* Quantos usuários foram afetados? Se tiver mais de um problema para resolver, qual será o mais importante?



## O que é o Application Insights?


![](./media/app-insights-devops/020.png)


1. O Application Insights instrumentaliza seu aplicativo e envia a telemetria sobre ele enquanto o aplicativo está em execução. Você pode compilar o SDK do Application Insights no aplicativo ou você pode aplicar a instrumentação no tempo de execução. O primeiro método é mais flexível, uma vez que você pode adicionar sua própria telemetria aos módulos regulares.
2. A telemetria é enviada para o portal do Application Insights, no qual é armazenada e processada. (Embora o Application Insights esteja hospedado no Microsoft Azure, ele pode monitorar quaisquer aplicativos Web, não apenas os do Azure.)
3. A telemetria é apresentada na forma de gráficos e tabelas de eventos.

Há dois tipos principais de telemetria: instâncias agregadas e brutas.

* Os dados de instância incluem, por exemplo, um relatório de uma solicitação que foi recebida pelo seu aplicativo Web. Você pode localizar e inspecionar os detalhes de uma solicitação usando a ferramenta Pesquisar no portal do Application Insights. A instância pode incluir dados como quanto tempo levou para seu aplicativo responder à solicitação, bem como a URL solicitada, a localização aproximada do cliente e outros dados.
* Os dados agregados incluem o número de eventos por unidade de tempo, para que você possa comparar a taxa de solicitações com os tempos de resposta. Ele também inclui as médias de métricas, como tempos de resposta de solicitação.

As principais categorias de dados são:

* Solicitações para seu aplicativo (normalmente solicitações HTTP), com os dados na URL, o tempo de resposta e o êxito ou falha.
* Dependências, chamadas REST e SQL feitas pelo seu aplicativo, também com o URI, tempos de resposta e êxito
* Exceções, incluindo os rastreamentos de pilha.
* Dados de exibição de página, que vêm de navegadores dos usuários.
* As métricas, como contadores de desempenho, bem como as métricas que você escreve. 
* Eventos personalizados que você pode usar para controlar os eventos de negócios
* Rastreamentos de log usados para depuração.


## Estudo de caso: Real Madrid F.C.

O serviço Web do [Clube de Futebol Real Madrid](http://www.realmadrid.com/) atende a aproximadamente 450 milhões de fãs em todo o mundo. Os fãs o acessam por navegadores da Web e os aplicativos móveis do Clube. Os fãs podem não apenas podem reservar ingressos, mas também acessar informações e videoclipes sobre os resultados, jogadores e próximos jogos. Eles podem pesquisar com filtros como os números de gols marcados. Também há links para mídia social. A experiência do usuário altamente personalizada e é projetada como uma comunicação bidirecional para envolver os fãs.

A solução [é um sistema de serviços e aplicativos no Microsoft Azure](https://www.microsoft.com/pt-BR/enterprise/microsoftcloud/realmadrid.aspx). A escalabilidade é um requisito fundamental: o tráfego é muito variável e pode atingir volumes muito grandes durante e próximo das partidas.

Para o Real Madrid, é vital monitorar o desempenho do sistema. O Visual Studio Application Insights fornece uma exibição abrangente em todo o sistema, garantindo um nível de serviço elevado e confiável.

O Clube também obtém uma compreensão detalhada dos fãs: onde estão (são apenas 3% na Espanha), que interesse têm em players, resultados históricos e jogos futuros e como eles respondem aos resultados das partidas.

A maioria desses dados de telemetria é coletada automaticamente sem nenhum código adicionado, o que simplificou significativamente a solução e reduziu a complexidade operacional. Para o Real Madrid, o Application Insights lida 3,8 bilhões de pontos de telemetria por mês.

O Real Madrid usa o módulo do Power BI para exibir sua telemetria.


![](./media/app-insights-devops/080.png)

## Detecção inteligente

O [Diagnóstico inteligente](app-insights-nrt-proactive-diagnostics.md) um recurso recente. Sem qualquer configuração especial por você, o Application Insights detectará automaticamente e lhe alertará sobre aumentos incomuns nas taxas de falha do aplicativo. Ele é inteligente o suficiente para ignorar um histórico de falhas ocasionais e também aumentos simplesmente proporcionais a um aumento nas solicitações. Por exemplo, se houver uma falha em um dos serviços dos quais você depende ou se o novo build que acabou de ser implantado não estiver funcionando muito bem, você saberá assim que consultar seu email. (E há webhooks para que você possa disparar outros aplicativos.)

Outro aspecto desse recurso executa uma análise aprofundada diária da sua telemetria, procurando padrões incomuns de desempenho que são difíceis de descobrir. Por exemplo, ela pode localizar lentidão no desempenho associada a uma determinada área geográfica ou com a versão de navegador específico.

Em ambos os casos, o alerta não apenas informa os sintomas descobertos, mas também fornece dados que serão necessários para ajudar a diagnosticar o problema, como relatórios de exceção relevantes.

![](./media/app-insights-devops/030.png)

O cliente Samtec disse: "Durante uma migração de recurso recente, encontramos um banco de dados subdimensionado que estava atingindo seus limites de recursos e causando tempos limite. Os alertas de detecção proativos chegaram literalmente conforme realizávamos a triagem do problema, muito próximo a em tempo real, como anunciado. Esse alerta juntamente com os alertas da plataforma Azure nos ajudou a corrigir o problema quase instantaneamente. O tempo de inatividade total foi menor que 10 minutos.zs"

## Live Metrics Stream

Implantar o build mais recente pode ser uma experiência inquietante. Se houver algum problema, você desejará saber sobre ele imediatamente para que possa recuar, se necessário. O Live Metrics Stream fornece métricas-chave com uma latência de aproximadamente 1 segundo.

![](./media/app-insights-devops/040.png)

## Mapa de aplicativo

O mapa de aplicativo detecta automaticamente a topologia do aplicativo, colocando as informações de desempenho sobre ela para permitir que você identifique facilmente os afunilamentos de desempenho e fluxos problemáticos em ambientes distribuídos. Ele permite que você descubra dependências de aplicativos nos Serviços do Azure. Você pode fazer a triagem do problema entendendo se ele é relacionado ao código ou à dependência e, de um único lugar, detalhar a experiência de diagnóstico relacionada. Por exemplo, seu aplicativo pode estar falhando devido à degradação do desempenho na camada SQL. Com o mapa de aplicativo, você poderá observar isso visualmente e detalhar a experiência do SQL Index Advisor ou Insights de Consulta.

![](./media/app-insights-devops/050.png)

## Análise do Application Insights

Com a [Análise](app-insights-analytics.md), você pode escrever consultas arbitrárias em uma poderosa linguagem semelhante a SQL. O diagnóstico em toda a pilha do aplicativo fica fácil, conforme várias perspectivas são conectadas e você pode fazer as perguntas certas para correlacionar o desempenho do serviço às métricas de negócios e à experiência do cliente.

Você pode consultar todos os dados brutos de métrica e instância de telemetria armazenados no portal. A linguagem inclui as operações de filtrar, adicionar, agregação, entre outras. Você pode calcular campos e realizar a análise estatística. Há visualizações de tabela e gráficas.

![](./media/app-insights-devops/025.png)

Por exemplo, é fácil:

* Segmentar os dados de desempenho de solicitação do aplicativo pelas camadas do cliente para entender sua experiência.
* Pesquisar códigos de erro específicos ou nomes de evento personalizados durante investigações do site ativo.
* Analisar o uso do aplicativo de clientes específicos para entender como os recursos são adquiridos e adotados.
* Controlar sessões e tempos de resposta para usuários específicos para permitir que as equipes de operações e suporte forneçam suporte ao cliente instantâneo.
* Determinar os recursos de aplicativo usados com frequência para responder a perguntas de priorização de recurso.

O cliente DNN disse: "O Application Insights nos forneceu a parte ausente da equação para que você possa combinar, classificar, consultar e filtrar os dados conforme necessário. Permitir que nossa equipe usasse sua própria experiência e criatividade para localizar dados com uma linguagem de consulta eficiente nos permitiu localizar análises e resolver problemas que nem mesmo sabíamos que tínhamos. Muitas respostas interessantes surgem das perguntas começando com *'Eu me pergunto se...'.*"

## Integração de ferramentas de desenvolvimento 

### Configurando o Application Insights

O Visual Studio e o Eclipse têm ferramentas para configurar os pacotes SDK corretos para o projeto que você está desenvolvendo. Quando você cria um novo projeto, recebe a opção de adicionar o Application Insights.

Caso você esteja usando uma estrutura de registros de rastreamento, como o Log4N, NLog ou System.Diagnostics.Trace, receberá a opção de enviar os logs para o Application Insights juntamente com a telemetria, para que você possa correlacionar facilmente os rastreamentos com solicitações, chamadas de dependência e exceções.

### Pesquisar telemetria no Visual Studio

Durante o desenvolvimento e depuração de um recurso, você pode exibir e pesquisar a telemetria diretamente no Visual Studio, usando as mesmas funcionalidades de pesquisa que no portal da Web.

E quando o Application Insights registra uma exceção, você pode exibir o ponto de dados no Visual Studio e ir diretamente para o código relevante.

![](./media/app-insights-devops/060.png)

Durante a depuração, você tem a opção de manter a telemetria no computador de desenvolvimento, exibindo-a no Visual Studio, mas sem enviá-la para o portal. Isso evita a mistura da telemetria de produção com a de depuração.

### Anotações do build

Se você usar o Visual Studio Team Services para compilar e implantar seu aplicativo, as anotações de implantação aparecerão em gráficos no portal. Se a versão mais recente tiver qualquer efeito sobre as métricas, isso se tornará bastante óbvio.

![](./media/app-insights-devops/070.png)

### Itens de trabalho 

Quando um alerta é gerado, o Application Insights pode criar automaticamente um item de trabalho no seu sistema de controle de trabalho (somente Visual Studio Team Services no momento).

## Introdução

A introdução ao Application Insights é muito fácil. As principais opções são:

* Instrumentar um aplicativo Web já em execução. Isso lhe fornece toda a telemetria de desempenho interna. Isso está disponível para [Java](app-insights-java-live.md) e [servidores IIS](app-insights-monitor-performance-live-website-now.md) e também para [aplicativos Web do Azure](app-insights-azure.md).
* Instrumentar seu projeto durante o desenvolvimento. Você pode fazer isso para aplicativos [ASP.NET](app-insights-asp-net.md) ou [Java](app-insights-java-get-started.md), bem como [Node.js](app-insights-nodejs.md) e um host de [outros tipos](app-insights-platforms.md). 
* Instrumentar [qualquer página da Web](app-insights-javascript.md) adicionando um trecho de código curto.


## Mas e...?

* [Privacidade e armazenamento](app-insights-data-retention-privacy.md): sua telemetria é mantida em servidores seguros do Azure.
* Desempenho: o impacto é muito baixo. A telemetria é feita em lotes.
* [Suporte](app-insights-get-dev-support.md): você pode aproveitar o programa de suporte do Azure. Há fóruns dinâmicos em que você pode obter respostas dos nossos desenvolvedores e, em último caso, podemos fornecer ajuda individual, especialmente enquanto você ainda estiver na versão beta.
* [Preço](app-insights-pricing.md): você pode começar a usar gratuitamente e isso continua enquanto estiver no volume baixo.

<!---HONumber=AcomDC_0615_2016-->