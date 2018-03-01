---
title: "Guia detalhado de como usar a Operacionalização de Preparações de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece detalhes sobre como executar pacotes de Fontes de Dados e de Preparação de Dados previamente projetados"
services: machine-learning
author: hughz
ms.author: cforbe
manager: mwinkle
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/13/2018
ms.openlocfilehash: 76bf850df5c8f4a93268dd06f7b9254bbd2cfce6
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/21/2018
---
# <a name="data-preparation-operationalization"></a>Operacionalização de preparação dos dados 

## <a name="operationalization-scenario"></a>Cenário de operacionalização

A seguir estão os diferentes cenários de Operacionalização de Preparação de Dados que um usuário pode encontrar.

### <a name="develop-your-model-based-on-training-data"></a>Desenvolver seu modelo com base nos dados de treinamento

Suponha que você pretende criar e implantar um serviço de API de pontuação em tempo real. A primeira etapa é desenvolver um modelo de pontuação com base em um conjunto de dados de treinamento. A preparação de dados importa uma amostra dos seus dados de treinamento como nova fonte de dados. Após a preparação dos dados, o pacote DataPrep contém as etapas de preparação. Usando uma conta de experimentação do AzureML, o usuário pode iterar em um modelo de aprendizado de máquina para gerar rótulos para cada entrada nos dados de treinamento.

Como os recursos dos dados precisam ser atualizados, o usuário retorna ao pacote DataPrep para preparar os dados de uma forma nova e salvar essas etapas. Esse processo de iteração de gerar novos recursos e ajustar seu modelo de aprendizado de máquina continua até que o modelo pontue com precisão os dados de teste. 

### <a name="deploy-your-model-to-the-azure-model-management-service"></a>Implantar o modelo para o Serviço de Gerenciamento de Modelos do Azure

Agora você tem os dados de clientes que você gostaria de pontuar em tempo real. Usando o serviço de Gerenciamento de Modelos do Azure, você pode implantar esse modelo da CLI do Python do AzureML como serviço. O serviço implantado expõe um ponto de extremidade REST para receber dados do cliente em tempo real e retornar rótulos de saída correspondentes de seu modelo treinado.

Para facilitar o uso, o ponto de extremidade do modelo aceita dados em formato JSON. A entrada deve ser uma cadeia de caracteres JSON que representa uma matriz bidimensional de dados que será passada pela transformação ReadJSONLiteral e convertida em uma fonte de dados DataPrep. O pacote de preparação de dados criado durante a fase de experimentação pode, então, ser executado em relação aos dados JSON transmitidos. O dataframe resultante pode ser passado para o modelo treinado para pontuação.

## <a name="read-json-literal-transformation"></a>Transformação Literal de JSON de Leitura

### <a name="description"></a>DESCRIÇÃO

Para fins de operacionalização, a preparação de dados contém uma transformação **ReadJsonLiteral** para executar uma atividade e gerar um Dataframe Pandas ou Spark. Essa transformação usa exclusivamente, como entrada, um pacote de preparação de dados existente e uma fonte de dados JSON. Essa transformação é exposta através da CLI do Python DataPrep.

### <a name="instructions"></a>Instruções

#### <a name="pythoncli"></a>PythonCLI

No Azure Machine Learning Workbench, abra a interface de linha de comando (Arquivo > Abrir prompt de comando).

Neste exemplo, o pacote de preparação de dados TrainingPreparationSteps é usado para preparar os dados e adiciona o recurso de volume para cada entrada.

```
>>> import azureml.dataprep.package as azdp

>>> azdp.run_on_data.__doc__
"Generate a dataframe based on a selected dataflow in a package using in-memory data sources.
'user_config' is expected as a dictionary that maps the absolute path of a dsource file to an in-memory data source represented as a list of lists."

>>> real_time_customer_data = [[1.0, 1.5, 2.5], [2.5, 1.5, 3]]
>>> azdp.run_on_data({ "C:\\TrainingSampleData.dsource": real_time_customer_data}, "C:\\TrainingPreparationSteps.dprep")
    Height   Width   Depth  Volumne
0   1.0       1.5    2.5    3.75
1   2.5       1.5    3.0    11.25
```

`run_on_data()` tem os seguintes parâmetros opcionais
 - `dataflow_idx`: especifique o índice do fluxo de dados desejado para executar no pacote
 - `secrets`: dicionário de armazenamento secreto (chave: secretId, valor: segredo armazenado)
 - `spark`: fornece uma sessão spark para execução spark

#### <a name="input"></a>Entrada

Uma entrada de matriz bidimensional ("matriz de matrizes"). No Python, a entrada deve ser uma lista de listas. Como o JSON não tem um tipo nativo de data, qualquer objeto datetime.datetime Python será convertido em cadeias de datas com formato ISO. Para pontos de extremidade REST, a entrada deve ser uma cadeia literal de JSON que representa uma matriz JSON 2D.

#### <a name="output"></a>Saída

Um DataFrame Pandas ou DataFrame. O tipo de DataFrame é determinado pela estrutura selecionada na configuração de execução (`.runconfig`). O dataframe resultante pode ser passado como entrada para o modelo treinado para pontuação.
