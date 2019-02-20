---
title: Integrando o Azure Data Lake Storage Gen2 a outros Serviços do Azure | Microsoft Docs
description: Entenda como o armazenamento de dados do Azure Data Lake Storage Gen2 se integra a outros serviços do Azure
documentationcenter: ''
services: storage
author: normesta
ms.component: data-lake-storage-gen2
ms.service: storage
ms.devlang: na
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: nitinme
ms.openlocfilehash: 7bc4889403e1d52cfa3b18792a554f0faf605132
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/07/2019
ms.locfileid: "55885496"
---
# <a name="integrate-azure-data-lake-storage-gen2-with-other-azure-services"></a>Integre o Azure Data Lake Storage Gen2 a outros serviços do Azure

É possível usar os serviços do Azure para ingerir, analisar e visualizar dados em sua conta de armazenamento do Azure Data Lake Storage Gen2. Escolha os serviços que atendem melhor a essas tarefas que você está tentando realizar.

## <a name="ingest-data-into-your-data-lake"></a>Ingerir dados no Data Lake

Esses serviços ajudam você a popular o Data Lake com os dados.

### <a name="azure-data-factory"></a>Fábrica de dados do Azure

É possível usar o [Azure Data Factory](https://azure.microsoft.com/services/data-factory/) para ingerir dados dessas fontes:

* Tabelas do Azure
* Bancos de Dados SQL do Azure
* SQL Data Warehouse do Azure
* Azure Storage Blobs
* Bancos de dados locais

Confira [Move data to and from Data Lake Storage Gen2 using Data Factory](../../data-factory/connector-azure-data-lake-store.md) (Mover dados para e do Data Lake Storage Gen2 usando o Data Factory).

## <a name="analyze-and-visualize-data-in-your-data-lake"></a>Analisar e visualizar dados em seu Data Lake

Esses serviços podem usar o Data Lake Storage Gen2 como um ponto de extremidade de armazenamento.

### <a name="azure-hdinsight"></a>Azure HDInsight

É possível provisionar um cluster do [Azure HDInsight](https://azure.microsoft.com/documentation/learning-paths/hdinsight-self-guided-hadoop-training/) que usa o Data Lake Storage Gen2 como o armazenamento em conformidade com HDFS. Para esta versão, para clusters de Hadoop e Storm no Windows e Linux, é possível usar o Data Lake Storage Gen2 somente como um armazenamento adicional. Esses clusters ainda usam o Armazenamento do Azure (WASB) como o armazenamento padrão. No entanto, para clusters de HBase no Windows e Linux, é possível usar o Data Lake Storage Gen2 como o armazenamento padrão e como armazenamento adicional.

Confira [Usar o Azure Data Lake Storage Gen2 com clusters do Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)

### <a name="azure-data-lake-analytics"></a>Análise Azure Data Lake

É possível usar o [Azure Data Lake Analytics](../../data-lake-analytics/data-lake-analytics-overview.md) para trabalhar com Big Data em escala na nuvem. Ele fornece recursos dinamicamente e permite analisar exabytes de dados. O Data Lake Analytics é otimizado para usar o Data Lake Storage Gen2 como uma fonte de dados. 

Confira [Get Started with Data Lake Analytics using Data Lake Storage Gen2](../../data-lake-analytics/data-lake-analytics-get-started-portal.md) (Introdução ao Data Lake Analytics usando o Data Lake Storage Gen2).

## <a name="copy-data-to-other-repositories"></a>Copiar dados para outros repositórios

Use esses serviços para copiar dados do Data Lake e colá-los em outros repositórios.

### <a name="sql-data-warehouse"></a>SQL Data Warehouse

É possível usar o PolyBase para carregar dados do Data Lake Storage Gen2 para o SQL Data Warehouse. Para saber mais, confira [Use Data Lake Storage Gen2 with SQL Data Warehouse](../../sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md) (Usar o Data Lake Storage Gen2 com o SQL Data Warehouse).

## <a name="see-also"></a>Consulte também

* [Visão geral do Azure Data Lake Storage Gen2](data-lake-storage-introduction.md)
* [Usando o Azure Data Lake Storage Gen2 para exigências de Big Data](data-lake-storage-data-scenarios.md)
