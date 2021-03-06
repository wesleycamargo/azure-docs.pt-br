---
title: Acessar recursos de dados nos notebooks Jupyter no Azure
description: Como acessar arquivos, APIs REST, banco de dados e diferentes recursos do Armazenamento do Microsoft Azure de um notebook Jupyter.
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 14a4191612a5d42836ae4be3ff902ca47a6b06d4
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271500"
---
# <a name="access-cloud-data-in-a-notebook"></a>Acessar dados de nuvem em um notebook

Fazer o trabalho interessante em um notebook Jupyter requer dados. Os dados, na verdade, são a alma dos notebooks.

Você pode certamente [importar arquivos de dados em um projeto](work-with-project-data-files.md), mesmo usando comandos como `curl` de dentro de um notebook para fazer download de um arquivo diretamente. No entanto, é provável, que você precise trabalhar com dados mais amplo que estão disponíveis de fontes diferentes de arquivos, como APIs REST, bancos de dados relacionais e armazenamento em nuvem, como tabelas do Azure.

Este artigo descreve brevemente essas opções diferentes. Porque o acesso a dados é melhor visto em ação, você pode encontrar o código executável nos [Exemplos do Microsoft Azure Notebooks - Acesse seus dados](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>APIs REST

Em termos gerais, a enorme quantidade de dados disponíveis da Internet é acessada, não por meio de arquivos, mas por meio das APIs de REST. Felizmente, como uma célula de notebook pode conter qualquer código que você quiser, você pode usar o código para enviar solicitações e receber dados JSON. Em seguida, você pode converter esse JSON em qualquer formato que você deseja usar como um dataframe do Pandas.

Para acessar dados usando uma API REST, use o mesmo código em células de código do bloco de anotações que você usar em qualquer outro aplicativo. A estrutura geral usando a biblioteca de solicitações é da seguinte maneira:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit" : "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Bancos de Dados SQL do Azure

Você pode acessar bancos de dados do SQL Server com a ajuda das bibliotecas pyodbc ou pymssql.

[Usar Python para consultar um banco de dados SQL do Azure](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python) fornece instruções sobre como criar um banco de dados que contém dados do AdventureWorks e mostra como consultar os dados. O mesmo código é mostrado no notebook para esse artigo.

## <a name="azure-storage"></a>Armazenamento do Azure

O Armazenamento do Microsoft Azure fornece vários tipos diferentes de armazenamento não relacional, dependendo do tipo de dados que você tem e como você precisa acessá-lo:

- Armazenamento de Tabelas: fornece armazenamento de baixo custo e de alto volume para dados tabulares, como logs coletados pelo sensor, logs de diagnóstico e assim por diante.
- Armazenamento de Blob: fornece armazenamento de arquivo semelhante para qualquer tipo de dados.

O notebook de exemplo demonstra o trabalho com tabelas e blobs, incluindo como usar uma assinatura de acesso compartilhado para permitir acesso somente leitura aos blobs.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

O Azure Cosmos DB fornece um repositório NoSQL totalmente indexado para documentos JSON). Os artigos a seguir fornecem uma série de maneiras diferentes de trabalhar com o Cosmos DB do Python:

- [Aplicativo de criar uma API de SQL com Python](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Criar um aplicativo Flask com o Azure Cosmos da DB API para MongoDB](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Criar um banco de dados de grafo usando Python e a API do Gremlin](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Compilar um aplicativo Cassandra com o Python e o Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Criar um aplicativo de API de Tabela com Python e Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Ao trabalhar com o Cosmos DB, você pode usar a biblioteca [azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/).

## <a name="other-azure-databases"></a>Outros banco de dados do Azure

O Azure fornece um número de outros tipos de banco de dados que você puder usar. Os artigos a seguir fornecem diretrizes para acessar esses bancos de dados do Python:

- [Banco de Dados do Azure para PostgreSQL: Usar o Python para se conectar e consultar dados](https://docs.microsoft.com/azure/postgresql/connect-python)
- [Início Rápido: Usar o Cache Redis do Azure com Python](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Banco de Dados do Azure para MySQL: Usar o Python para se conectar e consultar dados](https://docs.microsoft.com/azure/mysql/connect-python)
- [Fábrica de dados do Azure](https://azure.microsoft.com/services/data-factory/)
  - [Assistente de cópia para o Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Próximos passos

- [Como: Trabalhar com arquivos de dados do projeto](work-with-project-data-files.md)
