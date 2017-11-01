---
title: "Guia detalhado de como usar a API de execução da Preparação de Dados do Azure Machine Learning | Microsoft Docs"
description: "Este documento fornece detalhes sobre como executar pacotes de Fontes de Dados e de Preparação de Dados previamente projetados"
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
ms.date: 09/12/2017
ms.openlocfilehash: 4a0e4bd58ffa4bc7062ee4844a090be43047e8a1
ms.sourcegitcommit: 2d1153d625a7318d7b12a6493f5a2122a16052e0
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/20/2017
---
# <a name="execute-data-sources-and-data-preparations-packages-from-python"></a>Executar Fontes de Dados e pacotes de Preparação de Dados do Python

Para usar um pacote de Fontes de Dados ou de Preparação de Dados do Azure Machine Learning no Python:

1. Navegue até a guia **Dados** do projeto.

2. Clique com o botão direito do mouse na fonte apropriada.

3. Escolha **Gerar Arquivo de Código de Acesso a Dados**.

Essa ação cria um script de Python curto que executa o pacote e retorna um dataframe.

## <a name="data-sources"></a>Fontes de dados

O módulo `azureml.dataprep.datasource` contém uma única função para executar uma fonte de dados e retornar um dataframe: `load_datasource(path, secrets=None, spark=None)`.
- `path` é o caminho para a fonte de dados (arquivo .dsource).
- `secrets` é um dicionário opcional que mapeia as chaves para os segredos.
- `spark` é um booliano opcional que especifica se um dataframe Spark ou Pandas deve ser retornado. Por padrão, o Azure Machine Learning Workbench determina qual tipo de dataframe será retornado no tempo de execução com base no contexto.

## <a name="data-preparations-packages"></a>Pacotes de Preparação de Dados

O módulo `azureml.dataprep.package` contém três funções que executam um fluxo de dados de um pacote de Preparação de Dados.

### <a name="execution-functions"></a>Funções de execução

- `submit(package_path, dataflow_idx=0, secrets=None, spark=None)` envia o fluxo de dados especificado para execução, mas não retorna um dataframe.
- `run(package_path, dataflow_idx=0, secrets=None, spark=None)` executa o fluxo de dados especificado e retorna os resultados como um dataframe.
- `run_on_data(user_config, package_path, dataflow_idx=0, secrets=None, spark=None)` executa o fluxo de dados especificado com base em uma fonte de dados na memória e retorna os resultados como um dataframe. O argumento `user_config` é um dicionário que mapeia o caminho absoluto de uma fonte de dados (arquivo .dsource) para uma fonte de dados na memória, representada como uma lista de listas.

### <a name="common-arguments"></a>Argumentos comuns

- `package_path` é o caminho para o pacote de Preparação de Dados (arquivo .dprep).
- `dataflow_idx` é o índice com base em zero do qual o fluxo de dados no pacote é executado. Se o fluxo de dados especificado fizer referência a outros fluxos ou fontes de dados, eles também serão executados.
- `secrets` é um dicionário opcional que mapeia as chaves para os segredos.
- `spark` é um booliano opcional que especifica se um dataframe Spark ou Pandas deve ser retornado. Por padrão, o Workbench determina qual tipo de dataframe será retornado no tempo de execução com base no contexto.
