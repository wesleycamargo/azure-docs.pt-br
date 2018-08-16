---
title: Transformar dados com o Jar do Databricks - Azure | Microsoft Docs
description: Saiba como processar ou transformar dados executando um Jar do Databricks.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.assetid: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/15/2018
ms.author: douglasl
ms.openlocfilehash: a47d0130cd06a936da456ec6d78bde99907072f2
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526293"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformar dados executando uma atividade de Jar no Azure Databricks

A atividade de Jar do Azure Databricks em um [pipeline do Data Factory](concepts-pipelines-activities.md) executa um Spark Jar em seu cluster do Azure Databricks. Este artigo se baseia no artigo sobre [atividades de transformação de dados](transform-data.md) que apresenta uma visão geral da transformação de dados e as atividades de transformação permitidas. O Azure Databricks é uma plataforma gerenciada para executar o Apache Spark.

Para ver uma introdução de 11 minutos e uma demonstração desse recurso, assista ao seguinte vídeo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definição de atividade do Databricks Jar

A seguir está a definição JSON de exemplo de uma Atividade de Jar do Databricks:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Propriedades de atividade do Databricks Jar

A tabela a seguir descreve as propriedades JSON usadas na definição de JSON:

|Propriedade|DESCRIÇÃO|Obrigatório|
|:--|---|:-:|
|Nome|Nome da atividade no pipeline.|SIM|
|Descrição|Texto que descreve o que a atividade faz.|Não |
|Tipo|Para a Atividade do Databricks Jar, o tipo de atividade é DatabricksSparkJar.|SIM|
|linkedServiceName|Nome do serviço vinculado ao Databricks no qual a atividade de Jar é executado. Para saber mais sobre esse serviço vinculado, consulte o artigo [Compute linked services](compute-linked-services.md) (Serviços de computação vinculados).|SIM|
|mainClassName|O nome completo da classe que contém o método principal a ser executado. Essa classe deve estar contida em um JAR fornecido como uma biblioteca.|SIM|
|parâmetros|Parâmetros que serão passados ao método principal.  Isto é uma matriz de cadeias de caracteres.|Não |
|bibliotecas|Uma lista de bibliotecas a serem instaladas no cluster, que executará o trabalho. Ele pode ser uma matriz de <string, object>|Sim (pelo menos um contendo o método mainClassName)|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotecas com suporte para atividades do databricks

Na definição da atividade acima do Databricks você especifica esses tipos de biblioteca: *jar*, *egg*, *maven*, *pypi*,  *cran*.

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "http://cran.us.r-project.org"
            }
        }
    ]
}

```

Para obter mais detalhes, consulte [documentação do Databricks](https://docs.azuredatabricks.net/api/latest/libraries.html#managedlibrarieslibrary) para tipos de biblioteca.

## <a name="how-to-upload-a-library-in-databricks"></a>Como carregar uma biblioteca no Databricks

#### <a name="using-databricks-workspace-uihttpsdocsazuredatabricksnetuser-guidelibrarieshtmlcreate-a-library"></a>[Usando o espaço de trabalho do Databricks da interface do usuário](https://docs.azuredatabricks.net/user-guide/libraries.html#create-a-library)

Para obter o caminho dbfs da biblioteca adicionada usando a interface do usuário, você pode usar [CLI do Databricks (instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Normalmente, as bibliotecas Jar são armazenadas em dbfs:/FileStore/jars ao usar a interface do usuário. Você pode listar todos os por meio da CLI: *databricks fs ls dbfs:/FileStore/job-jars* 



#### <a name="copy-library-using-databricks-clihttpsdocsazuredatabricksnetuser-guidedev-toolsdatabricks-clihtmlcopy-a-file-to-dbfs"></a>[Copie a biblioteca usando a CLI do Databricks](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#copy-a-file-to-dbfs)
Usar a CLI do Databricks [(etapas de instalação)](https://docs.azuredatabricks.net/user-guide/dev-tools/databricks-cli.html#install-the-cli). 

Exemplo – copiar o JAR a dbfs: *dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar*
