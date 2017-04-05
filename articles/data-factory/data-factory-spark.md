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
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 3c7d109ec7fd92859cad4ded7422105e8094485c
ms.lasthandoff: 03/30/2017


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

> [!IMPORTANT]
> Se você for novo no Azure Data Factory, será recomendável que você percorra o tutorial [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md) antes de ler este artigo. Para obter uma visão geral do serviço Data Factory, confira [Introdução ao Azure Data Factory](data-factory-introduction.md). 

## <a name="apache-spark-cluster-in-azure-hdinsight"></a>Cluster do Apache Spark no Azure HDInsight
Primeiro, crie um cluster do Apache Spark no Azure HDInsight seguindo as instruções no tutorial: [Criar cluster do Apache Spark no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). 

## <a name="create-data-factory"></a>Criar um data factory 
Aqui estão as etapas típicas para criar um pipeline do Data Factory com uma Atividade do Spark.  

1. Criar uma fábrica de dados.
2. Crie um serviço vinculado que vincula o cluster do Apache Spark no Azure HDInsight ao seu data factory.
3. No momento, você deve especificar um conjunto de dados de saída para uma atividade mesmo que não exista nenhuma saída sendo produzida. Para criar um conjunto de dados do Blob do Azure, siga as etapas a seguir:
    1. Crie um serviço vinculado que vincula sua conta de Armazenamento do Azure ao data factory.
    2. Crie um conjunto de dados que se refere ao serviço vinculado do Armazenamento do Azure.  
3. Crie um pipeline com atividade de Spark que se refere ao serviço vinculado do Apache HDInsight criado na etapa n. 2. A atividade está configurada com o conjunto de dados que você criou na etapa anterior como um conjunto de dados de saída. O conjunto de dados de saída é o que conduz a agenda (por hora, diariamente, etc.). Portanto, você deve especificar o conjunto de dados de saída mesmo que a atividade não produza efetivamente uma saída.

Para obter instruções passo a passo detalhadas para criação de um data factory, consulte o tutorial: [Criar seu primeiro pipeline](data-factory-build-your-first-pipeline.md). Este tutorial usa uma atividade de Hive com um cluster Hadoop do HDInsight, mas as etapas são semelhantes para o uso de uma atividade Spark com um cluster Spark do HDInsight.   

As seções a seguir fornecem informações sobre entidades de Data Factory para usar o cluster do Apache Spark e atividade do Spark na data factory.   

## <a name="hdinsight-linked-service"></a>Serviço vinculado ao HDInsight
Antes de usar uma atividade Spark em um pipeline do Data Factory, crie um serviço vinculado ao HDInsight (seu próprio). O trecho de JSON a seguir mostra a definição de um serviço vinculado com o HDInsight que aponta para um cluster Spark do Azure HDInsight.   

```json
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<nameofyoursparkcluster>.azurehdinsight.net/",
              "userName": "admin",
              "password": "password",
              "linkedServiceName": "MyHDInsightStoragelinkedService"
        }
    }
}
```

> [!NOTE]
> No momento, a atividade do Spark não oferece suporte a clusters Spark que usam um Azure Data Lake Store como armazenamento primário ou um serviço vinculado com o HDInsight sob demanda. 

Para obter detalhes sobre o serviço vinculado ao HDInsight e outros serviços vinculados de computação, confira o artigo [Serviços vinculados de computação do Data Factory](data-factory-compute-linked-services.md). 

## <a name="spark-activity-json"></a>Atividade do Spark em JSON
Esta é a definição JSON de exemplo de um pipeline com atividade do Spark:    

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    }
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

Observe os seguintes pontos: 
- A propriedade type é definida como HDInsightSpark. 
- O rootPath é definido como adfspark\\pyFiles, em que adfspark é o contêiner de Blob do Azure e pyFiles é a pasta de arquivos nesse contêiner. Neste exemplo, o Armazenamento de Blobs do Azure é aquele que está associado ao cluster Spark. Você pode carregar o arquivo em um Armazenamento do Azure diferente. Se você fizer isso, crie um serviço vinculado do Armazenamento do Azure para vincular essa conta de armazenamento ao data factory. Em seguida, especifique o nome do serviço vinculado como um valor para a propriedade sparkJobLinkedService. Consulte [Propriedades de Atividade Spark](#spark-activity-properties) para obter detalhes sobre essa propriedade e outras propriedades às quais a atividade Spark dá suporte.  
- O entryFilePath é definido como test.py, que é o arquivo Python. 
- Os valores de parâmetros para o programa Spark são passados por meio da propriedade arguments. Neste exemplo, existem dois argumentos: arg1 e arg2. 
- A propriedade getDebugInfo é definida como Always, o que significa que os arquivos de log são gerados sempre (êxito ou falha). 
- A seção sparkConfig especifica uma configuração de ambiente do Python: worker.memory. 
- A seção de saídas tem um conjunto de dados de saída. Você deve especificar um conjunto de dados de saída mesmo que o programa spark não produza nenhuma saída. O conjunto de dados de saída conduz a agenda para o pipeline (por hora, diariamente, etc.).     

As propriedades de tipo (na seção typeProperties) são descritas posteriormente neste artigo na seção [Propriedades de Atividade Spark](#spark-activity-properties). 

Conforme mencionado anteriormente, você deve especificar um conjunto de dados de saída para a atividade, que é o que conduz a agenda para o pipeline (por hora, diariamente, etc.). Neste exemplo, um conjunto de dados de Blob do Azure é usado. Para criar um conjunto de dados de Blob do Azure, você precisa primeiro criar um serviço vinculado do Armazenamento do Azure. 

Aqui estão as definições exemplo de serviço vinculado do Armazenamento do Azure e o conjunto de dados de Blob do Azure: 

**Serviço Vinculado do Armazenamento do Azure:**
```json
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<storageaccountkey>"
        }
    }
}
```
 

**Conjunto de dados do Blob do Azure:** 
```json
{
    "name": "OutputDataset",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "fileName": "sparkoutput.txt",
            "folderPath": "spark/output/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": "\t"
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

Esse conjunto de dados trata-se mais de um conjunto de dados fictício. O Data Factory usa as configurações de frequência e o intervalo e executa o pipeline diariamente nos horários de início e término de um pipeline. Na definição de pipeline de exemplo, os horários de início e término estão a apenas um dia de intervalo, de modo que o pipeline é executado apenas uma vez. 

## <a name="spark-activity-properties"></a>Propriedades da Atividade Spark

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON: 

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| name | Nome da atividade no pipeline. | Sim |
| description | Texto que descreve o que a atividade faz. | Não |
| type | Essa propriedade deve ser definida como HDInsightSpark. | Sim |
| linkedServiceName | Nome do serviço vinculado do HDInsight no qual o programa Spark é executado. | Sim |
| rootPath | O contêiner de Blob do Azure e a pasta que contém o arquivo Spark. O nome do arquivo diferencia maiúsculas de minúsculas. | Sim |
| entryFilePath | Caminho relativo à pasta raiz do código/pacote Spark. | Sim |
| className | Classe principal de Java/Spark do aplicativo | Não | 
| argumentos | Uma lista de argumentos de linha de comando para o programa Spark. | Não | 
| proxyUser | A conta de usuário a ser representada para execução do programa Spark | Não | 
| sparkConfig | Propriedades de configuração do Spark. | Não | 
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



## <a name="see-also"></a>Consulte também
* [Atividade de Hive](data-factory-hive-activity.md)
* [Atividade Pig](data-factory-pig-activity.md)
* [Atividade MapReduce](data-factory-map-reduce.md)
* [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
* [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


