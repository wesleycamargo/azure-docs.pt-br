---
title: Invocar programas Spark do Azure Data Factory
description: Aprenda a invocar programas Spark de uma Azure Data Factory usando a atividade MapReduce.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: fd98931c-cab5-4d66-97cb-4c947861255c
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 2af5d275bb331101b370e4a12043e27b6cdf5b68
ms.lasthandoff: 03/15/2017


---
# <a name="invoke-spark-programs-from-data-factory"></a>Invocar Programas Spark pelo Data Factory
> [!div class="op_single_selector" title1="Transformation Activities"]
> * [Atividade de Hive](data-factory-hive-activity.md) 
> * [Atividade Pig](data-factory-pig-activity.md)
> * [Atividade MapReduce](data-factory-map-reduce.md)
> * [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
> * [Atividade do Spark](data-factory-spark.md)
> * [Atividade de Execução em Lote do Machine Learning](data-factory-azure-ml-batch-execution-activity.md)
> * [Atividade do Recurso de Atualização do Machine Learning](data-factory-azure-ml-update-resource-activity.md)
> * [Atividade de Procedimento Armazenado](data-factory-stored-proc-activity.md)
> * [Atividade do U-SQL do Data Lake Analytics](data-factory-usql-activity.md)
> * [Atividade Personalizada do .NET](data-factory-use-custom-activities.md)

## <a name="introduction"></a>Introdução
A atividade do HDInsight Spark em um [pipeline](data-factory-create-pipelines.md) do Data Factory executa programas do Spark em [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) cluster HDInsight. Este artigo se baseia no artigo sobre [atividades de transformação de dados](data-factory-data-transformation-activities.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas.

## <a name="hdinsight-linked-service"></a>Serviço vinculado ao HDInsight
Antes de usar uma atividade Spark em um pipeline do Data Factory, crie um serviço vinculado ao HDInsight (seu próprio). O trecho de JSON a seguir mostra a definição de um serviço vinculado com o HDInsight para apontar ao seu próprio cluster Azure HDInsight Spark.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://MyHdinsightSparkcluster.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> No momento, a atividade do Spark não oferece suporte a clusters Spark que usam o Data Lake Store como armazenamento primário ou o serviço vinculado com o HDInsight sob demanda. 

Para obter detalhes sobre o serviço vinculado ao HDInsight e outros serviços vinculados de computação, confira o artigo [Serviços vinculados de computação do Data Factory](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>Atividade do Spark em JSON
Este é o exemplo de definição JSON de uma atividade do Spark:    

```json
{
    "name": "MySparkActivity",
    "description": "This activity invokes the Spark program",
    "type": "HDInsightSpark",
    "outputs": [
        {
            "name": "PlaceholderDataset"
        }
    ],
    "linkedServiceName": "HDInsightLinkedService",
    "typeProperties": {
        "rootPath": "mycontainer\\myfolder",
        "entryFilePath": "main.py",
        "arguments": [ "arg1", "arg2" ],
        "sparkConfig": {
              "spark.python.worker.memory": "512m"
        }
    }
}
```
A tabela a seguir descreve as propriedades JSON usadas na definição de JSON: 

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| name | Nome da atividade no pipeline. | Sim |
| description | Texto que descreve o que a atividade faz. | Não |
| type | Essa propriedade deve ser definida como HDInsightSpark. | Sim |
| linkedServiceName | Referência a um serviço vinculado ao HDInsight no qual o programa Spark é executado. | Sim |
| rootPath | O contêiner de Blob do Azure e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo à pasta raiz do código/pacote Spark | Sim |
| className | Classe principal de Java/Spark do aplicativo | Não | 
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | Não | 
| proxyUser | A conta de usuário a ser representada para execução do programa Spark | Não | 
| sparkConfig | Propriedades de configuração do Spark | Não | 
| getDebugInfo | Especifica quando os arquivos de log do Spark são copiados no armazenamento do Azure usado pelo cluster HDInsight (ou) especificado por sparkJobLinkedService. Valores permitidos: Nenhum, Sempre ou Falha. Valor padrão: Nenhum. | Não | 
| sparkJobLinkedService | O serviço vinculado ao Armazenamento do Azure que contém o arquivo de trabalho, dependências e os logs do Spark.  Se você não especificar um valor para essa propriedade, o armazenamento associado ao cluster HDInsight será usado. | Não |

## <a name="folder-structure"></a>Estrutura de pastas
A atividade do Spark não oferece suporte a um script em linha, como o fazem as atividades do Pig e do Hive. Os trabalhos do Spark também são mais extensíveis do que trabalhos do Pig/Hive. Para trabalhos do Spark, você pode fornecer várias dependências, como pacotes jar (colocados no CLASSPATH de java), arquivos do python (colocados no PYTHONPATH) e outros arquivos.

Crie a seguinte estrutura de pastas no armazenamento de Blobs do Azure referenciado pelo serviço vinculado ao HDInsight. Depois, carregue os arquivos dependentes nas subpastas apropriadas na pasta raiz, representada por **entryFilePath**. Por exemplo, carregue arquivos do python na subpasta pyFiles e os arquivos jar na subpasta jars da pasta raiz. Em tempo de execução, o serviço Data Factory espera a seguinte estrutura de pastas no Armazenamento de Blobs do Azure:     

| Caminho | Descrição | Obrigatório | Tipo |
| ---- | ----------- | -------- | ---- | 
| .    | O caminho raiz do trabalho do Spark no serviço vinculado ao armazenamento    | Sim | Pasta |
| &lt;definido pelo usuário&gt; | O caminho que aponta para o arquivo de entrada do trabalho do Spark | Sim | Arquivo | 
| ./jars | Todos os arquivos nessa pasta são carregados e colocados no classpath de java do cluster | Não | Pasta |
| ./pyFiles | Todos os arquivos nessa pasta são carregados e colocados no PYTHONPATH do cluster | Não | Pasta |
| ./files | Todos os arquivos nessa pasta são carregados e colocados no diretório de trabalho executor | Não | Pasta |
| ./archives | Todos os arquivos nessa pasta são descompactados | Não | Pasta |
| ./logs | A pasta onde os logs do cluster Spark são armazenados.| Não | Pasta |

Veja um exemplo de um armazenamento que contém dois arquivos de trabalho do Spark no Armazenamento de Blobs do Azure referenciado pelo serviço vinculado ao HDInsight. 

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs    
```

> [!IMPORTANT]
> Para obter uma explicação completa da criação de um pipeline com uma atividade de transformação, confira o artigo [Criar um pipeline para transformar dados](data-factory-build-your-first-pipeline-using-editor.md). 

## <a name="spark-sample-on-github"></a>Amostra do Spark no GitHub
Antes do suporte à Atividade do Spark existir, a solução alternativa para executar programas do Spark do pipeline do Data Factory era usar uma atividade MapReduce. Ainda é possível usar a [Atividade MapReduce](data-factory-map-reduce.md) em um pipeline do Data Factory para executar programas do Spark em seu cluster HDInsight Spark. Recomendamos que você use a atividade Spark em vez de usar a atividade MapReduce. 

O [Spark - amostra de Data Factory no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) mostra como usar a atividade MapReduce para invocar um programa Spark. O programa Spark apenas copia dados de um contêiner de Blob do Azure para outro. 

## <a name="see-also"></a>Consulte também
* [Atividade de Hive](data-factory-hive-activity.md)
* [Atividade Pig](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


