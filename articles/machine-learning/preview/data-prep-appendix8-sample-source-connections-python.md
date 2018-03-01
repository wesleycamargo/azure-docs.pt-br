---
title: "Conexões de dados de origem adicionais de exemplo possíveis com a preparação de dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece um conjunto de exemplos de conexões de fonte de dados possíveis com a preparação de dados do Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 66c356d6d42254e7443b645bff3393daca67012b
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="sample-of-custom-source-connections-python"></a>Exemplo de conexões origem personalizada (Python) 
Antes de ler este apêndice, leia [Visão geral da extensibilidade do Python](data-prep-python-extensibility-overview.md).

## <a name="load-data-from-dataworld"></a>Carregar dados de data.world

### <a name="prerequisites"></a>pré-requisitos

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

Crie uma transformação Transformar Fluxo de Dados (Script). Em seguida, use o script a seguir para carregar os dados de data.world.

```python
#paths = df['Path'].tolist()

import datadotworld as dw

#Load the dataset.
lds = dw.load_dataset('data-society/the-simpsons-by-the-data')

#Load specific data frame from the dataset.
df = lds.dataframes['simpsons_episodes']

```
## <a name="azure-cosmos-db-as-a-data-source-connection"></a>Azure Cosmos DB como uma conexão de fonte de dados
Para obter um exemplo do Azure Cosmos DB como uma conexão de dados, leia [Carregar o Azure Cosmos DB como uma conexão de dados de origem](data-prep-load-azure-cosmos-db.md)
