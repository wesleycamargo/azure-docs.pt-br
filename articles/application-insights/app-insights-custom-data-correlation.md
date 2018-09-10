---
title: Azure Application Insights | Microsoft Docs
description: ''
services: application-insights
documentationcenter: ''
author: eternovsky
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.date: 08/08/2018
ms.reviewer: mbullwin
ms.author: Evgeny.Ternovsky
ms.openlocfilehash: 31e37efc1aad3d355bdd8391535f317ec137f5d7
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/16/2018
ms.locfileid: "40161536"
---
# <a name="correlating-application-insights-data-with-custom-data-sources"></a>Correlacionando os dados do Application Insights com fontes de dados personalizadas

O Application Insights coleta vários tipos de dados diferentes: exceções, rastreamentos, exibições de página e outros. Embora isso geralmente seja suficiente para investigar o desempenho, a confiabilidade e o uso do aplicativo, há casos em que é útil correlacionar os dados armazenados no Application Insights com outros conjuntos de dados totalmente personalizados.

Algumas situações em que é recomendável ter dados personalizados incluem:

- Tabelas de pesquisa ou enriquecimento de dados: por exemplo, complementar um nome do servidor com o proprietário do servidor e o local do laboratório no qual ele pode ser encontrado 
- Correlação com fontes de dados não pertencentes ao Application Insights: por exemplo, correlacionar os dados sobre uma compra em uma webstore com as informações do serviço de atendimento de compra para determinar a precisão das estimativas do tempo de entrega 
- Dados totalmente personalizados: muitos de nossos clientes adoram a linguagem de consulta e o desempenho da plataforma de dados do Log Analytics que dá suporte ao Application Insights e desejam usá-la para consultar dados não relacionados ao Application Insights. Por exemplo, para rastrear o desempenho do painel solar como parte de uma instalação de casa inteligente, conforme descrito [aqui]( http://blogs.catapultsystems.com/cfuller/archive/2017/10/04/using-log-analytics-and-a-special-guest-to-forecast-electricity-generation/).

## <a name="how-to-correlate-custom-data-with-application-insights-data"></a>Como correlacionar dados personalizados com os dados do Application Insights 

Como o Application Insights tem o suporte da eficiente plataforma de dados do Log Analytics, podemos usar toda a potência do Log Analytics para ingerir os dados. Em seguida, escreveremos consultas usando o operador de “junção”, que correlacionará esses dados personalizados com os dados disponíveis para nós no Log Analytics. 

## <a name="ingesting-data"></a>Ingerindo dados

Nesta seção, examinaremos como inserir os dados no Log Analytics.

Caso não tenha um, provisione um novo espaço de trabalho do Log Analytics seguindo [estas instruções]( https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-collect-azurevm) e incluindo a etapa “criar um espaço de trabalho”.

Para começar a enviar dados para o Log Analytics. Existem várias opções:

- Para um mecanismo síncrono, você pode chamar diretamente a [API do coletor de dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api) ou usar nosso conector de Aplicativo Lógico – basta procurar “Azure Log Analytics” e selecionar a opção “Enviar Dados”:

 ![Captura de tela de escolha e ação](./media/app-insights-custom-data-correlation/01-logic-app-connector.png)  

- Para uma opção assíncrona, use a API do Coletor de Dados para criar um pipeline de processamento. Confira [este artigo](https://docs.microsoft.com/azure/log-analytics/log-analytics-create-pipeline-datacollector-api) para obter detalhes.

## <a name="correlating-data"></a>Correlacionando dados

O Application Insights se baseia na plataforma de dados do Log Analytics. Portanto, podemos usar [junções entre recursos](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-cross-workspace-search) para correlacionar os dados ingeridos no Log Analytics com nossos dados do Application Insights.

Por exemplo, podemos ingerir nosso inventário de laboratório e os locais em uma tabela chamada “LabLocations_CL” em um espaço de trabalho do Log Analytics chamado “myLA”. Se, em seguida, desejarmos examinar nossas solicitações rastreadas no aplicativo do Application Insights chamado “myAI” e correlacionar os nomes dos computadores que atenderam às solicitações para os locais desses computadores armazenados na tabela personalizada mencionada anteriormente, poderemos executar a seguinte consulta no contexto do Application Insights ou do Log Analytics:

```
app('myAI').requests
| join kind= leftouter (
    workspace('myLA').LabLocations_CL
    | project Computer_S, Owner_S, Lab_S
) on $left.cloud_RoleInstance == $right.Computer
```

## <a name="next-steps"></a>Próximas etapas

- Veja a referência da [API do Coletor de Dados](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-collector-api).
- Para obter mais informações sobre [junções entre recursos](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-cross-workspace-search).
