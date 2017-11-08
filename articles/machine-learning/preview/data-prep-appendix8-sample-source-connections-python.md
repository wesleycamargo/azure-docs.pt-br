---
title: "Conexões de dados de origem adicionais de exemplo possíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de conexões de fonte de dados possíveis com a preparação de dados do Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: 9aaf4329b25cb189146949afed89cf15619ba592
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/31/2017
---
# <a name="sample-of-custom-source-connections-python"></a>Exemplo de conexões origem personalizada (Python) 
Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Carregar dados de data.world

### <a name="prerequisites"></a>Pré-requisitos

#### <a name="register-yourself-at-dataworld"></a>Registrar-se em data.world
É necessário um token de API do site data.world.

#### <a name="install-dataworld-library"></a>Instalar a biblioteca data.world

Abra a interface de linha de comando do Azure Machine Learning Workbench ao selecionar **Arquivo** > **Abrir interface de linha de comando**.

```console
pip install git+git://github.com/datadotworld/data.world-py.git
```

Em seguida, execute `dw configure` na linha de comando, que solicita o token. Quando você inserir o token, um diretório .dw/ será criado no diretório inicial e seu token será armazenado lá.

```
API token (obtained at: https://data.world/settings/advanced): <enter API token here>
```
Agora você deve ser capaz de importar as bibliotecas data.world.

#### <a name="load-data-into-data-preparation"></a>Carregar dados na preparação de dados

Crie um novo fluxo de dados baseado em script. Em seguida, use o script a seguir para carregar os dados de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```

## <a name="load-azure-cosmos-db-data-into-data-preparation"></a>Carregar os dados do Azure Cosmos DB na preparação de dados

Crie um novo fluxo de dados baseado em script e então use o script a seguir para carregar os dados do Azure Cosmos DB. (As bibliotecas precisam ser instaladas primeiro. Para saber mais, veja o documento de referência anterior para o qual criamos um link).

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
