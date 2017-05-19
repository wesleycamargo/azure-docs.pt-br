---
title: "Métrica e diagnósticos personalizados em tempo real no Application Insights do Azure | Microsoft Docs"
description: "Monitore seu aplicativo Web em tempo real usando métrica personalizada e diagnostique problemas com um feed em tempo real de falhas, rastreamentos e eventos."
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 1f471176-38f3-40b3-bc6d-3f47d0cbaaa2
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: 86e01cf6cb14334e85da4102610fa7feb66cb543
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017

---

# <a name="custom-live-metrics-and-events-monitor--diagnose-with-1-second-latency"></a>Métricas e eventos personalizados em tempo real: monitorar e diagnosticar com latência de um segundo 
O Live Metrics Stream mostra as métricas e eventos do [Application Insights](app-insights-overview.md) com uma latência quase de tempo real de um segundo. Esse monitoramento instantâneo ajuda a reduzir o tempo médio para detectar e diagnosticar e o ajuda a manter os SLAs. Você pode:
* Monitorar KPIs personalizados em tempo real: faça experimentos filtros e com a divisão e segmentação Application Insights Telemetry existente para obter os KPIs mais relevantes instantaneamente no portal. Nenhuma alteração de código ou de configuração ou implantações é necessária. Qualquer métrica ou medida personalizada que você estiver enviando também fica disponível.
* Detectar e diagnosticar em tempo real: veja falhas de solicitação e de dependência quando elas ocorrem, bem como rastreamentos de exceção detalhados. Filtre todos os problemas conhecidos para se concentrar em problemas novos/reais.
* Depuração em tempo real: reproduza um problema e veja toda a telemetria relacionada em tempo real, com filtragem personalizada até chegar a uma ID de sessão específica (ou com qualquer atributo personalizado) que identifica a interação reproduzida. Colete informações de quaisquer/todos os servidores para obter um ponto de partida para corrigir o problema.
* Veja instantaneamente como o consumo de recursos reage à carga: monitore *qualquer* contador de desempenho do Windows em tempo real, enquanto executa testes de carga ou monitore a produção para agir antes que algo seja afetado negativamente. Nenhuma alteração de configuração ou implantação é necessária.
* Valide uma correção que está sendo lançada. Verifique se a atualização de seu serviço está indo bem enquanto ela ocorre. Valide se a falha que você corrigiu de fato parou de ocorrer.
* Identifique com facilidade um servidor que está apresentando problemas e filtre todo o KPI/feed em tempo real para apenas aquele servidor.

Os dados do Fluxo de eventos e métricas em tempo real são gratuitos: eles não são acrescentados à sua fatura. Os dados são transmitidos de seu servidores sob demanda quando você abre a experiência do portal. Os dados persistem somente durante o período em que estão no gráfico e, em seguida, são descartados. Toda a funcionalidade está disponível para aplicativos clássicos ASP.NET e aplicativos .NET Core têm apenas um conjunto fixo de métricas em tempo real e falhas de exemplo no momento. Estamos atualizando todos os SDKs com suporte de acordo com os recursos de transmissão ao vivo mais recentes. 

Nós coletamos a o fluxo em tempo real das instâncias do aplicativo antes que qualquer amostragem ou qualquer TelemetryProcessors personalizado seja aplicado. 

![Vídeo do Live Metrics Stream](./media/app-insights-live-stream/youtube.png) [Vídeo do Live Metrics Stream](https://www.youtube.com/watch?v=zqfHf1Oi5PY)

Acesse as métricas e eventos em tempo real clicando na opção à esquerda ou no botão na folha de Visão geral:

![Na folha Visão Geral, clique em Live Stream](./media/app-insights-live-stream/live-stream-2.png)

## <a name="custom-live-kpi"></a>KPI em tempo real personalizado
Você pode monitorar o KPI personalizado em tempo real aplicando filtros arbitrários a qualquer Application Insights Telemetry no portal. Clique no controle de filtro que é exibido quando você passa o mouse sobre qualquer um dos gráficos. O gráfico a seguir plota um KPI personalizado de contagem de solicitações com filtros nos atributos de URL e a duração. Valide seus filtros com a seção Versão Prévia do Fluxo, que mostra um feed em tempo real da telemetria que corresponde aos critérios que você especificou em qualquer ponto no tempo. 

![KPI de solicitação personalizado](./media/app-insights-live-stream/live-stream-filteredMetric.png)

Você pode monitorar um valor diferente da Contagem. As opções dependem do tipo de fluxo, que poderia ser qualquer Application Insights Telemetry: solicitações, dependências, exceções, rastreamentos, eventos ou métricas. Ela pode ser sua própria [medida personalizada](app-insights-api-custom-events-metrics.md#properties):

![Opções de valor](./media/app-insights-live-stream/live-stream-valueoptions.png)

Além da Application Insights Telemetry, você também pode monitorar qualquer contador de desempenho do Windows selecionando entre as opções de fluxo e fornecendo o nome do contador de desempenho.

Métricas em tempo real são agregadas em dois pontos: localmente em cada servidor e, em seguida, em todos os servidores. Você pode alterar o padrão de ambos selecionando outras opções nos respectivos menus suspensos.

## <a name="sample-telemetry-custom-live-diagnostic-events"></a>Telemetria de exemplo: eventos de diagnóstico em tempo real personalizados
Por padrão, o feed de eventos em tempo real mostra exemplos de solicitações com falha e chamadas de dependência, exceções, eventos e rastreamentos. Clique no ícone do filtro para ver os critérios aplicados em qualquer ponto no tempo. 

![Feed em tempo real padrão](./media/app-insights-live-stream/live-stream-eventsdefault.png)

Assim como acontece com as métricas, você pode especificar qualquer critério arbitrário para qualquer um dos tipos de Application Insights Telemetry. Neste exemplo, estamos selecionando falhas, rastreamentos e eventos de solicitação específicos. Também estamos selecionando todas as exceções e falhas de dependência.

![Feed em tempo real personalizado](./media/app-insights-live-stream/live-stream-events.png)

Observação: no momento, para critérios baseados em mensagens de exceção, use a mensagem de exceção mais externa. No exemplo anterior, para filtrar a exceção benigna com a mensagem de exceção interna (segue o delimitador "< –") "O cliente se desconectou.", use um critério de que a mensagem não contém "Erro ao ler o conteúdo da solicitação".

Consulte os detalhes de um item no feed em tempo real clicando nele. Você pode pausar o feed clicando em **Pausar** ou simplesmente rolando para baixo ou clicando em um item. O feed em tempo real será retomado quando você rolar de volta para o início ou clicando no contador de itens coletados enquanto ele estava em pausa.

![Amostra de falhas em tempo real](./media/app-insights-live-stream/live-metrics-eventdetail.png)

## <a name="filter-by-server-instance"></a>Filtrar por instância do servidor

Se você quiser monitorar uma instância de função de servidor específico, filtre por servidor.

![Amostra de falhas em tempo real](./media/app-insights-live-stream/live-stream-filter.png)

## <a name="sdk-requirements"></a>Requisitos do SDK
O Fluxo de métricas em tempo real personalizado está disponível com a versão 2.4.0-beta2 ou mais recente do [SDK do Application Insights para Web](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/). Lembre-se de selecionar a opção "Incluir pré-lançamento" no gerenciador de pacotes do NuGet.

## <a name="authenticated-channel"></a>Canal autenticado
Os critérios de filtro personalizados especificados são enviados para o componente de Métricas em tempo real no SDK do Application Insights. Os filtros podem conter informações confidenciais, como customerIDs. Você pode proteger o canal com uma chave de API secreta além da chave de instrumentação.
### <a name="create-an-api-key"></a>Criar uma chave de API

![Criar chave de API](./media/app-insights-live-stream/live-metrics-apikeycreate.png)

### <a name="add-api-key-to-configuration"></a>Adicionar chave de API à configuração
No arquivo applicationinsights.config, adicione AuthenticationApiKey a QuickPulseTelemetryModule:
``` XML

<Add Type="Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.QuickPulse.QuickPulseTelemetryModule, Microsoft.AI.PerfCounterCollector">
      <AuthenticationApiKey>YOUR-API-KEY-HERE</AuthenticationApiKey>
</Add> 

```
Ou, no código, defina-a no QuickPulseTelemetryModule:

``` C#

    module.AuthenticationApiKey = "YOUR-API-KEY-HERE";

```

No entanto, caso reconheça e confie em todos os servidores conectados, você pode testar os filtros personalizados sem o canal autenticado. Essa opção está disponível por seis meses. Essa substituição é necessária uma vez a cada nova sessão ou quando um novo servidor ficar online.

![Opções de autenticação das métricas em tempo real](./media/app-insights-live-stream/live-stream-auth.png)

>[!NOTE]
>É altamente recomendável que você configure o canal autenticado antes de inserir informações potencialmente confidenciais, como CustomerIDs, nos critérios de filtro.
>

## <a name="troubleshooting"></a>Solucionar problemas

Não há dados? Se seu aplicativo estiver em uma rede protegida: o Fluxo de métricas em tempo real usará endereços IP diferentes de outras Application Insights Telemetries. Certifique-se de que [esses endereços IP](app-insights-ip-addresses.md) estejam abertos em seu firewall.



## <a name="next-steps"></a>Próximas etapas
* [Monitorando o uso com o Application Insights](app-insights-web-track-usage.md)
* [Usando a Pesquisa de diagnóstico](app-insights-diagnostic-search.md)


