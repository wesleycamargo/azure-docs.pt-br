---
title: Conectar a diferentes fontes de dados do Azure Databricks | Microsoft Docs
description: Saiba como conectar a diferentes fontes de dados do Azure Databricks.
services: azure-databricks
documentationcenter: ''
author: nitinme
manager: cgronlun
editor: cgronlun
ms.service: azure-databricks
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: nitinme
ms.openlocfilehash: 865313a7c6eabd847529b88ff5fff0b7db438fa5
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/23/2018
ms.locfileid: "30174030"
---
# <a name="connect-to-data-sources-from-azure-databricks"></a>Conectar a fontes de dados do Azure Databricks

Este artigo fornece links para todas as diferentes fontes de dados no Azure que podem ser conectadas ao Azure Databricks. Siga os exemplos nesses links para extrair dados das fontes de dados do Azure (por exemplo, Armazenamento de Blobs, Hubs de Eventos do Azure e etc.) em um cluster do Azure Databricks e executar trabalhos analíticos neles. 

## <a name="prerequisites"></a>pré-requisitos

* É necessário ter um espaço de trabalho do Azure Databricks e um cluster do Spark. Siga as instruções em [Introdução ao Azure Databricks](quickstart-create-databricks-workspace-portal.md).

## <a name="data-sources-for-azure-databricks"></a>Fontes de dados para Azure Databricks

A lista a seguir fornece as fontes de dados no Azure que podem ser utilizadas com Azure Databricks. Para obter uma lista completa de fontes de dados que podem ser utilizadas com Azure Databricks, consulte [Fontes de dados para Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html).

- [Banco de Dados SQL do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/sql-databases.html)

    Esse link fornece a API de DataFrame para conexão com bancos de dados SQL usando JDBC e como controlar o paralelismo de leituras por meio da interface JDBC. Este tópico fornece exemplos detalhados usando a API do Scala, com exemplos abreviados de Python e Spark SQL no final.
- [Repositório Azure Data Lake](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-datalake.html)

    Este link fornece exemplos sobre como usar a entidade de serviço do Azure Active Directory para autenticar com o Data Lake Store. Ele também fornece instruções sobre como acessar os dados no Data Lake Store a partir do Azure Databricks.

- [Armazenamento de Blobs do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/azure-storage.html)

    Esse link fornece exemplos de como acessar diretamente o Armazenamento de Blobs a partir do Azure Databricks usando a chave de acesso ou a SAS para um determinado contêiner. O link também fornece informações sobre como acessar o Armazenamento de Blobs a partir do Azure Databricks usando a API de RDD.

- [Azure Cosmos DB](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/cosmosdb-connector.html)

    Este link fornece instruções sobre como usar o [conector do Apache Spark do Microsoft Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-spark) a partir do Azure Databricks para acessar dados no Azure Cosmos DB.

- [Hubs de eventos do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/eventhubs-connector.html)

    Este link fornece instruções sobre como usar o [conector do Apache Spark dos Hubs de Eventos do Azure](https://github.com/Azure/azure-event-hubs-spark) a partir do Azure Databricks para acessar dados nos Hubs de Eventos do Azure.

- [SQL Data Warehouse do Azure](https://docs.azuredatabricks.net/spark/latest/data-sources/azure/sql-data-warehouse.html)

    Este link fornece instruções sobre como usar o conector do SQL Data Warehouse do Azure para conectar a partir do Azure Databricks.
    

## <a name="next-steps"></a>Próximas etapas

Para saber mais sobre fontes, a partir das quais é possível importar dados para o Azure Databricks, consulte [Fontes de dados para Azure Databricks](https://docs.azuredatabricks.net/spark/latest/data-sources/index.html#).


