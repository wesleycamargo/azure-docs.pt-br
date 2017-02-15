---
title: "Monitorar uso e estatísticas em um serviço do Azure Search | Microsoft Docs"
description: "Acompanhe o consumo de recursos e o tamanho de índice da Pesquisa do Azure, um serviço de pesquisa de nuvem hospedado do Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 122948de-d29a-426e-88b4-58cbcee4bc23
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 10/29/2016
ms.author: heidist
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 6e40acb01d6297aa66090859533fd48c108a9a88


---
# <a name="monitor-usage-and-query-metrics-in-an-azure-search-service"></a>Monitorar métricas de uso e de estatísticas em um serviço do Azure Search
O Azure Search coleta estatísticas sobre a execução de consultas no nível de serviço, incluindo QPS (consultas por segundo), latência e percentual de consultas descartadas se o volume excede a capacidade. As métricas são visíveis no portal por meio da folha Monitoramento.

   ![Captura de tela da atividade de QPS][5]

Este artigo descreve as métricas do serviço completo. Para obter informações de atividade no nível do índice, habilite a análise de tráfego de pesquisa e use o Power BI para exibir a análise. Visite [Análise de Tráfego de Pesquisa para a Pesquisa do Azure](search-traffic-analytics.md) para começar.

## <a name="view-query-throughput-statistics"></a>Exibir estatísticas de produtividade de consultas
Clique no bloco Monitoramento no painel de serviços para abrir a folha Monitoramento.

   ![Bloco Monitoramento][2]

A atividade das consultas no nível de serviço é registrada levando em consideração a execução, a latência e a limitação das consultas. As métricas são coletadas de maneira contínua, mas pode demorar vários minutos até o portal mostrar as atividades mais recentes. 

Clique em um bloco de métrica para exibir detalhes, adicionar alertas, habilitar diagnóstico ou editar o gráfico.

  ![Comandos na folha Métrica][4]

### <a name="set-up-alerts"></a>Configurar alertas
Na página de detalhes da métrica, você pode configurar alertas para disparar uma notificação por email caso as atividades de execução, de latência ou de limitação das consultas excedam os critérios definidos.

### <a name="enable-diagnostics"></a>Habilitar diagnósticos
Ao ativar o diagnóstico, é possível configurar onde os dados de diagnóstico são armazenados, se as métricas e os logs de operação devem ser incluídos e por quanto tempo os dados devem ser retidos.

### <a name="change-chart-type-and-data-collection-interval"></a>Alterar o tipo de gráfico e o intervalo para a coleta de dados
Para cada métrica, clique em **editar** para alterar a visualização de gráfico de linhas para gráfico de barras ou para modificar o eixo x para abranger um intervalo de tempo diferente.

  ![Configuração do intervalo de tempo][3]

## <a name="view-counts-and-resource-usage-in-the-portal"></a>Exibir contagens e uso de recursos no portal
Acompanhar o crescimento do tamanho dos índices e do documento pode ajudar você a ajustar a capacidade de forma proativa antes de atingir o limite superior que você estabeleceu para seu serviço. 

Para monitorar o uso de recursos, exiba as contagens e as estatísticas do serviço no [portal](https://portal.azure.com). Caso esteja criando uma ferramenta personalizada de administração do serviço, você também poderá obter as informações por meio de programação.

1. Entre no [portal](https://portal.azure.com). 
2. Abra o painel de serviços de seu serviço de Pesquisa do Azure. É possível encontrar blocos do serviço na Home page, ou você pode navegar até o serviço usando Procurar na Barra de navegação rápida. 

A seção Uso inclui um medidor que informa qual parte dos recursos disponíveis estão atualmente em uso. Para obter informações sobre os limites por serviço para índices, documentos e armazenamento, consulte [Limites de serviço](search-limits-quotas-capacity.md).

  ![Bloco Uso][1]

> [!NOTE]
> A captura de tela acima é para o serviço Gratuito, que tem um máximo de uma réplica e uma partição cada, podendo hospedar somente três índices, 10.000 documentos ou 50 MB de dados, o que ocorrer primeiro. Os serviços criados nos tipos de preço Básico ou Standard têm limites de serviço muito maiores. Para obter mais informações sobre como escolher um tipo de preço, consulte [Escolher um tipo de preço ou SKU](search-sku-tier.md).
> 
> 

### <a name="get-index-statistics-using-the-rest-api"></a>Obter estatísticas de índice usando a API REST
A API REST da Pesquisa do Azure e o SDK do .NET fornecem acesso programático às métricas de serviço.  Se você estiver usando [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para carregar um índice do Banco de Dados SQL ou do Banco de Dados de Documentos do Azure, uma API adicional estará disponível para obter os números dos quais você precisa. 

* [Obter estatísticas de índice](https://msdn.microsoft.com/library/azure/dn798942.aspx)
* [Contar documentos](https://msdn.microsoft.com/library/azure/dn798924.aspx)
* [Obter o status do indexador](https://msdn.microsoft.com/library/azure/dn946884.aspx)

## <a name="next-steps"></a>Próximas etapas
Examine [Dimensionar réplicas e partições](search-limits-quotas-capacity.md) para obter diretrizes sobre como balancear a alocação de partições e de réplicas de um serviço existente. 

Visite [Gerenciar o seu serviço do Search no Microsoft Azure](search-manage.md), para obter mais informações sobre a administração do serviço, ou [Desempenho e otimização](search-performance-optimization.md), para obter diretrizes de ajuste.

<!--Image references-->
[1]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[2]: ./media/search-monitor-usage/AzSearch-Monitor-Tile.PNG
[3]: ./media/search-monitor-usage/AzSearch-Monitor-Intervals.PNG
[4]: ./media/search-monitor-usage/AzSearch-Monitor-AlertCmd.PNG
[5]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG








<!--HONumber=Nov16_HO3-->


