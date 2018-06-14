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
ms.openlocfilehash: 29adf362fdacdb793af071fa6d7bd59214536374
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34207746"
---
# <a name="event-analysis-and-visualization-with-application-insights"></a>Visualização e análise de eventos com o Application Insights

O Azure Application Insights é uma plataforma extensível para monitoramento e diagnóstico de aplicativos. Inclui uma poderosa ferramenta de análise e consulta, visualizações e dashboard personalizável e outras opções, incluindo alertas automatizados. É a plataforma recomendada para monitoramento e diagnóstico para aplicativos e serviços do Service Fabric. Este artigo ajuda a resolver as seguintes perguntas comuns

* Como saber o que está acontecendo no aplicativo e serviços e coletar telemetria
* Como fazer para solucionar problemas do aplicativo, especialmente os serviços que comunicam-se uns com os outros
* Como fazer para obter métrica sobre o desempenho dos serviços, por exemplo, tempo de carregamento da página, solicitações http

O objetivo deste artigo é mostrar como obter insights e solucionar problemas no App Insights. Se você quiser saber mais sobre como definir e configurar o AI com Service Fabric, confira este [tutorial](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="monitoring-in-app-insights"></a>Monitoramento em App Insights

O Application Insights tem uma experiência avançada ao usar o Service Fabric. Na página de visão geral, o AI fornece informações importantes sobre o serviço, como o tempo de resposta e o número de solicitações processadas. Ao clicar no botão "Pesquisar" na parte superior, é possível ver uma lista de solicitações recentes no aplicativo. Além disso, você poderá ver solicitações com falha e diagnosticar quais erros podem ter ocorrido.

![Visão geral do AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-overview.png)

No painel direito da imagem anterior, há dois tipos principais de entradas na lista: solicitações e eventos. Solicitações são chamadas feitas à API do aplicativo por meio de solicitações HTTP nesse caso e os eventos são eventos personalizados, que funcionam como telemetria que você pode adicionar em qualquer parte do código. É possível explorar ainda mais a instrumentação dos aplicativos em [API do Application Insights para métricas e eventos personalizados](../application-insights/app-insights-api-custom-events-metrics.md). Clicar em uma solicitação exibirá detalhes adicionais, conforme mostrado na imagem a seguir, incluindo dados específicos do Service Fabric, que são coletados no pacote nuget do Service Fabric do AI. Essas informações são úteis para solucionar problemas e saber qual é o estado do aplicativo e todas essas informações podem ser pesquisadas no Application Insights

![Detalhes da solicitação do AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-request-details.png)

O Application Insights tem uma exibição designada para consultar todos os dados que chegam. Clique em "Metrics Explorer" na parte superior da página Visão Geral para navegar até o Portal do AI. Aqui, é possível executar consultas em eventos personalizados mencionados anteriormente, solicitações, exceções, contadores de desempenho e outras métricas usando a linguagem de consulta do Kusto. O exemplo a seguir mostra todas as solicitações na última 1 hora.

![Detalhes da solicitação do AI](media/service-fabric-diagnostics-event-analysis-appinsights/ai-metrics-explorer.png)

Para explorar ainda mais os recursos do Portal do App Insights, acesse a [documentação do Portal do Application Insights](../application-insights/app-insights-dashboards.md).

### <a name="configuring-ai-with-wad"></a>Configurar o AI com WAD

>[!NOTE]
>No momento, isso só é aplicável a clusters do Windows.

Há duas maneiras primárias de enviar dados do WAD para o AI do Azure, o que é alcançado pela adição de um coletor do AI para a configuração WAD, conforme detalhado [neste artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

#### <a name="add-an-ai-instrumentation-key-when-creating-a-cluster-in-azure-portal"></a>Adicione uma Chave de Instrumentação do AI ao criar um cluster no Portal do Azure

![Adicionar uma AIKey](media/service-fabric-diagnostics-event-analysis-appinsights/azure-enable-diagnostics.png)

Ao criar um cluster, se o Diagnóstico estiver ativado em "On", um campo opcional para inserir uma chave de Instrumentação do Application Insights será exibido. Se você colar a chave do AI aqui, o coletor da AI será configurado automaticamente no modelo do Resource Manager usado para implantar o cluster.

#### <a name="add-the-ai-sink-to-the-resource-manager-template"></a>Adicione o coletor do AI ao modelo do Resource Manager

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

Nos dois trechos de código anteriores, o nome "applicationInsights" era usado para descrever o coletor. Isso não é um requisito e, enquanto o nome do coletor estiver incluído em "coletores", você poderá definir o nome para qualquer cadeia de caracteres.

Atualmente, os logs do cluster aparecem como **rastreamentos** no visualizador de log do AI. Como a maioria dos rastreamentos originados da plataforma é de nível "Informativo", você também pode considerar alterar a configuração do coletor para enviar apenas logs do tipo "Crítico" ou "Erro." Isso pode ser feito adicionando "Canais" ao seu coletor, conforme demonstrado [neste artigo](../monitoring-and-diagnostics/azure-diagnostics-configure-application-insights.md).

>[!NOTE]
>Se você usar uma chave do AI incorreta no portal ou no modelo do Resource Manager, será necessário alterar manualmente a chave e atualizá-la/reimplantá-la.

### <a name="configuring-ai-with-eventflow"></a>Configurar AI com EventFlow

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

## <a name="aisdk"></a>AI.SDK

É recomendável usar EventFlow e WAD como soluções de agregação porque permitem uma abordagem mais modular para diagnósticos e monitoramento, ou seja, se você quiser alterar as saídas do EventFlow, isso não exigirá nenhuma alteração na instrumentação real, apenas uma simples modificação no arquivo de configuração. No entanto, se você decidir investir na utilização do Application Insights e não é provável que mude para uma plataforma diferente, será necessário procurar usar o novo SDK do AI para agregar eventos e enviá-los ao AI. Isso significa que não será mais necessário configurar o EventFlow para enviar os dados para o AI, mas, em vez disso, irá instalar o pacote do NuGet Service Fabric do Application Insight. Detalhes sobre o pacote podem ser encontrados [aqui](https://github.com/Microsoft/ApplicationInsights-ServiceFabric).

[O suporte do Application Insights para Microsserviços e Contêineres](https://azure.microsoft.com/blog/app-insights-microservices/) apresenta alguns dos novos recursos que estão sendo trabalhados (atualmente ainda em versão beta), os quais permitem que você tenha opções de monitoramento prontas para uso com o AI. Isso inclui o acompanhamento de dependência (utilizado na construção de um AppMap de todos os seus serviços e aplicativos em um cluster e a comunicação entre eles) e uma melhor correlação de rastreamentos provenientes de seus serviços (ajuda a identificar melhor um problema no fluxo de trabalho de um aplicativo ou serviço).

Se você estiver desenvolvendo em .NET e provavelmente estará usando alguns dos modelos de programação do Service Fabric, e disposto a utilizar o AI como sua plataforma para visualizar e analisar dados de eventos e logs, então, é recomendável que você vá via a rota SDK do AI como seu fluxo de trabalho de diagnóstico e monitoramento. Leia [este](../application-insights/app-insights-asp-net-more.md) e [este](../application-insights/app-insights-asp-net-trace-logs.md) para começar a utilizar o AI para coletar e exibir seus logs.

## <a name="navigating-the-ai-resource-in-azure-portal"></a>Navegar pelo recurso do AI no Portal do Azure

Depois de configurar o AI como saída para seus eventos e logs, as informações deverão começar a aparecer no recurso do AI em alguns minutos. Navegue até o recurso do AI, o qual o direcionará para o dashboard de recurso do AI. Clique em **Pesquisar** na barra de tarefas do AI para visualizar os últimos rastreamentos recebidos e poder filtrar através deles.

*Metrics Explorer* é uma ferramenta útil para criar painéis personalizados com base em métricas que seus aplicativos, serviços e clusters podem estar reportando. Consulte [Exploring Metrics no Application Insights](../application-insights/app-insights-metrics-explorer.md) para configurar alguns gráficos com base nos dados que você está coletando.

Ao clicar em **Análise** você será direcionado para o portal de Análise do Application Insights, onde poderá consultar eventos e rastreamentos com maior escopo e opcionalidade. Leia mais sobre isso em [Análise no Application Insights](../application-insights/app-insights-analytics.md).

## <a name="next-steps"></a>Próximas etapas

* [Configurar alertas no AI](../application-insights/app-insights-alerts.md) para ser notificado sobre mudanças no desempenho ou uso
* [Detecção Inteligente no Application Insights](../application-insights/app-insights-proactive-diagnostics.md) realiza uma análise pró-ativa da telemetria enviada ao AI para avisá-lo sobre possíveis problemas de desempenho
