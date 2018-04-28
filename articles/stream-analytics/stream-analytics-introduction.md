---
title: Visão geral do Azure Stream Analytics
description: Saiba mais sobre o Stream Analytics, um serviço de nuvem gerenciado que ajuda a analisar dados de streaming de Internet das coisas (IoT) em tempo real.
services: stream-analytics
author: jseb225
ms.author: jeanb
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: overview
ms.workload: data-services
ms.custom: mvc
ms.date: 03/27/2018
ms.openlocfilehash: 4d68ee7f38c317b4ee42f5b40cc4c4196114e159
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2018
---
# <a name="what-is-stream-analytics"></a>O que é o Stream Analytics?

O Azure Stream Analytics é um mecanismo de processamento de eventos que permite examinar grandes volumes de fluxo de dados de dispositivos. Os dados de entrada podem ser de dispositivos, de sensores, de sites da Web, de feeds de mídias sociais, de aplicativos e muito mais. Ele também dá suporte à extração de informações dos fluxos de dados, identificando padrões e relações. Você pode usar esses padrões para disparar outras ações downstream, como alertas ou informações de feed para uma ferramenta de relatórios, ou armazená-lo para uso posterior.

Abaixo vemos alguns exemplos em que o Azure Stream Analytics pode ser usado: 

* Fusão do sensor de IoT (Internet das Coisas) e análise em tempo real na telemetria do dispositivo
* Análise de sequência de cliques/blogs
* Análise geoespacial para gerenciamento de frotas e veículos sem motoristas
* Monitoramento remoto e manutenção preditiva de ativos de alto valor
* Análise em tempo real em dados de pontos de venda para detecção de anomalias e controle de estoque

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

O Azure Stream Analytics inicia com uma fonte de fluxo de dados que são ingeridos pelo Hub de Eventos do Azure, pelo Hub IoT do Azure ou por um armazenamento de dados, como o Armazenamento de Blobs do Azure. Para examinar os fluxos, você deve criar um trabalho do Stream Analytics que especifica a fonte de entrada que está transmitindo os dados. O trabalho também especifica uma consulta de transformação que define como procurar dados, padrões ou relações. A consulta de transformação utiliza uma linguagem de consulta semelhante a SQL que é usada para filtrar, classificar, agregar e unir dados de streaming por determinado período de tempo. Ao executar o trabalho, você pode ajustar as opções de ordenação de eventos e a duração das janelas de tempo na execução de operações de agregação.

Depois de analisar os dados de entrada, você especifica uma saída para os dados transformados e pode controlar o que deve ser feito em resposta às informações analisadas. Por exemplo, você pode executar ações como:

* Envie dados para uma fila monitorada a fim de disparar fluxos de trabalho personalizados downstream.
* Envie dados ao painel do Power BI para visualização em tempo real.
* Arquive dados em outros serviços de armazenamento do Azure.

A imagem a seguir ilustra o pipeline do Stream Analytics. O trabalho do Stream Analytics pode usar todas as entradas e saídas ou um conjunto selecionado. Esta imagem mostra como os dados são enviados ao Stream Analytics, analisados e enviados para outras ações, como armazenamento ou apresentação:

![Pipeline do Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

## <a name="key-capabilities-and-benefits"></a>Principais recursos e benefícios

O Azure Stream Analytics foi projetado para ser fácil de usar, flexível, confiável e escalonável para qualquer tamanho de trabalho. Ele está disponível em vários data centers e em nuvens soberanas. A imagem abaixo ilustra os principais recursos do Azure Stream Analytics:

![Principais recursos do Stream Analytics](./media/stream-analytics-introduction/stream_analytics_key_capabilities.png)

## <a name="ease-of-getting-started"></a>Facilidade de uso do guia de introdução

É fácil começar a usar o Azure Stream Analytics. Bastam alguns cliques para se conectar a várias fontes e coletores e para criar um pipeline de ponta a ponta. O Stream Analytics pode se conectar aos [Hubs de Eventos do Azure](https://docs.microsoft.com/azure/event-hubs/) e ao [Hub IoT do Azure](https://docs.microsoft.com/azure/iot-hub/) para a ingestão de dados de streaming. Ele também pode se conectar ao serviço [Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/storage-introduction) para a ingestão de dados históricos. Ele pode combinar dados de hubs de eventos com outras fontes de dados e mecanismos de processamento. A entrada de trabalho também pode incluir dados de referência que sejam estáticos ou dados com alteração lenta, e você pode unir dados de streaming a esses dados de referência para executar operações de pesquisa.

O Stream Analytics pode rotear saída de trabalho para vários sistemas de armazenamento, como os [Blobs do Azure](https://docs.microsoft.com/azure/storage/storage-introduction), o [Banco de Dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/), o [Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/) ou o [Azure Cosmos DB ](https://docs.microsoft.com/azure/cosmos-db/introduction). Depois de armazenar, você pode executar a análise em lotes com o Azure HDInsight ou enviar a saída para outro serviço, por exemplo, para ser consumido por hubs de eventos, ou para o [Power BI](https://docs.microsoft.com/power-bi/) para visualização em tempo real usando a API de streaming do Power BI.

## <a name="programmer-productivity"></a>Produtividade do programador

O Azure Stream Analytics usa uma linguagem de consulta baseada em SQL simples que foi aumentada com restrições temporais avançadas para analisar os dados em movimento. Para definir as transformações de trabalho, use uma [Linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) simples e declarativa que permite criar consultas temporais e análises complexas usando constructos SQL simples. A linguagem de consulta do Stream Analytics é consistente com a linguagem SQL; basta estar familiarizado com a linguagem SQL para começar a criar trabalhos. Você também pode criar trabalhos usando ferramentas de desenvolvedor, como o Azure PowerShell, [ferramentas do Visual Studio para o Stream Analytics](stream-analytics-tools-for-visual-studio-install.md) ou modelos do Azure Resource Manager. O uso de ferramentas de desenvolvedor permite a você criar consultas de transformação offline e usar o [pipeline CI/CD](stream-analytics-tools-for-visual-studio-cicd.md) para enviar trabalhos ao Azure. 

A linguagem de consulta do Stream Analytics oferece uma ampla variedade de funções para analisar e processar os dados de streaming. Essa linguagem de consulta dá suporte a funções de manipulação de dados e de agregação simples para funções geoespaciais complexas. Você pode editar consultas no portal e testá-las usando os dados de exemplo que são extraídos do fluxo ativo.

Você pode estender os recursos da linguagem de consulta definindo e chamando funções adicionais. Você pode definir as chamadas de função no serviço Azure Machine Learning para tirar proveito das soluções desta e integrar UDFs (funções definidas pelo usuário) JavaScript ou agregações definidas pelo usuário para executar cálculos complexos como parte de uma consulta do Stream Analytics.

## <a name="fully-managed"></a>Totalmente gerenciado 

O Azure Stream Analytics é uma oferta sem servidor (PaaS) totalmente gerenciada no Azure. Isso significa que você não precisa provisionar hardware ou gerenciar clusters para executar seus trabalhos. O Azure Stream Analytics gerencia seu trabalho integralmente, cuidando da configuração de clusters de computação complexos na nuvem e ajustando o desempenho necessário para executar o trabalho. A Integração com os Hubs de Eventos do Azure e o Hub IoT do Azure permite que a solução ingira milhões de eventos por segundo, provenientes de dispositivos conectados, cliques em fluxos e arquivos de log, para citar alguns. Usando o recurso de partição dos hubs de eventos, você pode dividir os cálculos em etapas lógicas, cada um com a capacidade de ser mais particionado para aumentar a escalabilidade.

## <a name="low-total-cost-of-ownership"></a>Custo total de propriedade baixo

Como um serviço de nuvem, o Stream Analytics é otimizado para que você possa economizar custos. Não há nenhum custo inicial envolvido; você só paga pelas [unidades de streaming que consumir](stream-analytics-streaming-unit-consumption.md) e pela quantidade de dados processados. Não há nenhum compromisso ou provisionamento de cluster necessário. Você pode expandir ou reduzir seus trabalhos de streaming com base nas necessidades do negócio. 

## <a name="reliability"></a>Confiabilidade 

Como um serviço gerenciado, o Stream Analytics garante o processamento de eventos com 99,9% de disponibilidade, ajuda a evitar a perda de dados e fornece continuidade dos negócios. Confira o [SLA do Stream Analytics](https://azure.microsoft.com/support/legal/sla/stream-analytics/v1_0/) para obter mais detalhes. O Stream Analytics pode processar milhões de eventos por segundo e fornecer resultados com baixa latência.
O Stream Analytics garante exatamente o processamento de eventos exatamente uma vez e pelo menos uma entrega de eventos. Ele tem recursos internos de recuperação no caso de falha na entrega de um evento. O Stream Analytics pode manter o estado do seu trabalho internamente; você pode iniciar um trabalho a partir da hora da última saída; e ele fornece resultados reproduzíveis, fornecendo sempre o mesmo resultado. Esse recurso do Stream Analytics permite voltar no tempo e investigar cálculos ao fazer análise de causas-raiz. 

## <a name="performance"></a>Desempenho

O Azure Stream Analytics é otimizado para alto desempenho: ele pode processar dados de streaming e executar cálculos de memória internamente. Ele permite a expansão ou redução de escala para lidar com aplicativos de processamento de eventos complexos e em tempo real. O Stream Analytics dá suporte a desempenho por meio de particionamento. Uma consulta complexa pode ser colocada em paralelo e executada em vários nós de streaming. 

## <a name="next-steps"></a>Próximas etapas

Agora você tem uma visão geral do Azure Stream Analytics. Em seguida, você pode se aprofundar e criar seu primeiro trabalho do Stream Analytics:

* [Criar um trabalho do Stream Analytics usando o portal do Azure](stream-analytics-quick-create-portal.md).
* [Criar um trabalho do Stream Analytics usando o Azure PowerShell](stream-analytics-quick-create-powershell.md).
