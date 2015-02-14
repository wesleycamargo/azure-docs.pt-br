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
	ms.date="12/08/2014" 
	ms.author="bradsev"/>

# Coletar despejos de heap para Depuração e Análise

Os despejos de heap podem ser automaticamente coletados para serviços do Hadoop e posicionados na conta de armazenamento de blob de um usuário em HDInsightHeapDumps/.  Os arquivos de despejo para um serviço com heaps contêm um instantâneo da memória do aplicativo. Isso inclui os valores das variáveis no momento em que o despejo foi criado.

A coleção de despejos de heap para vários serviços deve ser habilitada para serviços em clusters individuais. O padrão para esse recurso deve ser desativado para um cluster. Esses despejos de heap podem ser grandes, portanto é aconselhável monitorar a conta de armazenamento de blob onde eles estão sendo salvos depois que a coleção for ativada.

## Neste artigo

- [Para quais serviços despejos de heap podem ser habilitados?](#whichServices)
- [Os elementos de configuração que habilitam os despejos de heap](#configuration)
- [Como habilitar os despejos de heap com o PowerShell do HDInsight do Azure](#powershell)
- [Como habilitar os despejos de heap com o SDK do .NET do HDInsight](#sdk)


## <a name="whichServices"></a>Para quais serviços despejos de heap podem ser habilitados?

Os serviços que podem ter despejos de heap habilitados quando solicitados são: 

*  **hcatalog**: tempelton
*  **hive**: hiveserver2, metastore, derbyserver 
*  **mapreduce**: jobhistoryserver 
*  **yarn**: resourcemanager nodemanager, timelineserver 
*  **hdfs**: datanode, secondarynamenode, namenode

## <a name="configuration"></a>Os elementos de configuração que habilitam os despejos de heap

Para ativar os despejos de heap para um serviço, o usuário precisa definir os elementos de configuração apropriado na seção desse serviço, o qual é especificado como service_name.

	"javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
	"javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof" 

O valor de <service_name> pode ser qualquer um dos serviços listados acima: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, ou namenode.

## <a name="powershell"></a>Como habilitar os despejos de heap com o PowerShell do HDInsight do Azure

Por exemplo, para ativar os despejos de heap usando o PowerShell para o jobhistoryserver usuário deve fazer o seguinte:

Usando o sdk do powershell:

	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

	$MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="sdk"></a>Como habilitar os despejos de heap com SDK do .NET do HDInsight do Azure

Por exemplo, para ativar os despejos de heap usando o SDK do .NET para jobhistoryserver usuário deve fazer o seguinte:

Usando o sdk do c#:

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

	clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));



<!--HONumber=42-->
