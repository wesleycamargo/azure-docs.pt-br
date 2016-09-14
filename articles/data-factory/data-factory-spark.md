<properties 
	pageTitle="Invocar programas Spark do Azure Data Factory" 
	description="Aprenda a invocar programas Spark de uma Azure Data Factory usando a atividade MapReduce." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/25/2016" 
	ms.author="spelluru"/>

# Invocar Programas Spark pelo Data Factory
## Introdução
Você pode usar a Atividade MapReduce em um pipeline do Data Factory para executar programas Spark em seu cluster Spark HDInsight. Confira o artigo [Atividade MapReduce](data-factory-map-reduce.md) para obter informações detalhadas sobre como usar a atividade antes de ler esse artigo.

## Amostra do Spark no GitHub
O [Spark - amostra de Data Factory no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/Spark) mostra como usar a atividade MapReduce para invocar um programa Spark. O programa Spark apenas copia dados de um contêiner de Blob do Azure para outro.

## Entidades de Data Factory
A pasta **Spark-ADF/src/ADFJsons** contém arquivos para entidades de Data Factory (serviços vinculados, conjuntos de dados, pipeline).

Há dois **serviços vinculados** neste exemplo: Armazenamento do Azure e Azure HDInsight. Especifique o nome do armazenamento do Azure e valores de chave em **StorageLinkedService.json** e clusterUri, userName e senha em **HDInsightLinkedService.json**.

Há dois **conjuntos de dados** neste exemplo: **input.json** e **output.json**. Esses arquivos estão localizados na pasta **Conjuntos de dados**. Esses arquivos representam conjuntos de dados de entrada e saída para a atividade MapReduce

Você encontra pipelines de exemplo na pasta **ADFJsons/Pipeline**. Examine um pipeline para entender como invocar um programa Spark usando a atividade MapReduce.

A atividade MapReduce está configurada para invocar **com.adf.sparklauncher.jar** no contêiner **adflibs** no seu armazenamento do Azure (especificado no StorageLinkedService.json). O código-fonte para este programa está na pasta Spark-ADF/src/main/java/com/adf/ e chama spark-enviar e executar trabalhos Spark.

> [AZURE.IMPORTANT] 
Leia [LEIAME.TXT](https://github.com/Azure/Azure-DataFactory/blob/master/Samples/Spark/README.txt) para obter as informações adicionais e mais recentes antes de usar o exemplo.
>  
> Use seu próprio cluster Spark HDInsight com essa abordagem para invocar programas Spark usando a atividade MapReduce. Não há suporte para o uso de um clister HDInsight sob demanda.


## Consulte também
- [Atividade de Hive](data-factory-hive-activity.md)
- [Atividade Pig](data-factory-pig-activity.md)
- [Atividade MapReduce](data-factory-map-reduce.md)
- [Atividade de Transmissão do Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar scripts R](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

<!---HONumber=AcomDC_0831_2016-->