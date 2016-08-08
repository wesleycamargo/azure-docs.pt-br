<properties
	pageTitle="Depurar e analisar serviços do Hadoop com despejos de heap | Microsoft Azure"
	description="Colete despejos de heap automaticamente para serviços do Hadoop e coloque dentro a conta de armazenamento de Blobs do Azure para depuração e análise."
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/25/2016"
	ms.author="jgao"/>


# Coletar despejos de heap no armazenamento de Blob para depurar e analisar serviços do Hadoop

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Despejos de heap contêm um instantâneo da memória do aplicativo, incluindo os valores das variáveis no momento em que o despejo foi criado. Portanto, eles são muito úteis para diagnosticar problemas que ocorrem no tempo de execução. Despejos de heap dos serviços Hadoop podem ser coletados automaticamente e colocados na conta de armazenamento de Blob do Azure de um usuário em HDInsightHeapDumps/.

A coleção de despejos de heap para vários serviços deve ser habilitada para serviços em clusters individuais. O padrão para esse recurso deve ser desativado para um cluster. Esses despejos de heap podem ser grandes; portanto, é aconselhável monitorar a conta de armazenamento de Blob na qual eles são salvos após habilitar a coleta.

> [AZURE.NOTE] As informações neste artigo aplicam-se apenas ao HDInsight baseado no Windows. Para obter informações sobre o HDInsight baseado em Linux, consulte [Habilitar despejos de heap para serviços do Hadoop no HDInsight baseado em Linux](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## Serviços qualificados para despejos de heap

Você pode habilitar o despejo de heap para os seguintes serviços:

*  **hcatalog** - tempelton
*  **hive** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **yarn** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## Elementos de configuração que habilitam despejos de heap

Para habilitar despejos de heap para um serviço, você precisa definir os elementos de configuração apropriados na seção desse serviço, especificado por **service\_name**.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

O valor de **service\_name** pode ser qualquer um dos serviços listados acima: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode ou namenode.

## Habilitar usando o Azure PowerShell

Por exemplo, para habilitar despejos de heap para jobhistoryserver usando o PowerShell do Azure, você faria o seguinte:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## Habilitar o uso do SDK do .NET

Por exemplo, para habilitar despejos de heap para jobhistoryserver usando o Azure HDInsight .NET SDK, você faria o seguinte:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));

<!---HONumber=AcomDC_0727_2016-->