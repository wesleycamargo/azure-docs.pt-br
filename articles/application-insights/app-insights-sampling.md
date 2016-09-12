<properties 
	pageTitle="Amostragem de telemetria no Application Insights" 
	description="Como manter o volume de telemetria sob controle." 
	services="application-insights" 
    documentationCenter="windows"
	authors="vgorbenko" 
	manager="douge"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/30/2016" 
	ms.author="awills"/>

#  Amostragem no Application Insights

*O Application Insights está em modo de visualização.*


A amostragem é um recurso no Application Insights que permite coletar e armazenar um conjunto reduzido de telemetria, ao mesmo tempo que mantém uma análise estatisticamente correta dos dados do aplicativo. Ela reduz o tráfego e ajuda a evitar a [limitação](app-insights-pricing.md#data-rate). Os dados são filtrados de tal forma que itens relacionados sejam permitidos, de modo que você possa navegar entre os itens quando estiver realizando investigações de diagnóstico. Quando as contagens de métrica são apresentadas a você no portal, elas são normalizadas novamente para levar em conta a amostragem, a fim de minimizar qualquer efeito sobre as estatísticas.

A amostragem está atualmente na versão Beta e pode ser alterada no futuro.

## Em resumo:

* A amostragem retém 1 em registros *n* e descarta o resto. Por exemplo, ela pode reter 1 em 5 eventos, com uma taxa de amostragem de 20%.
* A amostragem acontece automaticamente se o seu aplicativo enviar muita telemetria. A amostragem automática só é ativada em grandes volumes e somente em aplicativos de servidor de web do ASP.NET.
* Você também pode definir a amostragem manualmente no portal na página de preços (para reduzir o volume de telemetria retido e manter a sua cota mensal); ou no SDK do ASP.NET no arquivo .config, para também reduzir o tráfego de rede.
* A taxa de amostragem atual é uma propriedade de cada registro. Na janela Pesquisar, abra um evento, como uma solicitação. Expanda as reticências "..." para acessar as propriedades completas e localizar a propriedade "* count" - chamada, por exemplo,"contagem de solicitações"ou"contagem de eventos", dependendo do tipo de telemetria. Se ele for > 1, amostragem está ocorrendo. Uma contagem de 3 significaria que a amostragem é 33%: cada registro mantido representa três registros originalmente gerados.
* Se você registrar eventos personalizados e desejar certificar-se de que um conjunto de eventos é retido ou descartado em conjunto, certifique-se de que eles têm o mesmo valor de OperationID.
* Se você escrever consultas de Análise, deverá [levar em conta a amostragem](app-insights-analytics-tour.md#counting-sampled-data). Em particular, em vez de simplesmente contar registros, você deve usar `summarize sum(itemCount)`.


## Tipos de amostragem

Há três módulos de amostragem alternativos:

* A **amostragem adaptável** ajusta automaticamente o volume da telemetria enviada do SDK para seu aplicativo ASP.NET. Ela é padrão desde o SDK v 2.0.0-beta3.
* A **amostragem de taxa fixa** reduz o volume de telemetria enviado do seu servidor ASP.NET e dos navegadores dos seus usuários. Você define a taxa.
* A **amostragem de ingestão** reduz o volume de telemetria mantido pelo serviço do Application Insights a uma taxa definida por você. Ela não reduz o tráfego de telemetria, mas ajuda você a se manter em sua cota mensal.

## Amostragem de ingestão

Essa forma de amostragem opera no ponto em que a telemetria de seu servidor Web, seus navegadores e seus dispositivos atinge o ponto de extremidade do serviço Application Insights. Embora ela não reduza o tráfego de telemetria enviado do seu aplicativo, reduz a quantidade processada e mantida (e cobrada) pelo Application Insights.

Use esse tipo de amostragem se seu aplicativo geralmente ultrapassar a cota mensal e se você não tiver a opção de usar os tipos de amostragem baseados em SDK.

Defina a taxa de amostragem na folha Cotas e Preços:

![Na folha Visão Geral do aplicativo, clique em Configurações, Cota, Amostras, escolha uma taxa de amostragem e clique em Atualizar.](./media/app-insights-sampling/04.png)

Assim como outros tipos de amostragem, o algoritmo retém os itens relacionados de telemetria. Por exemplo, quando você estiver analisando a telemetria na Pesquisa, poderá localizar a solicitação relacionada a uma exceção específica. As contagens de métrica como a taxa de solicitação e a taxa de exceções são mantidas corretamente.

Os pontos de dados que são descartados pela amostragem não estão disponíveis em nenhum recurso do Application Insights como [Exportação Contínua](app-insights-export-telemetry.md).

A amostragem de ingestão não funciona enquanto a amostragem adaptável ou de taxa fixa com base no SDK estiver em operação. Se a taxa de amostragem no SDK for menor que 100%, a taxa de amostragem de ingestão definida será ignorada.

> [AZURE.WARNING] O valor mostrado no bloco indica o valor definido para amostragem de ingestão. Ele não representará a taxa de amostragem real se a amostragem do SDK estiver em operação.


## Amostragem adaptável em seu servidor Web

A amostragem adaptativa está disponível para o SDK do Application Insights para ASP.NET v 2.0.0-beta3 e posterior, e está habilitada por padrão.


A amostragem adaptável afeta o volume de telemetria enviado do seu aplicativo de servidor Web para o serviço Application Insights. O volume é ajustado automaticamente para se manter em uma taxa máxima de tráfego especificada.

Ela não funciona em volumes baixos de telemetria e, portanto, um aplicativo em depuração ou um site com baixo uso não serão afetados.

Para atingir o volume de destino, parte da telemetria gerada é descartada. Assim como os outros tipos de amostragem, o algoritmo retém os itens de telemetria relacionados. Por exemplo, quando você estiver analisando a telemetria na Pesquisa, poderá localizar a solicitação relacionada a uma exceção específica.

As contagens de métrica, como a taxa de solicitações e a taxa de exceções são ajustadas para compensar a taxa de amostragem, para que mostrem valores aproximadamente corretos no Gerenciador de Métricas.

**Atualize os pacotes NuGet** de seu projeto para a versão de *pré-lançamento* mais recente do Application Insights: clique com o botão direito do mouse no projeto no Gerenciador de Soluções, marque a opção **Incluir pré-lançamento** e procure por Microsoft.ApplicationInsights.Web.

Em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), é possível ajustar diversos parâmetros no nó `AdaptiveSamplingTelemetryProcessor`. Os números mostrados são os valores padrão:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    A taxa de destino desejada pelo algoritmo adaptável **em cada host de servidor**. Se o seu aplicativo Web for executado em muitos hosts, reduza esse valor para permanecer dentro de sua taxa de destino de tráfego no portal do Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    O intervalo no qual a taxa atual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Talvez você queira reduzir esse intervalo se a sua telemetria estiver sujeita a picos repentinos.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    Quando o valor da porcentagem de amostragem muda, quanto tempo depois temos permissão para diminuir a porcentagem de amostragem novamente a fim de capturar menos dados.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageIncreaseTimeout>`

    Quando o valor da porcentagem de amostragem muda, quanto tempo depois temos permissão para aumentar a porcentagem de amostragem novamente a fim de capturar mais dados.

* `<MinSamplingPercentage>0.1</MinSamplingPercentage>`

    À medida que a porcentagem de amostragem varia, qual é o valor mínimo que podemos definir.

* `<MaxSamplingPercentage>100.0</MaxSamplingPercentage>`

    À medida que a porcentagem de amostragem varia, qual é o valor máximo que podemos definir.

* `<MovingAverageRatio>0.25</MovingAverageRatio>`

    No cálculo da média móvel, o peso atribuído ao valor mais recente. Use um valor menor ou igual a 1. Valores menores tornam o algoritmo menos reativo a mudanças repentinas.

* `<InitialSamplingPercentage>100</InitialSamplingPercentage>`

    O valor atribuído quando o aplicativo acabou de ser iniciado. Não o reduza enquanto estiver depurando.

### Alternativa: configurar amostragem adaptável no código

Em vez de ajustar a amostragem no arquivo .config, você pode usar o código. Isso permite especificar uma função de retorno de chamada invocada sempre que a taxa de amostragem é avaliada novamente. Você pode usar isso, por exemplo, para descobrir qual taxa de amostragem está sendo usada.

Remova o nó `AdaptiveSamplingTelemetryProcessor` do arquivo .config.



*C#*

```C#

    using Microsoft.ApplicationInsights;
    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.Channel.Implementation;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var adaptiveSamplingSettings = new SamplingPercentageEstimatorSettings();

    // Optional: here you can adjust the settings from their defaults.

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    
    builder.UseAdaptiveSampling(
         adaptiveSamplingSettings,

        // Callback on rate re-evaluation:
        (double afterSamplingTelemetryItemRatePerSecond,
         double currentSamplingPercentage,
         double newSamplingPercentage,
         bool isSamplingPercentageChanged,
         SamplingPercentageEstimatorSettings s
        ) =>
        {
          if (isSamplingPercentageChanged)
          {
             // Report the sampling rate.
             telemetryClient.TrackMetric("samplingPercentage", newSamplingPercentage);
          }
      });

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Saiba mais sobre os processadores de telemetria](app-insights-api-filtering-sampling.md#filtering).)


<a name="other-web-pages"></a>
## Amostragem para áginas da Web com JavaScript

Você pode configurar as páginas da Web para amostragem de taxa fixa de qualquer servidor.

Ao [configurar as páginas da Web para o Application Insights](app-insights-javascript.md), modifique o trecho de código que você receber do portal do Application Insights. (Em aplicativos ASP.NET, o trecho de código geralmente vai em \_Layout.cshtml.) Insira uma linha como `samplingPercentage: 10,` antes da chave de instrumentação:

    <script>
	var appInsights= ... 
	}({ 


    // Value must be 100/N where N is an integer.
    // Valid examples: 50, 25, 20, 10, 5, 1, 0.1, ...
	samplingPercentage: 10, 

	instrumentationKey:...
	}); 
	
	window.appInsights=appInsights; 
	appInsights.trackPageView(); 
	</script> 

Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro. Atualmente, a amostragem não dá suporte a outros valores.

Se você habilitar também a amostragem de taxa fixa no servidor, o cliente e o servidor serão sincronizados para que, na Pesquisa, você possa navegar entre exibições de página e solicitações relacionadas.


## Amostragem de taxa fixa para sites ASP.NET

A amostragem de taxa fixa reduz o tráfego enviado do seu servidor Web e de navegadores da Web. Ao contrário da amostragem adaptável, ela reduz a telemetria a uma taxa fixa decidida por você. Ela também sincroniza a amostragem de servidor e de cliente para que os itens relacionados sejam mantidos, por exemplo, se você examinar um modo de exibição de página na Pesquisa, poderá localizar a solicitação relacionada.

O algoritmo de amostragem retém os itens relacionados. Para cada evento de solicitação HTTP, ele e os eventos relacionados são descartados ou transmitidos.

No Metrics Explorer, as taxas como as contagens de solicitações e de exceções são multiplicadas por um fator para compensar a taxa de amostragem, para que elas sejam aproximadamente corretas.

1. **Atualize os pacotes NuGet do seu projeto** para a versão de *pré-lançamento* mais recente do Application Insights. Clique com o botão direito do mouse no projeto no Gerenciador de Soluções, marque a opção **Incluir pré-lançamento** e procure por Microsoft.ApplicationInsights.Web.

2. **Desabilitar a amostragem adaptável**: em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), remova ou comente o nó `AdaptiveSamplingTelemetryProcessor`.

    ```xml

    <TelemetryProcessors>
    <!-- Disabled adaptive sampling:
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    -->
    

    ```

2. **Habilitar o módulo de amostragem de taxa fixa.** Adicione esse trecho de código a [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md):

    ```XML

    <TelemetryProcessors>
     <Add  Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

      <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
      <SamplingPercentage>10</SamplingPercentage>
      </Add>
    </TelemetryProcessors>

    ```

> [AZURE.NOTE] Para o percentual de amostragem, escolha um percentual que esteja próximo a 100/N, em que N é um inteiro. Atualmente, a amostragem não dá suporte a outros valores.



### Alternativa: habilite a amostragem de taxa fixa no código do servidor


Em vez de definir o parâmetro de amostragem no arquivo .config, você pode usar o código.

*C#*

```C#

    using Microsoft.ApplicationInsights.Extensibility;
    using Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel;
    ...

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    builder.UseSampling(10.0); // percentage

    // If you have other telemetry processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

([Saiba mais sobre os processadores de telemetria](app-insights-api-filtering-sampling.md#filtering).)


## Quando usar a amostragem?

Amostragem adaptável é habilitada automaticamente se você usar o SDK do ASP.NET versão 2.0.0-beta3 ou posterior. Você pode usar a amostragem de ingestão (em nosso servidor) independente da versão do SDK utilizada.

A amostragem não é necessária para a maioria dos aplicativos de pequeno e médio porte. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas por meio da coleta de dados em todas as atividades de usuário.

 
As principais vantagens da amostragem são:

* O serviço Application Insights remove (“limita”) os pontos de dados quando o aplicativo envia uma taxa muito alta de telemetria em um curto intervalo de tempo.
* Permanecer dentro da [cota](app-insights-pricing.md) de pontos de dados para o seu tipo de preço.
* Para reduzir o tráfego de rede da coleção de telemetria.

### Que tipo de amostragem eu devo usar?


**Use a amostragem de ingestão se:**

* Você geralmente ultrapassa sua cota mensal de telemetria.
* Você estiver usando uma versão do SDK que não oferece suporte à amostragem. Por exemplo, o SDK do Java ou as versões do ASP.NET anteriores à 2.
* Você estiver recebendo muita telemetria dos navegadores dos usuários.

**Use a amostragem de taxa fixa se:**

* Você estiver usando o SDK do Application Insights para os serviços Web do ASP.NET versão 2.0.0 ou posterior e
* Você quiser uma amostragem sincronizada entre cliente e servidor, para que quando estiver investigando eventos na [Pesquisa](app-insights-diagnostic-search.md), você possa navegar entre os eventos relacionados no cliente e no servidor, por exemplo, exibições de página e solicitações http.
* Você tiver certeza sobre a porcentagem de amostragem apropriada para seu aplicativo. Ela deve ser alta o suficiente para obter métricas precisas, mas abaixo da taxa que excede sua cota de preços e limitações.


**Use a amostragem adaptável:**

Caso contrário, recomendamos a amostragem adaptável. Ela está habilitada por padrão no SDK do servidor ASP.NET, versão 2.0.0-beta3 ou posterior. Ela não reduz o tráfego até uma determinada taxa mínima e, portanto, não afetará um site de baixa utilização.


## Como saber se a amostragem está em operação?

Para descobrir a taxa de amostragem real, independentemente de onde ela tiver sido aplicada, use uma [consulta do Analytics](app-insights-analytics.md) como esta:

    requests | where timestamp > ago(1d)
    | summarize 100/avg(itemCount) by bin(timestamp, 1h) 
    | render areachart 

Em cada registro retido, o `itemCount` indica o número de registros originais que ele representa, igual a 1 + o número de registros descartados anteriormente.


## Como funciona a amostragem?

As amostragens de taxa fixa e adaptável são um recurso do SDK nas versões do ASP.NET a partir da 2.0.0. A amostragem de ingestão é um recurso do serviço Application Insights e poderá estar em operação se o SDK não estiver executando a amostragem.

O algoritmo de amostragem decide quais itens de telemetria descartar e quais manter (se estiver no SDK ou no serviço Application Insights). A decisão de amostragem baseia-se em várias regras que visam preservar todos os pontos de dados inter-relacionados intactos, mantendo uma experiência de diagnóstico no Application Insights que seja acionável e confiável mesmo com um conjunto reduzido de dados. Por exemplo, se para uma solicitação com falha seu aplicativo enviar itens de telemetria adicionais (como exceção e rastreamentos registrados desta solicitação), a amostragem não dividirá essa solicitação e outra telemetria. Ela mantém ou remove todos juntos. Como resultado, quando você examinar os detalhes da solicitação no Application Insights, é possível ver sempre a solicitação junto com seus itens de telemetria associados.

Para aplicativos que definem o “usuário” (ou seja, a maioria dos aplicativos Web típicos), a decisão de amostragem é baseada no hash da ID de usuário, o que significa que toda a telemetria para qualquer usuário específico é preservada ou removida. Para os tipos de aplicativos que não definem usuários (como serviços Web), a decisão de amostragem baseia-se na ID de operação da solicitação. Por fim, para os itens de telemetria que não têm uma ID de usuário nem de operação definida (por exemplo, itens telemetria relatados dos threads assíncronos sem contexto HTTP), a amostragem apenas captura um percentual dos itens de telemetria de cada tipo.

Ao apresentar a telemetria de volta para você, o serviço Application Insights ajusta as métricas pelo mesmo percentual de amostragem usado no momento da coleta, para compensar os pontos de dados ausentes. Portanto, ao examinar a telemetria no Application Insights, os usuários veem aproximações estatisticamente corretas que estão muito próximas aos números reais.

A precisão da aproximação depende principalmente do percentual de amostragem configurado. Além disso, a precisão aumenta para os aplicativos que lidam com um grande volume de solicitações geralmente semelhantes de uma grande quantidade de usuários. Por outro lado, para aplicativos que não funcionam com uma carga significativa, a amostragem não é necessária, pois esses aplicativos geralmente podem enviar toda a sua telemetria, ao mesmo tempo que permanecem dentro da cota, sem causar perda de dados da limitação.

Observe que o Application Insights não realiza a amostragem dos tipos de telemetria Métricas e Sessões, já que para esses tipos, uma redução na precisão pode ser altamente indesejável.

### Amostragem adaptável

A amostragem adaptável adiciona um componente que monitora a taxa atual de transmissão do SDK e ajusta a porcentagem de amostragem a fim de tentar permanecer dentro da taxa máxima desejada. O ajuste é recalculado em intervalos regulares e tem base em uma média móvel da taxa de transmissão de saída.

## Amostragem e o SDK do JavaScript

O SDK do lado do cliente (JavaScript) participa da amostragem de taxa fixa em conjunto com o SDK do lado do servidor. As páginas instrumentadas enviarão apenas a telemetria do lado do cliente dos mesmos usuários para os quais o lado do servidor tomou sua decisão de “amostragem”. Essa lógica é projetada para manter a integridade da sessão do usuário nos lados do cliente e do servidor. Como resultado, em qualquer item de telemetria específico no Application Insights é possível encontrar todos os outros itens de telemetria para esse usuário ou sessão.

*Minha telemetria do lado do cliente e do servidor não mostra exemplos coordenados como descrito acima.*

* Verifique se você habilitou a amostragem de taxa fixa tanto no servidor quanto no cliente.
* Certifique-se de que a versão do SDK é 2.0 ou superior.
* Verifique se você definiu o mesmo percentual de amostragem no cliente e no servidor.


## Perguntas frequentes 

*Por que a amostragem não se trata apenas de “coletar X% de cada tipo de telemetria”?*

 *  Embora essa abordagem de amostragem forneceria uma precisão muito alta em aproximações de métrica, ela interromperia a capacidade de correlacionar dados de diagnóstico por usuário, sessão e solicitação, que são essenciais para o diagnóstico. Portanto, a amostragem funciona melhor com a lógica “coletar todos os itens de telemetria para X% de usuários do aplicativo” ou “coletar toda a telemetria para X% das solicitações do aplicativo”. Para os itens de telemetria não associados às solicitações (como o processamento assíncrono em segundo plano), o fallback é “coletar X% de todos os itens para cada tipo de telemetria”.

*O percentual de amostragem pode ser alterado com o tempo?*

 * Sim, a amostragem adaptável altera gradualmente a porcentagem de amostragem com base no volume atualmente observado da telemetria.

 

*Se eu usar a amostragem de taxa fixa, como saber qual percentual de amostragem funcionará melhor para o meu aplicativo?*

* Uma maneira é iniciar com a amostragem adaptável, descobrir qual taxa se adequa (consulte a pergunta anterior) e, em seguida, alternar para a amostragem de taxa fixa usando essa taxa.

    Caso contrário, é preciso adivinhar. Analise o uso atual da telemetria na AI, observe qualquer limitação que esteja ocorrendo e estime o volume da telemetria coletada. Essas três entradas, junto com seu tipo de preço selecionado, sugere o quanto você talvez queira reduzir o volume da telemetria coletada. No entanto, um aumento no número de usuários ou alguma outra mudança no volume de telemetria pode invalidar sua estimativa.

*O que acontece se eu configurar o percentual de amostragem com um valor muito baixo?*

* Um percentual de amostragem excessivamente baixo (amostragem superagressiva) reduz a precisão das aproximações quando o Application Insights tenta compensar a visualização dos dados para a redução do volume de dados. Além disso, a experiência de diagnóstico pode ser afetada negativamente, já que algumas das solicitações lentas ou raramente com falhas podem ser amostradas.

*O que acontece se eu configurar o percentual de amostragem com um valor muito alto?*

* Configurar um percentual de amostragem muito alto (não agressivo o suficiente) resultará em uma redução insuficiente no volume da telemetria coletada. Ainda pode ocorrer perda de dados de telemetria relacionada à limitação, e o custo do uso do Application Insights pode ser maior do que o planejado devido a encargos excedentes.

*Em quais plataformas posso usar a amostragem?*

* A amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não estiver executando a amostragem. Isso funcionaria, por exemplo, se o seu aplicativo usasse um servidor Java, ou se você estivesse usando uma versão mais antiga do SDK do ASP.NET.

* Se você estiver usando o SDK do ASP.NET, versões 2.0.0 e acima (hospedado no Azure ou em seu próprio servidor), você terá a amostragem adaptável por padrão, mas poderá alternar para a taxa fixa, conforme descrito acima. Com a amostragem de taxa fixa, o SDK do navegador sincroniza automaticamente os eventos relacionados ao exemplo.

*Há alguns eventos raros que sempre desejo ver. Como posso fazê-los passar pelo módulo de amostragem?*

 * Inicialize uma instância separada de TelemetryClient com um novo TelemetryConfiguration (não o Active padrão). Use isso para enviar seus eventos raros.

<!---HONumber=AcomDC_0831_2016-->