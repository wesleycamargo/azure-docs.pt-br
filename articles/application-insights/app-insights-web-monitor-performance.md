<properties 
	pageTitle="Monitorar a integridade e o uso do aplicativo com o Application Insights" 
	description="Introdução ao Application Insights. Analise o uso, disponibilidade e desempenho de seu local ou aplicativos do Microsoft Azure." 
	services="application-insights" 
    documentationCenter=""
	authors="alancameronwills" 
	manager="keboyd"/>

<tags 
	ms.service="application-insights" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="ibiza" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/02/2015" 
	ms.author="awills"/>
 
# Monitore o desempenho em aplicativos da web

*O Application Insights está em modo de visualização.*


Certifique-se de que seu aplicativo está sendo bem executado, e saiba rapidamente sobre quaisquer falhas. O [Application Insights][start] vai lhe dizer sobre quaisquer problemas de desempenho e exceções, e o ajudará a localizar e diagnosticar as causas raízes.

O Application Insights pode monitorar os aplicativos da web ASP.NET e serviços WCF hospedados no local ou em máquinas virtuais, bem como em sites do Microsoft Azure.


## <a name="setup"></a>Configurar o monitoramento de desempenho

Se você ainda não tem o Application Insights adicionado ao seu projeto (ou seja, não tem o ApplicationInsights.config), escolha uma destas formas para começar:

* [Adicione o Application Insights ao seu projeto de aplicativo no Visual Studio][greenbrown] - Recomendado. Além do monitoramento de desempenho passivo, você pode inserir o registro em log do diagnóstico e acompanhar o uso.
* [Monitorar o desempenho de um site ao vivo agora][redfield] - Dessa forma, você não precisa atualizar o projeto do aplicativo ou reimplantar o site da web.
* [Para um site do Microsoft Azure](../insights-how-to-customize-monitoring.md) você já pode ver as métricas na lente de Monitoramento do site. 

Usando qualquer um desses métodos, você verá rapidamente os dados na lâmina de visão geral do Application Insights.


## <a name="view"></a>Explorando métricas

Clique em qualquer bloco para ver mais detalhes, e para ver os resultados por um período mais longo. Por exemplo, clique no bloco Solicitações e, em seguida, selecione um intervalo de tempo:


![Clique por mais dados e selecione um intervalo de tempo](./media/app-insights-web-monitor-performance/appinsights-48metrics.png)

Clique em um gráfico para selecionar outras medidas que são exibidas, ou adicionar um novo gráfico e selecionar a métrica:

![Clique em um gráfico para escolher métricas](./media/app-insights-web-monitor-performance/appinsights-61perfchoices.png)

> [AZURE.NOTE]**Desmarque todas as métricas** para ver a seleção completa que está disponível. As métricas se enquadram em grupos; quando qualquer membro de um grupo é selecionado, somente os outros membros do grupo aparecem.


## <a name="metrics"></a>O que significa tudo isso? Blocos e relatórios de desempenho

Existe uma variedade de métricas de desempenho que você pode obter. Vamos começar com estas que aparecem por padrão na folha do aplicativo.


### Solicitações

O número de solicitações de HTTP receberam em um período especifico. Compare isso com os resultados em outros reatórios para ver como seu aplicativo se comporta conforme a carga varia.

As solicitações HTTP incuem todas as solicitações GET ou POST para páginas, dados e imagens.

Clique no mosaico para obter contagens para URLs específicas.

### Tempo de resposta média

Mede o tempo entre uma solicitação da web inserindo seu aplicativo e a resposta que está sendo devolvida.

Os pontos mostram uma média da movimentação. Se existe várias solicitações, pode haver agumas que desviam da média sem um pico óbvio ou profundo no gráfico.

Procure por picos incomuns. Em geral, espere o tempo de resposta para eevar com uma elevação nas solicitações. Se a elevação é desproporcional, seu aplicativo pode ser atingido por um limite de recurso como CPU ou a capacidade de um serviço que usa.

Clique no bloco para obter tempos para URLs específicas.

![](./media/app-insights-web-monitor-performance/appinsights-42reqs.png)


### Solicitações mais lentas

![](./media/app-insights-web-monitor-performance/appinsights-44slowest.png)

Mostra quais solicitações podem precisar de sintonização de desempenho.


### Solicitações falhas

![](./media/app-insights-web-monitor-performance/appinsights-46failed.png)

Uma contagem de solicitações que gerou exceções não capturadas.

Clique no mosaico para ver os detalhes de falhas específicas, e selecione uma solicitação individual para ver seus detalhes.

Somente uma amostra representativa de falhas é retida para inspeção individual.

### Outras métricas

Para ver o que outras métricas que você pode exibir, clique em um gráfico e, em seguida, desmarque a seleção de todas as métricas para ver o conjunto disponível completo. Clique em (i) para ver cada definição da métrica.

![Desmarque a seleção de todas as métricas para ver o conjunto completo](./media/app-insights-web-monitor-performance/appinsights-62allchoices.png)


Ao selecionar qualquer métrica, desabilitará as outras que não podem aparecer no mesmo gráfico.

## Contadores de desempenho do sistema

Algumas das métricas dentre as quais você pode escolher são [contadores de desempenho](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters). O Windows fornece uma ampla variedade deles, mas você também pode definir seus próprios contadores de desempenho.

Este exemplo mostra os contadores de desempenho que estão disponíveis por padrão. [Adicionamos um gráfico separado ](app-insights-metrics-explorer.md#editing-charts-and-grids) para cada contador e nomeamos o gráfico [salvando-o como um favorito](app-insights-metrics-explorer.md#editing-charts-and-grids):

![](./media/app-insights-web-monitor-performance/sys-perf.png)


Se os contadores que deseja não estiverem na lista de propriedades, você poderá adicioná-los ao conjunto coletado pelo SDK. Abra ApplicationInsights.config e edite a diretiva do coletor de desempenho:

    <Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCollector.PerformanceCollectorModule, Microsoft.ApplicationInsights.Extensibility.PerfCollector">
      <Counters>
        <Add PerformanceCounter="\Objects\Processes"/>
        <Add PerformanceCounter="\Sales(electronics)# Items Sold" ReportAs="Item sales"/>
      </Counters>
    </Add>

O formato é `\Category(instance)\Counter"`, ou apenas `\Category\Counter` para categorias que não têm instâncias. Para saber quais contadores estão disponíveis em seu sistema, leia [esta introdução](http://www.codeproject.com/Articles/8590/An-Introduction-To-Performance-Counters).

`ReportAs` é necessário para os nomes de contadores que contêm caracteres além dos seguintes: letras, colchetes arredondados, barras "/", hifens, sublinhados, espaços e pontos.

Se você especificar uma instância, ela será coletada como uma propriedade "CounterInstanceName" da métrica reportada.

Se preferir, você pode escrever código que tenha o mesmo efeito:

    var perfCollector = new PerformanceCollectorModule();
    perfCollector.Counters = new List<CustomPerformanceCounterCollectionRquest>();
    perfCollector.Counters.Add(new CustomPerformanceCounterCollectionRquest(
      @"\Sales(electronics)# Items Sold", "Items sold"));
    perfCollector.Initialize(TelemetryConfiguration.Active);
    TelemetryConfiguration.Active.TelemetryModules.Add(perfCollector);



## Definir alertas

Para ser notificado por email sobre valores incomuns de qualquer métrica, adicione um alerta. Você pode escolher para enviar o email para os administradores de conta ou para endereços de email específicos.

![](./media/app-insights-web-monitor-performance/appinsights-413setMetricAlert.png)

Defina o recurso antes de outras propriedades. Não escolha os recursos webtest se você quer definir alertas em métricas de desempenho ou de uso.

Observe as unidades quando você for solicitado para inserir o valor de limite.

*Não vejo o botão Adicionar Alerta.* - Esta é uma conta de grupo para a qual você tem acesso somente leitura? Verifique com o administrador da conta.

## <a name="diagnosis"></a>Diagnosticando problemas

Aqui estão algumas dicas para localizar e diagnosticar problemas de desempenho:

* Configure os [testes de web][availability] para ser alertado se seu site cair ou responder de forma incorreta ou lenta. 
* Compare a contagem de Solicitação com outras métricas para ver se falhas ou resposta lenta são relatadas ao carregar.
* [Inserir e pesquisar instruções de rastreamento][diagnostic] em seu código para ajudar a detectar problemas.

## <a name="next"></a>Próximas etapas

[Testes da web][availability] - Tem solicitações da web enviadas ao seu aplicativo em intervalos regulares em todo o mundo.

[Capture e pesquise rastreamento de diagnóstico][diagnostic] - Inserir chamadas de rastreamento e separar através dos resultados para problemas de pinpoint.

[Rastreamento de uso][usage] - Saiba como as pessoas usam se aplicativo.

[Solução de problemas][qna] - e Perguntas e respostas

## Vídeo

[AZURE.VIDEO performance-monitoring-application-insights]

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[diagnostic]: app-insights-diagnostic-search.md
[greenbrown]: app-insights-start-monitoring-app-health-usage.md
[qna]: app-insights-troubleshoot-faq.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-get-started.md
[usage]: app-insights-web-track-usage.md

 

<!---HONumber=August15_HO8-->