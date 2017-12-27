---
title: "Introdução ao Stream Analytics | Microsoft Docs"
description: "Saiba mais sobre o Stream Analytics, um serviço de nuvem gerenciado que ajuda a analisar dados de streaming de Internet das coisas (IoT) em tempo real."
keywords: "análise como um serviço, serviços gerenciados, processamento de fluxo, análise de fluxo, o que é Stream Analytics"
services: stream-analytics
documentationcenter: 
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 10/17/2017
ms.author: samacha
ms.openlocfilehash: 5747f2f1d3eed3905e0ae3123dab74287beccf66
ms.sourcegitcommit: c87e036fe898318487ea8df31b13b328985ce0e1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/19/2017
---
# <a name="what-is-stream-analytics"></a>O que é o Stream Analytics?

O Azure Stream Analytics é um mecanismo de processamento de eventos gerenciado para configurar cálculos de análise em tempo real no fluxo de dados. Os dados podem vir de dispositivos, sensores, sites, feeds de mídia social, aplicativos, sistemas de infraestrutura e muito mais. 

Use o Stream Analytics para examinar grandes volumes de streaming de dados de dispositivos ou processos, extrair informações do fluxo de dados, identificar padrões, tendências e relações. Use esses padrões para disparar outros processos ou ações, como alertas, fluxos de trabalho de automação, informações de feed para uma ferramenta de relatório ou armazená-los para investigação posterior. 

Alguns exemplos:

* Análise e alertas de comercialização de estoque.
* Detecção de fraudes, dados e identificar as proteções. 
* Análise de sensor e atuador interna.
* Análise de sequência de cliques da Web.

## <a name="how-does-stream-analytics-work"></a>Como funciona o Stream Analytics?

Este diagrama ilustra o pipeline do Stream Analytics, mostrando como os dados são consumidos, analisados e enviados para apresentação ou ação. 

![Pipeline do Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

O Stream Analytics começa com uma fonte de dados de streaming. Os dados podem ser incluídos no Azure vindos de um dispositivo usando um hub de eventos do Azure ou um hub IoT. Os dados também podem ser extraídos de um repositório de dados, como o Armazenamento de Blobs do Azure. 

Para examinar o fluxo, crie um *trabalho* do Stream Analytics que especifique de onde vêm os dados. O trabalho também especifica uma *transformação*; como procurar dados, padrões ou relações. Nesta tarefa, o Stream Analytics dá suporte a uma linguagem de consulta do tipo SQL para filtrar, classificar, agregar e associar dados de streaming em um período de tempo.

Por fim, o trabalho especifica uma saída para os dados transformados. Você controla o que fazer em resposta às informações que você analisou. Por exemplo, em resposta à análise, você pode:

* Envie um comando para alterar as configurações do dispositivo. 
* Envie dados para uma fila monitorada para outras ações baseadas nas descobertas. 
* Envie dados para um painel do Power BI.
* Envie dados para armazenamento, como o Data Lake Store, o Banco de Dados SQL do Azure ou o Armazenamento de Blobs ou de Tabelas do Azure.

Você pode ajustar o número de eventos processados por segundo enquanto o trabalho está em execução. Você também pode gerar logs de diagnóstico para solução de problemas.

## <a name="key-capabilities-and-benefits"></a>Principais recursos e benefícios

O Stream Analytics foi projetado para ser fácil de usar, flexível e escalonável para qualquer tamanho de trabalho.

### <a name="connect-inputs-and-outputs"></a>Conectar entradas e saídas

O Stream Analytics se conecta diretamente a [Hubs de Eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e a [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) para a ingestão de fluxo, e ao [serviço Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) para ingerir dados históricos. Combine dados de hubs de eventos com o Stream Analytics com outras fontes de dados e mecanismos de processamento. A entrada de trabalho também pode incluir dados de referência (dados estáticos ou alterados lentamente). Você pode adicionar dados de streaming a esses dados de referência para executar operações de pesquisa da mesma maneira que faria com consultas a banco de dados.

Rotear a saída de trabalho do Stream Analytics em muitas direções. Grave em um armazenamento como o Blob do Azure, Banco de Dados SQL do Azure, Azure Data Lake Sbore ou Azure Cosmos DB. A partir daí, você pode executar a análise de lote com o Azure HDInsight. Ou envie a saída para outro serviço, para ser consumido por outro processo, como hubs de eventos, Barramento de Serviço do Azure, filas, ou para visualização no Power BI.

### <a name="simple-to-use"></a>Simples de usar

Para definir as transformações, use uma [linguagem de consulta do Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) simples e declarativa que lhe permite criar análises sofisticadas sem programação. A linguagem de consulta usa o fluxo de dados como entrada. Você pode filtrar e classificar os dados, agregar valores, executar cálculos, unir dados (dentro de um fluxo ou em dados de referência) e usar as funções geoespaciais. Você pode editar consultas no portal, usando o IntelliSense e a verificação de sintaxe, e pode testar consultas usando dados de exemplo que podem ser extraídos do fluxo em tempo real.

### <a name="extensible-query-language"></a>Linguagem de consulta extensível

Você pode estender os recursos da linguagem de consulta definindo e chamando funções adicionais. Você pode definir as chamadas de função no serviço Azure Machine Learning para tirar proveito das soluções do Azure Machine Learning. Você também pode integrar UDFs (funções do definidas pelo usuário) JavaScript para executar cálculos complexos como parte de uma consulta do Stream Analytics.

### <a name="scalable"></a>Escalonável

O Stream Analytics pode lidar com até 1 GB de dados de entrada por segundo. A Integração com [Hubs de eventos do Azure](https://azure.microsoft.com/services/event-hubs/) e [Hub IoT do Azure](https://azure.microsoft.com/services/iot-hub/) permite que a solução ingira milhões de eventos por segundo, provenientes de dispositivos conectados, cliques em fluxos e arquivos de log, para citar alguns. Usando o recurso de partição dos hubs de eventos, você pode dividir os cálculos em etapas lógicas, cada um com a capacidade de ser mais particionado para aumentar a escalabilidade.

### <a name="low-cost"></a>Baixo custo

Como um serviço de nuvem, o Stream Analytics é otimizado para que você possa economizar custos. É pago com base no uso de uma unidade de streaming e na quantidade de dados processados pelo sistema. O uso é obtido com base no volume de eventos processados e na capacidade de computação provisionada no cluster do trabalho.

### <a name="reliable"></a>Confiável

Como um serviço gerenciado, o Stream Analytics ajuda a evitar a perda de dados e fornece continuidade dos negócios. Se ocorrerem falhas, o serviço fornecerá recursos internos de recuperação. Com a capacidade de manter o estado internamente, o serviço fornece resultados reproduzíveis garantindo que é possível arquivar eventos e aplicar novamente o processamento no futuro, sempre obtendo os mesmos resultados. Assim, você pode voltar no tempo e investigar cálculos ao fazer análise de causas-raiz, análises hipotéticas, etc.

## <a name="next-steps"></a>Próximas etapas

* Comece [experimentando entradas e consultas de dispositivos IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Crie uma [solução Stream Analytics de ponta a ponta](stream-analytics-real-time-fraud-detection.md) que examina os metadados de telefone para procurar chamadas fraudulentas.
* Encontre respostas para suas perguntas do Stream Analytics no [fórum do Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

