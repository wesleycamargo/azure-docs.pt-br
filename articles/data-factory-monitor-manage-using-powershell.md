<properties title="Monitor and manage Azure Data Factory using Azure PowerShell" pageTitle="Monitorar e gerenciar o Data Factory do Azure utilizando o PowerShell do Azure" description="Aprenda a usar o Azure PowerShell para monitorar e gerenciar fábricas de dados do Azure que você criou." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Monitorar e gerenciar o Data Factory do Azure utilizando o PowerShell do Azure
A tabela a seguir lista os cmdlets, é possível utilizar o monitor e gerenciar as data factories do Azure usando o PowerShell do Azure. 

> [WACOM.NOTE] Consulte [Referência de cmdlet de Data Factory][cmdlet-reference] para obter uma documentação abrangente sobre os cmdlets de Data Factory. 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
Obtém as informações sobre uma data factory específica ou todas as data factories em uma assinatura do Azure dentro do grupo de recursos especificado.
 
###Exemplo 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

Esse comando retorna todas as data factories no grupo de recursos ADFTutorialResourceGroup.
 
###Exemplo 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

Esse comando retorna detalhes sobre a data factory ADFTutorialDataFactory no grupo de recursos ADFTutorialResourceGroup. 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
O cmdlet Get-AzureDataFactoryLinkedService obtém informações sobre um serviço vinculado específico ou todos os serviços vinculados a uma data factory do Azure.

### Exemplo 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
Esse comando retorna informações sobre todos os serviços vinculados ao data factory ADFTutorialDataFactory do Azure.


É possível utilizar o parâmetro - DataFactory em vez de utilizar os parâmetros DataFactoryName e ResourceGroupName. Isso ajuda a inserir os nomes de factory e grupo de recursos somente uma vez e utilizar o objeto de data factory como parâmetro para todos os cmdlets que utilizam os parâmetros ResourceGroupName e DataFactoryName.

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### Exemplo 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

Esse comando retorna informações sobre o serviço vinculado MyBlobStore no data factory factoryADFTutorialDataFactory do Azure. 

É possível utilizar o parâmetro - DataFactory em vez de utilizar os parâmetro -ResourceGroup e -DataFactoryName conforme mostrado abaixo: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
O cmdlet Get-AzureDataFactoryTable obtém informações sobre uma tabela específica ou todas as tabelas em um data factory do Azure. 

### Exemplo 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Esse comando retorna informações sobre todas as tabelas na data factory ADFTutorialDataFactory.

É possível utilizar o parâmetro - DataFactory em vez de utilizar os parâmetro -ResourceGroup e -DataFactoryName conforme mostrado abaixo: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### Exemplo 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

Esse comando retorna informações sobre a tabela EmpTableFromBlob na data factory ADFTutorialDataFactory.



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
O cmdlet Get-AzureDataFactoryPipeline obtém informações sobre uma pipeline específica ou todas as pipelines em um data factory do Azure.

### Exemplo 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Esse comando retorna informações sobre todas as pipelines na data factory ADFTutorialDataFactory.

### Exemplo 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

Obtém informações sobre a pipeline ADFTutorialPipeline na data factory ADFTutorialDataFactory.

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
O cmdlet Get-AzureDataFactorySlice obtém todas as fatias para uma tabela em uma data factory do Azure que são produzidas após o StartDateTime e antes do EndDateTime. A fatia de dados com status Ready está pronta para consumo por fatias dependentes.

A tabela a seguir lista todos os status de uma fatia e suas descrições.

<table border="1">	
	<tr>
		<th align="left">Status</th>
		<th align="left">Descrição</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>O processamento de dados ainda não foi iniciado.</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>O processamento de dados está em andamento.</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>O processamento de dados foi concluído e a fatia de dados está pronta.</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>Falha na execução da execução que produz a fatia.</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Ignorar o processamento da fatia.</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Repetir a execução que produz a fatia.</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>O processamento de dados da fatia expirou.</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>A fatia de dados está aguardando a validação em relação as políticas de validação antes de serem processadas.</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Tente novamente a validação da fatia.</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Falha na validação da fatia.</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>Uma fatia será esse status se LongRetry for especificado na tabela JSON e repetições regulares da fatia falharem.</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>A validação da fatia (com base em políticas definidas na tabela JSON) está sendo executada.</td>
	</tr>

</table>

Para cada uma das fatias, é possível fazer analise profunda e consultar mais informações sobre a execução que está produzindo a fatia utilizando os cmdlets Get-AzureDataFactoryRun e Save-AzureDataFactoryLog.

### Exemplo

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

Esse comando obtém todas as fatias para a tabela EmpSQLTable na data factory ADFTutorialDataFactory produzidas após 2014-05-20T10:00:00 (GMT). Substitua a data e hora com a data e hora especificada ao executar o Set-AzureDataFactoryPipelineActivePeriod.

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

O cmdlet Get-AzureDataFactoryRun obtém todas as execuções para uma fatia de dados de uma tabela em uma data factory do Azure.  Uma tabela em uma data factory do Azure é composta de fatias ao longo do eixo de tempo. A largura de uma fatia é determinada pelo agendamento - por hora/diariamente. A execução é uma unidade de processamento de uma fatia. Pode haver uma ou mais execuções de uma fatia no caso de repetições ou se você executar novamente a fatia em caso de falhas. Uma fatia é identificada pela sua hora de início. Portanto, para o cmdlet Get-AzureDataFactoryRun, é necessário passar a hora de início da fatia dos resultados do cmdlet Get-AzureDataFactorySlice.

Por exemplo, para obter uma execução da fatia a seguir, utilize 2015-04-02T20:00:00. 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### Exemplo

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

Esse comando obtém todas as execuções de fatias da tabela EmpSQLTable na data factory ADFTutorialDataFactory iniciada às 16:00 GMT em 21/05/2014.

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
O cmdlet Save-AzureDataFactoryLog baixa os arquivos de log associados com o processamento do Azure HDInsight de projetos de Pig ou Hive ou para atividades personalizadas para seu disco rígido local. Primeiro execute o cmdlet Get-AzureDataFactoryRun para obter uma ID para uma execução de atividade para uma fatia de dados e, em seguida, utilize a ID para recuperar os arquivos de log do armazenamento de blob (objeto binário grande) associado ao cluster HDInsight. 

Se você não especificar o parâmetro **-DownloadLogs**, o cmdlet retorna apenas o local dos arquivos de log. 

Se você especificar o parâmetro **-DownloadLogs** sem especificar um diretório de saída (parâmetro **-Output **), os arquivos de log são baixados para a pasta **Documentos** padrão. 

Se você especificar o parâmetro **-DownloadLogs** juntamente com uma pasta de saída (**-Output**), os arquivos de log são baixados para a pasta especificada. 


### Exemplo 1
Este comando salva os arquivos de log para a execução de atividade com a ID do 841b77c9-d56c-48d1-99a3-8c16c3e77d39 em que a atividade pertence para uma pipeline na data factory denominada LogProcessingFactory no grupo de recursos denominado ADF. Os arquivos de log são salvos na pasta C:\Test. 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### Exemplo 2
Este comando salva os arquivos de log para a pasta Documentos (padrão).


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### Exemplo 3
Esse comando retorna o local dos arquivos de log. Observe que o parâmetro - DownloadLogs não foi especificado. 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
O cmdlet Get-AzureDataFactoryGateway obtém informações sobre todos os gateways ou um gateway específico em uma data factory do Azure. É necessário instalar um gateway em seu computador local para poder adicionar um SQL Server local como um serviço vinculado a uma data factory.

### Exemplo 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
Esse comando retorna informações sobre todos os gateways na data factory ADFTutorialDataFactory.

### Exemplo 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

Esse comando retorna informações sobre o gateway EmpTableFromBlob na data factory ADFTutorialDataFactory.

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
Esse cmdlet define o período ativo para as fatias de dados que são processados pela pipeline. Se você utilizar o conjunto AzureDataFactorySliceStatus, certifique-se de que a data de início e término da fatia está no período ativo da pipeline.

Depois que os pipelines são criados, você pode especificar a duração em que o processamento de dados ocorrerá. Especificando o período ativo de um pipeline, você está definindo a duração de tempo em que as fatias de dados serão processadas com base nas propriedades de Disponibilidade que foram definidas para cada tabela do ADF.

### Exemplo

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

Esse comando define o período ativo para as fatias de dados que são processadas pela pipeline ADFTutoiralPipeline para 21/5/2014 16:00 GMT para 22/5/2014 16:00 GMT.

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
Define o status de uma fatia para uma tabela. A fatia da data de início e de término deve estar no período ativo da pipeline.

### Valores com suporte para status
Cada fatia de dados para uma tabela percorre os estágios diferentes. Esses estágios são ligeiramente diferentes com base em se as políticas de validação são especificadas.


- Se as políticas de validação  não forem especificadas: PendingExecution -> InProgress -> Ready
- Se a políticas de validação são especificadas: PendingExecution -> Pending Validation -> InProgress -> Ready

A tabela a seguir fornece descrições dos status possíveis de uma fatia e informa se o status pode ser definido utilizando o Set-AzureDataFactorySliceStatus ou não.

<table border="1">	
	<tr>
		<th>Status</th>
		<th>Descrição</th>
		<th>Isso pode ser definido utilizando o cmdlet></th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>O processamento de dados ainda não foi iniciado.</td>
		<td>S</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>O processamento de dados está em andamento.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>O processamento de dados foi concluído e a fatia de dados está pronta.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>Falha na execução da execução que produz a fatia.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Ignorar o processamento da fatia.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Repetir a execução que produz a fatia.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>O processamento de dados expirou.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>A fatia de dados está aguardando a validação em relação as políticas de validação antes de serem processadas.</td>
		<td>S</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Tente novamente a validação da fatia.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Falha na validação da fatia.</td>
		<td>N</td>
	</tr>
	</table>


### Valores com suporte - tipo de atualização
Para cada tabela em uma data factory do Azure, ao definir o status de uma fatia, é necessário especificar se a atualização de status se aplica somente à tabela ou se as atualizações de status propagam para todas as fatias afetadas.

<table border="1">	
	<tr>
		<th>Tipo de atualização</th>
		<th>Descrição</th>
		<th>Isso pode ser definido utilizando o cmdlet</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>Define o status de cada fatia da tabela na faixa de tempo especificado</td>
		<td>S</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>Define o status de cada fatia da tabela e todas as tabelas (ascendente) dependentes que são utilizadas como tabelas de entrada para atividades na pipeline.</td>
		<td>S</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
O cmdlet Suspend-AzureDataFactoryPipeline suspende a pipeline especificada em uma data factory do Azure. É possível retomar a pipeline posteriormente utilizando o cmdlet Resume-AzureDataFactoryPipeline.

### Exemplo

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Este comando suspende a pipeline ADFTutorialPipeline na data factory do Azure ADFTutorialDataFactory.

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
O cmdlet Resume-AzureDataFactoryPipeline retoma a pipeline especificada que está em estado suspenso em um data factory do Azure. 

### Exemplo

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Este comando retoma a pipeline ADFTutorialPipeline na data factory ADFTutorialDataFactory que foi suspensa antes, utilizando o comando Suspend-AzureDataFactoryPipeline.

## Consulte também

Artigo | Descrição
------ | ---------------
[Monitore e gerencie a data factory do Azure utilizando o Portal de visualização do Azure][monitor-manage-using-portal] | Este artigo descreve como monitorar e gerenciar uma data factory do Azure utilizando o Portal de visualização do Azure.
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um blob do Azure.
[Usar o Pig e o Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo tem um passo a passo que mostra como usar a Atividade de HDInsight para executar um script do hive/pig para processar dados de entrada a fim de gerar dados de saída. 
[Tutorial: Mover e processar arquivos de log usando o Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando o Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em um Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em um pipeline. 
[Solucionar problemas de Data Factory][troubleshoot] | Este artigo descreve como solucionar problemas do Data Factory do Azure.
[Referência do Desenvolvedor do Data Factory do Azure][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 
[Referência de cmdlet do Data Factory do Azure][cmdlet-reference] | Este conteúdo de referência apresenta detalhes sobre todos os **cmdlets do Data Factory**.

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
