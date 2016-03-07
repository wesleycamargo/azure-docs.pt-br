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
	ms.date="02/18/2016" 
	ms.author="awills"/>

#  Amostragem no Application Insights

*O Application Insights está em modo de visualização.*


A amostragem é um recurso no Application Insights que permite coletar e armazenar um conjunto reduzido de telemetria, ao mesmo tempo que mantém uma análise estatisticamente correta dos dados do aplicativo. Ela reduz o tráfego e ajuda a evitar a [limitação](app-insights-pricing.md#data-rate). Os dados são filtrados de tal forma que itens relacionados sejam permitidos, de modo que você possa navegar entre os itens quando estiver realizando investigações de diagnóstico. Quando as contagens de métrica são apresentadas a você no portal, elas são normalizadas novamente para levar em conta a amostragem, a fim de minimizar qualquer efeito sobre as estatísticas.

A amostragem está atualmente na versão Beta e pode ser alterada no futuro.

Há três módulos de amostragem alternativos:

* A **amostragem adaptável** ajusta automaticamente a porcentagem de amostragem para atingir um determinado volume de solicitações. Ela está habilitada por padrão no SDK do Application Insights para ASP.NET, versão 2.0.0-beta3 ou posterior. 
* A **amostragem de taxa fixa** também está disponível nas versões do SDK posteriores a 2. Especifique a porcentagem de amostragem. Isso funciona no cliente JavaScript e no servidor ASP.NET. O cliente e o servidor sincronizarão suas amostragens para que, na Pesquisa, você possa navegar entre exibições de página e solicitações relacionadas.
* **Amostragem de ingestão**. Isso acontece no serviço Application Insights. O SDK envia toda a telemetria do seu navegador e do seu servidor, mas nós mantemos apenas uma porcentagem. Ele é adaptável, e só funciona se seu aplicativo enviar mais do que um volume mínimo de telemetria e somente se o SDK não estiver executando um dos outros tipos de amostragem. Também é influenciado pelo tipo de preço que você está usando. Embora não reduza o tráfego, ajuda a manter sua cota mensal.

## Habilitando a amostragem adaptável

**Atualize os pacotes NuGet** de seu projeto para a versão de *pré-lançamento* mais recente do Application Insights: clique com o botão direito do mouse no projeto no Gerenciador de Soluções, marque a opção **Incluir pré-lançamento** e procure por Microsoft.ApplicationInsights.Web.

Em [ApplicationInsights.config](app-insights-configuration-with-applicationinsights-config.md), é possível ajustar diversos parâmetros no nó `AdaptiveSamplingTelemetryProcessor`. Os números mostrados são os valores padrão:

* `<MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>`

    A taxa de destino desejada pelo algoritmo adaptável para **um host de servidor único**. Se o seu aplicativo Web for executado em muitos hosts, convém reduzir esse valor para permanecer dentro de sua taxa de destino de tráfego no portal do Application Insights.

* `<EvaluationInterval>00:00:15</EvaluationInterval>`

    O intervalo no qual a taxa atual de telemetria é reavaliada. Avaliação é executada como uma média móvel. Talvez você queira reduzir esse intervalo se a sua telemetria estiver sujeita a picos repentinos.

* `<SamplingPercentageDecreaseTimeout>00:02:00</SamplingPercentageDecreaseTimeout>`

    Quando o valor da porcentagem de amostragem muda, quanto tempo depois temos permissão para diminuir a porcentagem de amostragem novamente a fim de capturar menos dados.

* `<SamplingPercentageIncreaseTimeout>00:15:00</SamplingPercentageDecreaseTimeout>`

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

    var builder = TelemetryConfiguration.Active.GetTelemetryProcessorChainBuilder();
    
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


## Habilitação da amostragem de taxa fixa no servidor

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

Amostragem adaptável é habilitada automaticamente se você usar o SDK do ASP.NET versão 2.0.0-beta3 ou posterior. Não importa a versão SDK usada, a amostragem de ingestão (em nosso servidor) poderá funcionar se não houver amostragem no SDK.

A amostragem não é necessária para a maioria dos aplicativos de pequeno e médio porte. As informações de diagnóstico mais úteis e as estatísticas mais precisas são obtidas por meio da coleta de dados em todas as atividades de usuário.

 
As principais vantagens da amostragem são:

* O serviço Application Insights remove (“limita”) os pontos de dados quando o aplicativo envia uma taxa muito alta de telemetria em um curto intervalo de tempo. 
* Permanecer dentro da [cota](app-insights-pricing.md) de pontos de dados para o seu tipo de preço. 
* Para reduzir o tráfego de rede da coleção de telemetria. 

### Amostragem fixa ou adaptável

Todas as formas de amostragem funcionam apenas acima de um determinado limite de tráfego. Por exemplo, ela não afetará a maioria das sessões de depuração ou sites com pouca utilização.

Use a amostragem de taxa fixa se:

* Você quiser uma amostragem sincronizada entre cliente e servidor, para que quando estiver investigando eventos na [Pesquisa](app-insights-diagnostic-search.md), você possa navegar entre os eventos relacionados no cliente e no servidor, por exemplo, exibições de página e solicitações http.
* Você tiver certeza sobre a porcentagem de amostragem apropriada para seu aplicativo. Ela deve ser alta o suficiente para obter métricas precisas, mas abaixo da taxa que excede sua cota de preços e limitações. 
* Você não está depurando seu aplicativo. Quando você pressiona F5 e experimenta algumas páginas de seu aplicativo, provavelmente deseja ver toda a telemetria.

Caso contrário, recomendamos a amostragem adaptável. Ela está habilitada por padrão no SDK do servidor ASP.NET.

Se você não usar amostragem no SDK - por exemplo, se estiver usando Java ou se desabilitar a amostragem em ApplicationInsights.config - a amostragem de ingestão poderá funcionar à medida que os dados chegam ao serviço Application Insights. É uma forma de amostragem adaptável.

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

O SDK do lado do cliente (JavaScript) participa da amostragem em conjunto com o SDK do lado do servidor. As páginas instrumentadas enviarão apenas a telemetria do lado do cliente dos mesmos usuários para os quais o lado do servidor tomou sua decisão de “amostragem”. Essa lógica é projetada para manter a integridade da sessão do usuário nos lados do cliente e do servidor. Como resultado, em qualquer item de telemetria específico no Application Insights é possível encontrar todos os outros itens de telemetria para esse usuário ou sessão.

*Minha telemetria do lado do cliente e do servidor não mostra exemplos coordenados como descrito acima.*

* Verifique se você habilitou a amostragem tanto no servidor quanto no cliente.
* Certifique-se de que a versão do SDK é 2.0 ou superior.
* Verifique se você definiu o mesmo percentual de amostragem no cliente e no servidor.


## Perguntas frequentes 

*Por que a amostragem não se trata apenas de “coletar X% de cada tipo de telemetria”?*

 *  Embora essa abordagem de amostragem forneceria uma precisão muito alta em aproximações de métrica, ela interromperia a capacidade de correlacionar dados de diagnóstico por usuário, sessão e solicitação, que são essenciais para o diagnóstico. Portanto, a amostragem funciona melhor com a lógica “coletar todos os itens de telemetria para X% de usuários do aplicativo” ou “coletar toda a telemetria para X% das solicitações do aplicativo”. Para os itens de telemetria não associados às solicitações (como o processamento assíncrono em segundo plano), o fallback é “coletar X% de todos os itens para cada tipo de telemetria”. 

*O percentual de amostragem pode ser alterado com o tempo?*

 * Sim, a amostragem adaptável altera gradualmente a porcentagem de amostragem com base no volume atualmente observado da telemetria.

*Posso descobrir a taxa de amostragem que a amostragem adaptável está usando?*

 * Sim - use o método do código de configuração de amostragem adaptável para oferecer um retorno de chamada que obtenha a taxa de amostragem. Se você usar a exportação contínua, poderá ver a taxa de amostragem listada nos pontos de dados exportados.

*Se eu usar a amostragem de taxa fixa, como saber qual percentual de amostragem funcionará melhor para o meu aplicativo?*

* Uma maneira é iniciar com a amostragem adaptável, descobrir qual taxa se adequa (consulte a pergunta anterior) e, em seguida, alternar para a amostragem de taxa fixa usando essa taxa. 

    Caso contrário, é preciso adivinhar. Analise o uso atual da telemetria na AI, observe qualquer limitação que esteja ocorrendo e estime o volume da telemetria coletada. Essas três entradas, junto com seu tipo de preço selecionado, sugerirá quanto você talvez queira reduzir o volume da telemetria coletada. No entanto, um aumento no número de usuários ou alguma outra mudança no volume de telemetria pode invalidar sua estimativa.

*O que acontece se eu configurar o percentual de amostragem com um valor muito baixo?*

* Um percentual de amostragem excessivamente baixo (amostragem superagressiva) reduzirá a precisão das aproximações quando o Application Insights tentar compensar a visualização dos dados para a redução do volume de dados. Além disso, a experiência de diagnóstico pode ser afetada negativamente, já que algumas das solicitações lentas ou raramente com falhas podem ser amostradas.

*O que acontece se eu configurar o percentual de amostragem com um valor muito alto?*

* Configurar um percentual de amostragem muito alto (não agressivo o suficiente) resultará em uma redução insuficiente no volume da telemetria coletada. Ainda pode ocorrer perda de dados de telemetria relacionada à limitação, e o custo do uso do Application Insights pode ser maior do que o planejado devido a encargos excedentes.

*Em quais plataformas posso usar a amostragem?*

* A amostragem de ingestão pode ocorrer automaticamente para qualquer telemetria acima de um determinado volume, se o SDK não estiver executando a amostragem. Isso funcionaria, por exemplo, se o seu aplicativo usasse um servidor Java, ou se você estivesse usando uma versão mais antiga do SDK do ASP.NET.

* Se você estiver usando o SDK do ASP.NET, versões 2.0.0 e acima (hospedado no Azure ou em seu próprio servidor), você terá a amostragem adaptável por padrão, mas poderá alternar para a taxa fixa, conforme descrito acima. Com a amostragem de taxa fixa, o SDK do navegador sincroniza automaticamente os eventos relacionados ao exemplo.

*Há alguns eventos raros que sempre desejo ver. Como posso fazê-los passar pelo módulo de amostragem?*

 * Inicialize uma instância separada de TelemetryClient com um novo TelemetryConfiguration (não o Active padrão). Use isso para enviar seus eventos raros.

<!---HONumber=AcomDC_0224_2016-->