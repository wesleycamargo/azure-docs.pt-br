<properties 
	pageTitle="Solucionar problemas do Data Factory do Azure" 
	description="Saiba como solucionar problemas com o uso do Azure Data Factory." 
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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Solucionar problemas do Data Factory
Você pode solucionar problemas do Data Factory do Azure usando o Portal do Azure (ou) cmdlets do PowerShell do Azure. Este tópico tem instruções passo a passo que mostram como usar o Portal do Azure para solucionar rapidamente os erros encontrados com o Data Factory. 

## Neste artigo

- [Passo a passo: Solucionando problemas de um erro na cópia de dados](#copywalkthrough)
- [Passo a passo: Solucionando problemas de um erro no processamento do Hive/Pig](#pighivewalkthrough)

## <a name="copywalkthrough"></a>Passo a passo: Solucionando problemas de um erro na cópia de dados
Neste passo a passo, você apresentará um erro no tutorial do artigo Introdução ao Data Factory e aprenderá a usar o Portal do Azure para solucionar o erro.

### Pré-requisitos
1. Conclua o tutorial no artigo [Introdução ao Data Factory do Azure][adfgetstarted].
2. Confirme que o **ADFTutorialDataFactory** produz dados na tabela **emp** no Banco de Dados SQL do Azure.  
3. Agora, exclua a tabela **emp** (**drop table emp**) do Banco de Dados SQL do Azure. Isso apresentará um erro.
4. Execute o seguinte comando no **Azure PowerShell** para atualizar o período ativo para o pipeline de forma que ele tente gravar dados na tabela **emp**, que não existe mais.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] Substitua o valor <b>StartDateTime</b> pelo dia atual e o valor <b>EndDateTime</b> pelo dia seguinte. 


### Usar o Portal de Visualização do Azure para solucionar o erro

1.	Faça logon no [Portal de Visualização do Azure][azure-preview-portal]. 
2.	Clique em **ADFTutorialDataFactory** no **Quadro Inicial**. Se você não vir o link do data factory no **Quadro Inicial**, clique no hub **PROCURAR** e clique em **Tudo**. Clique em **Data factories...** na folha **Procurar** e clique em **ADFTutorialDataFactory**.
3.	Observe que você verá **Com erros** no bloco **Conjuntos de dados**. Clique em **Com erros**. Você deve ver a folha **Conjuntos de dados com erros**.

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. Na folha **Conjuntos de dados** com erros, clique em **EmpSQLTable** para ver a folha **TABELA**.	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Na folha **TABELA**, você verá as fatias do problema, ou seja, as fatias com um erro na lista **Fatias com problema** na parte inferior. Você também pode ver as fatias recentes com erros na lista **Fatias recentes**. Clique em uma fatia na lista **Fatias com problema**. 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Se você clicar em **Fatias com problema** (não em um problema específico), verá a folha **FATIAS DE DADOS** e, em seguida, clique em uma **fatia do problema específico** para ver o slide **FATIA DE DADOS** da fatia de dados selecionada.

6. Na folha **FATIA DE DADOS** para **EmpSQLTable**, você verá todas as **execuções de atividade** para a fatia na lista na parte inferior. Clique em uma **execução de atividade** na lista com falha.

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Na folha **Detalhes de Execução da Atividade** da execução de atividade selecionada, você verá detalhes sobre o erro. Nesse cenário, você verá: **Nome de objeto inválido 'emp'**.

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

Para resolver esse problema, crie a tabela **emp** usando o script SQL do artigo [Introdução ao Data Factory][adfgetstarted].


### Usar cmdlets do PowerShell do Azure para solucionar o erro
1.	Inicie o **PowerShell do Azure**. 
2.	Alterne para o modo **AzureResourceManager** pois os cmdlets do Data Factory estão disponíveis somente nesse modo.

         
		switch-azuremode AzureResourceManager

3. Execute o comando Get-AzureDataFactorySlice para ver as fatias e seus status. Você deve ver uma fatia com o status: Falha.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] Substitua **StartDateTime** pelo valor StartDateTime especificado para **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Observe a hora de **Início** da fatia com problema (a fatia com **Status** definido como **Falha**) na saída. 
4. Agora, execute o cmdlet **Get-AzureDataFactoryRun** para obter detalhes sobre a execução de atividade da fatia.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	O valor de **StartDateTime** é a hora de Início do erro/fatia com problema observada na etapa anterior. A data e hora devem ser colocadas entre aspas duplas.
5. Você deve ver a saída com detalhes sobre o erro (semelhante ao seguinte):

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighavewalkthrough"></a>Passo a passo: Solucionando problemas de um erro no processamento do Hive/Pig
Este passo a passo fornece as etapas para solucionar problemas de erro no processamento do Hive/Pig usando o Portal de Visualização do Azure e o PowerShell do Azure. 


### Passo a passo: Usar o Portal do Azure para solucionar problemas de um erro no processamento do Pig/Hive
Nesse cenário, o conjunto de dados está em um estado de erro devido a uma falha no processamento do Hive em um cluster HDInsight.

1. Clique em **Com erros** no bloco **Conjuntos de Dados** na página inicial **DATA FACTORY**.

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Na folha **Conjuntos de dados com erros**, clique na **tabela** em que você está interessado.

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Na folha **TABELA**, clique na **fatia com problema** com **STATUS** definido como **Falha**.

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Na folha **FATIA DE DADOS**, clique na **Execução de Atividade** com falha.

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Na folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**, você pode baixar os arquivos associados ao processamento do HDInsight. Clique em **Download** para que **Status/stderr** baixe o arquivo de log de erros que contém detalhes sobre o erro.

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### Passo a passo: Usar o PowerShell do Azure para solucionar problemas de um erro no processamento do Pig/Hive
1.	Inicie o **PowerShell do Azure**. 
2.	Alterne para o modo **AzureResourceManager** pois os cmdlets do Data Factory estão disponíveis somente nesse modo.

         
		switch-azuremode AzureResourceManager

3. Execute o comando Get-AzureDataFactorySlice para ver as fatias e seus status. Você deve ver uma fatia com o status: Falha.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] Substitua **StartDateTime** pelo valor StartDateTime especificado para **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Observe a hora de **Início** da fatia com problema (a fatia com **Status** definido como **Falha**) na saída. 
4. Agora, execute o cmdlet **Get-AzureDataFactoryRun** para obter detalhes sobre a execução de atividade da fatia.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	O valor de **StartDateTime** é a hora de Início do erro/fatia com problema observada na etapa anterior. A data e hora devem ser colocadas entre aspas duplas.
5. Você deve ver a saída com detalhes sobre o erro (semelhante ao seguinte):

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. Você pode executar o cmdlet **Save-AzureDataFactoryLog** com o valor Id que você vê da saída acima e baixar os arquivos de log usando a opção **-DownloadLogs** para o cmdlet.

## Dicas de solução de problemas

### Falha ao se conectar ao SQL Server local 
Verifique se o SQL Server está acessível por meio do computador em que o gateway está instalado.


1. Execute ping do computador onde o SQL Server está instalado
2. No computador no qual o gateway está instalado, tente se conectar à instância do SQL Server usando as credenciais especificadas no Portal do Azure usando o SQL Server Management Studio (SSMS).

### Falha na operação de cópia
1. Inicie o Gerenciador de Configuração de Gateway de gerenciamento de dados no computador no qual o gateway foi instalado. Verifique se o **Nome do gateway** está definido como o nome lógico do gateway no **Portal do Azure**, se o **Status da chave de gateway** é **registrado** e se o **Status do serviço** é **Iniciado**. 
2. Inicie o **Visualizador de Eventos**. Expanda **Logs de Aplicativos e Serviços** e clique em **Gateway de Gerenciamento de Dados**. Verifique se há erros relacionados ao Gateway de Gerenciamento de Dados. 



## Consulte também

Artigo | Descrição
------ | ---------------
[Habilitar seus pipelines para trabalhar com dados locais][use-onpremises-datasources] | Este artigo tem um passo a passo que mostra como copiar dados de um banco de dados SQL Server local para um blob do Azure.
[Usar o Pig e o Hive com o Data Factory][use-pig-and-hive-with-data-factory] | Este artigo tem um passo a passo que mostra como usar a Atividade de HDInsight para executar um script do hive/pig para processar dados de entrada a fim de gerar dados de saída. 
[Tutorial: Mover e processar arquivos de log usando o Data Factory][adf-tutorial] | Este artigo fornece um passo a passo que mostra como implementar um cenário próximo do real usando o Data Factory do Azure para transformar dados de arquivos de log em informações.
[Usar atividades personalizadas em um Data Factory][use-custom-activities] | Este artigo fornece um passo a passo com instruções para criar uma atividade personalizada e usá-la em um pipeline. 
[Monitorar e gerenciar o Data Factory do Azure usando o PowerShell][monitor-manage-using-powershell] | Este artigo descreve como monitorar um Data Factory do Azure usando cmdlets do PowerShell do Azure. 
[Solucionar problemas de Data Factory][troubleshoot] | Este artigo descreve como solucionar problemas do Data Factory do Azure. Você pode testar o passo a passo neste artigo no ADFTutorialDataFactory introduzindo um erro (excluindo a tabela no Banco de Dados SQL do Azure). 
[Referência do Desenvolvedor do Data Factory do Azure][developer-reference] | A Referência do Desenvolvedor tem o conteúdo de referência abrangente de cmdlets, script JSON, funções, etc... 
[Referência de cmdlet do Data Factory do Azure][cmdlet-reference] | Este conteúdo de referência apresenta detalhes sobre todos os **cmdlets do Data Factory**.

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!--HONumber=46--> 
