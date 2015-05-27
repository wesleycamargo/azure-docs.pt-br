<properties 
	pageTitle="Coletar despejos de heap para Depuração e Análise| Azure" 
	description="Coletar despejos de heap para Depuração e Análise" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Coletar despejos de heap para depuração e análise

Despejos de heap dos serviços Hadoop podem ser coletados automaticamente e colocados na conta de armazenamento de Blob do Azure de um usuário em HDInsightHeapDumps/. Os arquivos de despejo para um serviço com heaps contêm um instantâneo da memória do aplicativo. Isso inclui os valores das variáveis no momento em que o despejo foi criado.

A coleção de despejos de heap para vários serviços deve ser habilitada para serviços em clusters individuais. O padrão para esse recurso deve ser desativado para um cluster. Esses despejos de heap podem ser grandes; portanto, é aconselhável monitorar a conta de armazenamento de Blob na qual eles são salvos após habilitar a coleta.


## <a name="whichServices"></a>Serviços qualificados para despejos de heap

Os serviços cujos despejos de heap podem ser habilitados, se solicitados, são:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver 
*  **mapreduce** - jobhistoryserver 
*  **yarn** - resourcemanager, nodemanager, timelineserver 
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration"></a>Elementos de configuração que habilitam despejos de heap

Para habilitar despejos de heap para um serviço, você precisa definir os elementos de configuração apropriados na seção desse serviço, especificado por **service_name**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

O valor de **service_name** pode ser qualquer um dos serviços listados acima: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode ou namenode.

## <a name="powershell"></a>Como habilitar os despejos de heap usando o PowerShell do Azure

Por exemplo, para habilitar despejos de heap para jobhistoryserver usando o PowerShell do Azure, você faria o seguinte:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Como habilitar despejos de heap usando o Azure HDInsight .NET SDK

Por exemplo, para habilitar despejos de heap para jobhistoryserver usando o Azure HDInsight .NET SDK, você faria o seguinte:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));




<!--HONumber=54-->