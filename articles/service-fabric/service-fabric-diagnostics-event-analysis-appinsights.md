---
title: Análise de Evento do Azure Service Fabric com Application Insights | Microsoft Docs
description: Saiba mais sobre visualização e análise de eventos utilizando o Application Insights para o monitoramento e diagnóstico de clusters do Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/04/2018
ms.author: srrengar
ms.openlocfilehash: aedbc5925a6e101299170843abef79ef6125eafe
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/30/2018
ms.locfileid: "50230413"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Visualização e análise de eventos com o Application Insights

O Microsoft Azure Application Insights é uma plataforma extensível para monitoramento e diagnóstico de aplicativos. Inclui uma poderosa ferramenta de análise e consulta, visualizações e dashboard personalizável e outras opções, incluindo alertas automatizados. É a plataforma recomendada para monitoramento e diagnóstico para aplicativos e serviços do Service Fabric. Este artigo ajuda a resolver as seguintes perguntas comuns

* Como saber o que está acontecendo no aplicativo e serviços e coletar telemetria
* Como fazer para solucionar problemas do aplicativo, especialmente os serviços que comunicam-se uns com os outros
* Como fazer para obter métrica sobre o desempenho dos serviços, por exemplo, tempo de carregamento da página, solicitações http

O objetivo deste artigo é mostrar como obter insights e solucionar problemas no Application Insights. Se você quiser aprender a configurar o Application Insights com o Service Fabric, confira este [tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-application-insights"></a>Monitoramento no Application Insights

O Application Insights tem uma experiência avançada ao usar o Service Fabric. Na página de visão geral, o Application Insights fornece informações importantes sobre seu serviço, como o tempo de resposta e o número de solicitações processadas. Ao clicar no botão "Pesquisar" na parte superior, é possível ver uma lista de solicitações recentes no aplicativo. Além disso, você poderá ver solicitações com falha e diagnosticar quais erros podem ter ocorrido.

![Visão geral do Application Insights](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

No painel direito da imagem anterior, há dois tipos principais de entradas na lista: solicitações e eventos. Solicitações são chamadas feitas à API do aplicativo por meio de solicitações HTTP nesse caso e os eventos são eventos personalizados, que funcionam como telemetria que você pode adicionar em qualquer parte do código. É possível explorar ainda mais a instrumentação dos aplicativos em [API do Application Insights para métricas e eventos personalizados](../application-insights/app-insights-api-custom-events-metrics.md). Clicar em uma solicitação exibirá detalhes adicionais, conforme mostrado na imagem a seguir, incluindo dados específicos do Service Fabric, que são coletados no pacote nuget do Application Insights Service Fabric. Essas informações são úteis para solucionar problemas e saber qual é o estado do aplicativo e todas essas informações podem ser pesquisadas no Application Insights

![Detalhes do pedido de insights do aplicativo](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

O Application Insights tem uma exibição designada para consultar todos os dados que chegam. Clique em "Metrics Explorer" na parte superior da página Visão geral para navegar até o portal do Application Insights. Aqui, é possível executar consultas em eventos personalizados mencionados anteriormente, solicitações, exceções, contadores de desempenho e outras métricas usando a linguagem de consulta do Kusto. O exemplo a seguir mostra todas as solicitações na última 1 hora.

![Detalhes do pedido de insights do aplicativo](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Para explorar ainda mais os recursos do portal do Application Insights, acesse a documentação do portal [Application Insights](../application-insights/app-insights-dashboards.md).

### <a name="configuring-application-insights-with-wad"></a>Configurar o Application Insights com WAD

>[!NOTE]
>No momento, isso só é aplicável a clusters do Windows.

Há duas maneiras principais de enviar dados do WAD para o Azure Application Insights, o que é obtido pela adição de um coletor do Application Insights à configuração do WAD, conforme detalhado em [este artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-application-insights-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicionar uma chave de instrumentação do Application Insights ao criar um cluster no portal do Azure

![Adicionar uma AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ao criar um cluster, se o Diagnóstico estiver ativado em "On", um campo opcional para inserir uma chave de Instrumentação do Application Insights será exibido. Se você colar sua chave do Application Insights aqui, o coletor do Application Insights será configurado automaticamente no modelo do Resource Manager usado para implantar o cluster.

#### <a name="add-the-application-insights-sink-to-the-resource-manager-template"></a>Adicionar o coletor do Application Insights para o modelo do Resource Manager

No "WadCfg" do modelo do Resource Manager, adicione um "Coletor", incluindo as duas alterações a seguir:

1. Adicione a configuração do coletor diretamente após a conclusão da declaração de `DiagnosticMonitorConfiguration`:

    ```json
    "SinksConfig": {
        "Sink": [
            {
                "name": "applicationInsights",
                "ApplicationInsights": "***ADD INSTRUMENTATION KEY HERE***"
            }
        ]
    }

    ```

2. Inclua o Coletor no `DiagnosticMonitorConfiguration` ao adicionar a linha a seguir no `DiagnosticMonitorConfiguration` do `WadCfg` (antes da declaração de `EtwProviders`):

    ```json
    "sinks": "applicationInsights"
    ```

Nos dois snippets de código anteriores, o nome "applicationInsights" era usado para descrever o coletor. Isso não é um requisito e, enquanto o nome do coletor estiver incluído em "coletores", você poderá definir o nome para qualquer cadeia de caracteres.

Atualmente, os logs do cluster aparecem como **rastreios** no visualizador de log do Application Insights. Como a maioria dos rastreamentos originados da plataforma é de nível "Informativo", você também pode considerar alterar a configuração do coletor para enviar apenas logs do tipo "Crítico" ou "Erro." Isso pode ser feito adicionando "Canais" ao seu coletor, conforme demonstrado [neste artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Se você usar uma chave do Application Insights incorreta no portal ou no modelo do Resource Manager, será necessário alterar manualmente a chave e atualizá-la / reimplementá-la.

### <a name="configuring-application-insights-with-eventflow"></a>Configurar o Application Insights com EventFlow

Se você estiver utilizando EventFlow para agregar eventos, certifique-se de importar o pacote `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights`NuGet. O código a seguir é necessário na seção *saídas* do *eventFlowConfig.json*:

```json
"outputs": [
    {
        "type": "ApplicationInsights",
        "instrumentationKey": "***ADD INSTRUMENTATION KEY HERE***"
    }
]
```

Certifique-se de fazer as alterações necessárias nos filtros, assim como incluir quaisquer outras entradas (em conjunto com os respectivos pacotes NuGet).

## <a name="application-insights-sdk"></a>SDK do Application Insights

É recomendável usar EventFlow e WAD como soluções de agregação porque permitem uma abordagem mais modular para diagnósticos e monitoramento, ou seja, se você quiser alterar as saídas do EventFlow, isso não exigirá nenhuma alteração na instrumentação real, apenas uma simples modificação no arquivo de configuração. Se, no entanto, você decidir investir no uso do Application Insights e provavelmente não mudar para uma plataforma diferente, deverá procurar usar o novo SDK do Application Insights para agregar eventos e enviá-los para o Application Insights. Isso significa que você não precisará mais configurar o EventFlow para enviar seus dados para o Application Insights, mas, em vez disso, instalará o pacote Service Fabric NuGet do ApplicationInsight. Detalhes sobre o pacote podem ser encontrados [aqui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[O suporte do Application Insights para micros serviços e contêineres](https://azure.microsoft.com/blog/app-insights-microservices/) mostra alguns dos novos recursos que estão sendo trabalhados (atualmente ainda em beta), que permitem que você tenha opções de monitoramento mais avançadas com o Application Insights. Isso inclui o acompanhamento de dependência (utilizado na construção de um AppMap de todos os seus serviços e aplicativos em um cluster e a comunicação entre eles) e uma melhor correlação de rastreamentos provenientes de seus serviços (ajuda a identificar melhor um problema no fluxo de trabalho de um aplicativo ou serviço).

Se você estiver desenvolvendo no .NET e provavelmente usará alguns dos modelos de programação do Service Fabric e estiver disposto a usar o Application Insights como sua plataforma para visualizar e analisar dados de eventos e logs, recomendamos que você acesse a rota do SDK do Application Insights como seu fluxo de trabalho de monitoramento e diagnóstico. Leia [este](../application-insights/app-insights-asp-net-more.md) e [este](../application-insights/app-insights-asp-net-trace-logs.md) para começar a usar o Application Insights para coletar e exibir seus registros.

## <a name="navigating-the-application-insights-resource-in-azure-portal"></a>Navegando no recurso Application Insights no portal do Azure

Depois de configurar o Application Insights como uma saída para seus eventos e registros, as informações devem começar a aparecer em seu recurso do Application Insights em alguns minutos. Navegue até o recurso Application Insights, que o levará ao painel de recursos do Application Insights. Clique em **Pesquisar** na barra de tarefas do Application Insights para ver os últimos rastreamentos recebidos e para filtrá-los.

*Metrics Explorer* é uma ferramenta útil para criar painéis personalizados com base em métricas que seus aplicativos, serviços e clusters podem estar reportando. Consulte [Exploring Metrics no Application Insights](../application-insights/app-insights-metrics-explorer.md) para configurar alguns gráficos com base nos dados que você está coletando.

Ao clicar em **Análise** você será direcionado para o portal de Análise do Application Insights, onde poderá consultar eventos e rastreamentos com maior escopo e opcionalidade. Leia mais sobre isso em [Análise no Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar alertas no AI](../application-insights/app-insights-alerts.md) para ser notificado sobre mudanças no desempenho ou uso
* [Detecção inteligente no Application Insights](../application-insights/app-insights-proactive-diagnostics.md) realiza uma análise proativa da telemetria enviada ao Application Insights para avisá-lo de possíveis problemas de desempenho
