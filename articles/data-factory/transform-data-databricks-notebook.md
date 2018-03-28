---
title: Transformar dados com o Notebook do Databricks - Azure | Microsoft Docs
description: Saiba como processar ou transformar dados executando um Notebook do Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: a011c31c5ccf70c379358f2b2c7748011b2fdd44
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/17/2018
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformar dados executando um Notebook do Databricks

A Atividade de Notebook do Azure Databricks em um [pipeline do Data Factory](concepts-pipelines-activities.md) executa um Notebook do Databricks no espaço de trabalho do Azure Databricks. Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Definição de atividade do Notebook Databricks

A seguir está a definição JSON de exemplo de uma Atividade de Notebook do Databricks:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            }
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Propriedades da atividade de Notebook do Databricks

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|DESCRIÇÃO|Obrigatório|
|---|---|---|
|Nome|Nome da atividade no pipeline.|sim|
|Descrição|Texto que descreve o que a atividade faz.|Não |
|Tipo|Para Atividade de Notebook do Databricks, o tipo da atividade é DatabricksNotebook.|sim|
|linkedServiceName|Nome do serviço vinculado do Databricks no qual o Notebook do Databricks executa. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).|sim|
|notebookPath|O caminho absoluto do notebook a ser executado no espaço de trabalho do Databricks. Esse caminho deve começar com uma barra.|sim|
|baseParameters|Uma matriz de pares chave-valor. Parâmetros básicos podem ser utilizados para cada execução de atividade. Se o notebook utilizar um parâmetro que não for especificado, será usado o valor padrão do notebook. Encontre mais informações sobre os parâmetros em [Notebooks do Databricks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Não |
