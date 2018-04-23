---
title: Conectar-se ao Azure Cosmos DB como uma Fonte de Dados no Azure Machine Learning Workbench | Microsoft Docs
description: Este documento fornece um exemplo sobre como conectar-se ao Azure Cosmos DB por meio Azure Machine Learning Workbench
services: machine-learning
author: cforbe
ms.author: cforbe
manager: mwinkle
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: d36b394a528dc4bc1b6e0a9e0e5dbde728cbee1b
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/19/2018
---
# <a name="connecting-to-azure-cosmos-db-as-a-data-source"></a>Conectando-se ao Azure Cosmos DB como uma fonte de dados
Este artigo contém uma amostra de Python que permite que você se conecte ao Cosmos DB no Azure Machine Learning Workbench.

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Carregar os dados do Azure Cosmos DB na preparação de dados

Crie um novo fluxo de dados baseado em script e então use o script a seguir para carregar os dados do Azure Cosmos DB. 

```python
import pydocumentdb
import pydocumentdb.document_client as document_client

import pandas as pd

config = { 
    'ENDPOINT': '<Endpoint>',
    'MASTERKEY': '<Key>',
    'DOCUMENTDB_DATABASE': '<DBName>',
    'DOCUMENTDB_COLLECTION': '<collectionname>'
};

# Initialize the Python DocumentDB client.
client = document_client.DocumentClient(config['ENDPOINT'], {'masterKey': config['MASTERKEY']})

# Read databases and take first since id should not be duplicated.
db = next((data for data in client.ReadDatabases() if data['id'] == config['DOCUMENTDB_DATABASE']))

# Read collections and take first since id should not be duplicated.
coll = next((coll for coll in client.ReadCollections(db['_self']) if coll['id'] == config['DOCUMENTDB_COLLECTION']))

docs = client.ReadDocuments(coll['_self'])

df = pd.DataFrame(list(docs))
```

## <a name="other-data-source-connections"></a>Outras conexões de fonte de dados
Para obter outros exemplos, leia [Exemplos de conexões de dados de origem adicionais](data-prep-appendix8-sample-source-connections-python.md)
