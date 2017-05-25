---
title: "Suporte do Application Insights do Azure para várias funções, microsserviços e contêineres | Microsoft Docs"
description: "Monitoramento de aplicativos que consistem em vários componentes ou funções para desempenho e uso."
services: application-insights
documentationcenter: 
author: alancameronwills
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/15/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: c308183ffe6a01f4d4bf6f5817945629cbcedc92
ms.openlocfilehash: dad17277452081427a01d077128a3a137b2190f5
ms.contentlocale: pt-br
ms.lasthandoff: 05/17/2017


---
# <a name="monitor-multi-role-applications-with-application-insights-preview"></a>Como monitorar aplicativos de várias funções com o Application Insights (versão prévia)

Você pode monitorar aplicativos que consistem em vários componentes, funções ou serviços com [Application Insights do Azure](app-insights-overview.md). A integridade das funções e as relações entre eles são exibidas em um mapa de aplicativo único. Você pode rastrear a operações individuais por meio de várias funções com correlação automática de HTTP. Os diagnósticos do contêiner podem ser integrados e correlacionados à telemetria de aplicativos. Use um único recurso do Application Insights para todas as funções do seu aplicativo. 

![Mapa de aplicativo de várias funções](./media/app-insights-monitor-multi-role-apps/app-map.png)

Podemos usar "função" aqui em um sentido mais amplo para significar qualquer componente de aplicativo ou serviço que é criado como um projeto separado. Por exemplo, um aplicativo de negócios típico pode consistir em várias funções que se comunicam por meio de uma API REST. As funções podem ser hospedadas em contêineres como Docker, Service Fabric ou em hosts em nuvem ou no local. 

### <a name="sharing-a-single-application-insights-resource"></a>Compartilhamento um único recurso do Application Insights 

A principal técnica aqui é enviar telemetria de cada função em seu aplicativo para o mesmo recurso do Application Insights, mas usar a propriedade `cloud_RoleName` para distinguir as funções quando necessário. 

Em alguns casos, isso pode não ser apropriado e talvez você prefira usar recursos separados para diferentes grupos de funções. Por exemplo, talvez seja necessário usar recursos diferentes para fins de cobranças ou gerenciamento. O uso de recursos separados significa que você não vê todas as funções exibidas em um mapa de aplicativo único e não pode conferir funções na [Análise](app-insights-analytics.md). Você também precisa configurar os recursos separados.

Com essa limitação, vamos supor no restante desse documento que você quer enviar dados de várias funções para um recurso do Application Insights.

## <a name="configure-multi-role-applications"></a>Configuração de aplicativos de várias funções

Para obter um mapa de aplicativo de várias funções, você cumprir os seguintes requisitos:

* **Instalar a versão de pré-lançamento mais recente** do pacote Application Insights em cada função do aplicativo. 
* **Compartilhar um único recurso do Application Insights** para todas as funções do seu aplicativo.
* **Habilitar o mapa de aplicativo de várias funções** na folha de visualizações.

Configurar cada função do seu aplicativo usando o método apropriado para seu tipo. ([ASP.NET](app-insights-asp-net.md), [Java](app-insights-java-get-started.md), [Node.js](app-insights-nodejs.md).)

### <a name="1-install-the-latest-pre-release-package"></a>1. Instalação do pacote de pré-lançamento mais recente

Como atualizar ou instalar os pacotes do Application Insights no projeto para cada função. Se você estiver usando o Visual Studio:

1. Clique o botão direito do mouse no projeto e selecione **Gerenciar Pacotes Nuget**. 
2. Selecione **Incluir pré-lançamento**.
3. Se pacotes do Application Insights aparecerem nas atualizações, selecione-os. 

    Caso contrário, procure e instale o pacote apropriado:
    
    * Microsoft.ApplicationInsights.WindowsServer
    * Microsoft.ApplicationInsights.ServiceFabric - para funções em execução como executáveis de convidado e executando um aplicativo do Service Fabric em contêineres do Docker
    * Microsoft.ApplicationInsights.ServiceFabric.Native - para serviços confiáveis em aplicativos no Service Fabric
    * Microsoft.ApplicationInsights.Kubernetes para funções em execução no Docker e nos Kubernetes

### <a name="2-share-a-single-application-insights-resource"></a>2. Compartilhamento de um único recurso do Application Insights

* No Visual Studio, clique o botão direito do mouse em um projeto e selecione **Configurar o Application Insights** ou **Application Insights > Configuração**. Para o primeiro projeto, use o assistente para criar um recurso do Application Insights. Para projetos subsequentes, selecione o mesmo recurso.
* Se não houver nenhum menu do Application Insights, configure manualmente:

   1. No [portal do Azure](https://portal,azure.com), abra o recurso do Application Insights já criado para outra função.
   2. Na folha de Visão geral, abra a guia suspensa Essentials e copie a **Chave de instrumentação.**
   3. No seu projeto, abra ApplicationInsights.config e insira: `<InstrumentationKey>your copied key</InstrumentationKey>`

![Copie a chave de instrumentação para o arquivo .config](./media/app-insights-monitor-multi-role-apps/copy-instrumentation-key.png)


### <a name="3-enable-multi-role-application-map"></a>3. Habilitação do mapa de aplicativo de várias funções

No portal do Azure, abra o recurso do seu aplicativo. Na folha de visualizações, habilite o *Mapa de Aplicativo de Várias Funções*.

### <a name="4-enable-docker-metrics-optional"></a>4. Habilitação das métricas de Docker (opcional) 

Se uma função é executada no Docker hospedado em uma VM Windows do Azure, você pode coletar métricas adicionais do contêiner. Insira isso no seu arquivo de configuração de [diagnóstico do Azure](../monitoring-and-diagnostics/azure-diagnostics.md):

```
"DiagnosticMonitorConfiguration": {
        ...
        "sinks": "applicationInsights",
        "DockerSources": {
                "Stats": {
                    "enabled": true,
                    "sampleRate": "PT1M"
                }
            },
        ...
    }
    ...   
    "SinksConfig": {
        "Sink": [{
            "name": "applicationInsights",
            "ApplicationInsights": "<your instrumentation key here>"
        }]
    }
    ...
}

```

## <a name="use-cloudrolename-to-separate-roles"></a>Use cloud_RoleName para funções separadas

A propriedade `cloud_RoleName` está conectada a todas as telemetrias. Ela identifica a função ou o serviço que origina a telemetria. (Não é igual a cloud_RoleInstance, que separa funções idênticas executadas em paralelo em vários processos do servidor ou computadores.)

No portal, você pode filtrar ou segmentar a sua telemetria usando essa propriedade. Neste exemplo, a folha de falhas é filtrada para mostrar apenas as informações do serviço Web do front-end, a filtragem de falhas de back-end da API do CRM:

![Gráfico de métricas segmentado por nome de função de nuvem](./media/app-insights-monitor-multi-role-apps/cloud-role-name.png)

## <a name="trace-operations-between-roles"></a>Operações de rastreamento entre funções

Você pode rastrear as chamadas de um serviço para outro feitas durante o processamento de uma operação individual.


![Mostrar telemetria para operação](./media/app-insights-monitor-multi-role-apps/show-telemetry-for-operation.png)

Clique em uma lista correlacionada de telemetria para esta operação entre o servidor web de front-end e a API de back-end:

![Pesquisas entre funções](./media/app-insights-monitor-multi-role-apps/search-across-components.png)


## <a name="next-steps"></a>Próximas etapas

* [Separação da telemetria de desenvolvimento, teste e produção](app-insights-separate-resources.md)

