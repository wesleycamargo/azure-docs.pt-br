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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Solucionar problemas do Data Factory
Você pode solucionar problemas do Data Factory do Azure usando o Portal do Azure (ou) cmdlets do PowerShell do Azure. Este tópico tem instruções passo a passo que mostram como usar o Portal do Azure para solucionar rapidamente os erros encontrados com o Data Factory.

## Problema: Não é possível executar os cmdlets de fábrica de dados
Para resolver esse problema, alterne o modo do Azure para **AzureResourceManager**:

Iniciar **PowerShell do Azure** e execute o seguinte comando para alternar para o **AzureResourceManager** modo. Os cmdlets do Azure Data Factory estão disponíveis na **AzureResourceManager** modo.

         switch-azuremode AzureResourceManager

## Problema: Erro não autorizado ao executar um cmdlet de fábrica de dados
Você provavelmente não está usando a conta do Azure à direita ou a assinatura com o PowerShell do Azure. Use os seguintes cmdlets para selecionar a conta à direita do Azure e a assinatura a ser usada com o PowerShell do Azure.

1. Add-AzureAccount - Use a ID de direito de usuário e senha
2. Get-AzureSubscription - exibir todas as assinaturas para a conta. 
3. Select-AzureSubscription <subscription name> -Selecionar a assinatura correta. Use o mesmo que você use para criar uma fábrica de dados no Portal de visualização do Azure.

## Problema: Falha ao inicializar dados instalação expressa do Gateway do Portal do Azure
A instalação expressa para o Gateway de dados requer o Internet Explorer ou um navegador da web compatível com Microsoft ClickOnce. Se você não conseguir iniciar a instalação do Express, você pode:

1. Alterne para o Internet Explorer se falhar com outros navegadores. Ou
2. Use os links de "Instalação Manual" mostrados na lâmina do mesma no portal para fazer a instalação e, em seguida, copie a chave fornecida na tela e, em seguida, cole quando a configuração de Gateway de gerenciamento de dados está pronta. Se ele não inicia, procure seu menu Iniciar "Gateway de gerenciamento de dados Microsoft" e cole na chave quando for iniciado. 


## Problema: Falha ao iniciar o Gerenciador de credenciais do Portal do Azure
Ao configurar ou atualizar um serviço SQL Server vinculado por meio do Portal do Azure, o Gerenciador de credenciais do aplicativo será iniciado para garantir a segurança. Ele requer o Internet Explorer ou um navegador da web compatível com Microsoft ClickOnce. Você pode alternar para o Internet Explorer se falhar com outros navegadores.

## Problema: Falha ao se conectar ao SQL Server no local 
Verifique se o SQL Server está acessível por meio do computador em que o gateway está instalado. No computador no qual o gateway está instalado, você pode:

1. Executar ping da máquina onde o SQL Server está instalado. Ou
2. Tente se conectar à instância do SQL Server usando as credenciais especificadas no Portal do Azure usando o SQL Server Management Studio (SSMS).


## Problema: Entrada fatias estão no estado PendingExecution ou PendingValidation por vez

As fatias poderiam estar em **PendingExecution** ou **PendingValidation** estado devido a vários motivos e uma das razões mais comuns é que o **waitOnExternal** propriedade não for especificada no **disponibilidade** seção da primeira tabela/conjunto de dados no pipeline. Qualquer conjunto de dados que é produzido fora do escopo do alocador de dados do Azure deve ser marcado com **waitOnExternal** propriedade sob **disponibilidade** seção. Isso indica que os dados são externos e não foi feito por qualquer pipelines dentro da fábrica de dados. As fatias de dados são marcadas como **pronto** depois que os dados estão disponíveis no armazenamento do respectivo.

Consulte o exemplo a seguir para o uso de **waitOnExternal** propriedade. Você pode especificar **{waitOnExternal}** sem definir valores para propriedades na seção de forma que os valores padrão são usados.

Consulte o tópico de tabelas no [referência de script JSON][json-scripting-reference] para obter mais detalhes sobre essa propriedade.
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 Para resolver o erro, adicione o **waitOnExternal** seção à definição da tabela de entrada JSON e recrie a tabela.

## Problema: Falha de operação de cópia híbrida
Para obter mais detalhes:

1. Inicie o Gerenciador de configuração de Gateway de gerenciamento de dados na máquina na qual o gateway foi instalado. Verifique o **nome do Gateway** é definido como o nome lógico de gateway no **Portal Azure**, **status da chave do Gateway** é **registrado** e **status do serviço** é **iniciado**. 
2. Iniciar **eventos**. Expanda **Applications and Services Logs** e clique em **Data Management Gateway**. Verifique se há erros relacionados ao Gateway de Gerenciamento de Dados. 

## Problema: Em demanda HDInsight provisionamento falha com erro

Ao usar um serviço vinculado do tipo HDInsightOnDemandLinkedService, você deve especificar um linkedServiceName que aponta para o armazenamento de Blob do Azure. Essa conta de armazenamento será usada para copiar todos os logs e arquivos de suporte para o cluster de HDInsight sob demanda. Às vezes, a atividade que faz o provisionamento por demanda no HDInsight pode falhar com o seguinte erro:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Esse erro normalmente indica que o local da conta de armazenamento especificado no linkedServiceName não está no mesmo data center local onde o provisionamento do HDInsight está acontecendo. Por exemplo, se o local de fábrica de dados do Azure é Oeste dos EUA, e o provisionamento do HDInsight sob demanda acontece no Oeste dos EUA, mas o local de conta de armazenamento de BLOBs do Azure é definido como Leste dos EUA, o provisionamento por demanda falhará.

Além disso, há um segundo additionalLinkedServiceNames de propriedade JSON onde as contas de armazenamento adicionais podem ser especificadas no HDInsight sob demanda. Essas contas de armazenamento vinculado adicional devem estar no mesmo local que o cluster HDInsight ou falhará com o mesmo erro.



## Problema: Falha de atividade personalizada
Ao usar uma atividade personalizada no Azure Data Factory (tipo de atividade de pipeline CustomActivity), o aplicativo personalizado é executado no serviço vinculado especificado para HDInsight como um mapa apenas trabalho MapReduce de streaming.

Quando a atividade personalizado é executado, fábrica de dados do Azure poderá capturar essa saída do cluster HDInsight e salvá-lo no *adfjobs* contêiner de armazenamento em sua conta de armazenamento de BLOBs do Azure. Em caso de erro, você pode ler o texto da **stderr** arquivo de texto de saída após a ocorrência de uma falha. Os arquivos estão acessíveis e legível no portal do Azure em si no navegador da web, ou usando ferramentas do Gerenciador de armazenamento para acessar os arquivos mantidos no contêiner de armazenamento no armazenamento de Blob do Azure diretamente.

Para enumerar e ler os logs para uma determinada atividade personalizada, você pode seguir uma das explicações passo a passo ilustrada mais adiante nesta página. Em Resumo:

1.  No portal do Azure **Procurar** para localizar sua fábrica de dados.
2.  Use o **diagrama** botão para exibir o diagrama de fábrica de dados e, em seguida, clique no **Dataset** tabela a seguir específico **Pipeline** que tem a atividade personalizada. 
3.  No **tabela** lâmina, clique na fatia de interesse a **fatias de problema** para o intervalo de tempo ser investigado.
4.  O detalhado **fatia de dados** lâmina será exibido e ele pode listar vários **atividade é executada** da fatia. Clique em uma **atividade** da lista. 
5.  O **detalhes de execução da atividade** lâmina será exibida. Ele listará o **mensagem de erro** no meio da lâmina e vários **arquivos de Log** listadas na parte inferior da lâmina afiliada a essa atividade seja executada.
	- Sistema/logs-0.log
	- Status
	- Status/sair
	- Status/stderr
	- Status/stdout

6. Clique no primeiro **arquivo de Log** item na lista e o log será aberto em uma nova lâmina com o exibido para a leitura de texto completo. Revise o texto de cada log clicando em cada um deles. A lâmina de Visualizador de texto será aberto. Você pode clicar na **baixar** para baixar o arquivo de texto para exibição offline opcional.

Um **erro comum** de uma atividade personalizada execução do pacote falhou com código de saída '1'. Consulte ' wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr ' para obter mais detalhes.

Para ver mais detalhes para esse tipo de erro, abra o **stderr** arquivo. Um erro comum visto lá é uma condição de tempo limite como este: mapreduce INFO. Trabalho: Id de tarefa: attempt_1424212573646_0168_m_000000_0, Status: falha AttemptID:attempt_1424212573646_0168_m_000000_0 atingiu o tempo limite depois de 600 segundos

Esse mesmo erro pode aparecer várias vezes, se o trabalho repetiu 3 vezes por exemplo, ao longo do período de 30 ou mais minutos.

Esse erro de tempo limite indica 600 segundos (10 minutos) tempo limite aconteceu. Geralmente isso significa que o aplicativo .net personalizado não emitiu qualquer atualização de status por 10 minutos. Se o aplicativo está pendente ou interrompida aguardando em algo muito, os 10 minutos tempo limite é um mecanismo de segurança para impedir que esperar para sempre e atrasar o pipeline de fábrica de dados do Azure.

Esse tempo limite se origina na configuração do cluster HDInsight que estiver vinculado a atividade personalizada. A configuração é **mapred.task.timeout**, cujo padrão é 600000 milissegundos, conforme documentado nas configurações padrão Apache aqui: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Você pode substituir esse padrão alterando os padrões no momento do provisionamento de cluster de provisionamento do HDInsight. Ao usar a fábrica de dados do Azure e **HDInsight sob demanda** vinculado de serviço, a propriedade JSON pode ser adicionada perto suas propriedades HDInsightOnDemandLinkedService JSON. Por exemplo, você pode aumentar o valor para 20 minutos usando essa propriedade JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Para mais contexto e um exemplo completo de JSON para editar essas mapa reduzem configuração propriedades consulte exemplo 3 a documentação do MSDN https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problema: Solicitação PowerShell falha com erro 400 Solicitação inválida "Nenhum provedor de recurso registrado encontrado..."

As fábrica do PowerShell do Azure dados visualização privada versões anteriores 2014-05-01-preview, 2014-07-01-visualização e 2014-08-01-visualização serão descontinuados a partir de 10 de março de 2015. Recomendamos que você use a versão mais recente dos cmdlets ADF, que agora fazem parte do Azure PowerShell baixar, como o download desta URL http://go.microsoft.com/?linkid=9811175&clcid=0x409

Se você usar as versões descontinuadas do SDK do PowerShell do Azure, você pode receber os seguintes erros:

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> Passo a passo: Um erro na cópia dos dados de solução de problemas
Neste passo a passo, você apresentará um erro no tutorial do artigo Introdução ao Data Factory e aprenderá a usar o Portal do Azure para solucionar o erro.

### Pré-requisitos
1. Conclua o Tutorial a [Introdução ao Azure Data Factory][adfgetstarted] artigo.
2. Confirme se o **ADFTutorialDataFactory** produz dados a **emp** tabela no banco de dados do SQL Azure.  
3. Agora, exclua o **emp** tabela (* * soltar tabela emp * *) do banco de dados do SQL Azure. Isso apresentará um erro.
4. Execute o seguinte comando **PowerShell do Azure** para atualizar o período ativo para o pipeline para que ele tenta gravar dados de **emp** tabela, que não existe mais.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]Substitua <b>StartDateTime</b> valor com o dia atual e <b>EndDateTime</b> valor com o dia seguinte.


### Usar o Portal de Visualização do Azure para solucionar o erro

1.	Faça logon para [Portal de visualização do Azure][azure-preview-portal]. 
2.	Clique em **ADFTutorialDataFactory** do **Startboard**. Se você não vir a fábrica de dados link a **quadro inicial**, clique em **Procurar** hub e clique em **tudo**. Clique em **fábricas de dados...** no **Procurar** blade e clique em **ADFTutorialDataFactory**.
3.	Observe que você consulte **com erros** sobre o **conjuntos de dados** lado a lado. Clique em **com erros**. Você deve ver **conjuntos de dados com erros** lâmina.

	![Fábrica de dados com o link de erros][image-data-factory-troubleshoot-with-error-link]

4. No **conjuntos de dados** com a lâmina de erros, clique em **EmpSQLTable** para ver o **tabela** lâmina.

	![Conjuntos de dados com a lâmina de erros][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. No **tabela** lâmina, você deve ver as fatias de problema, ou seja, as fatias com um erro no **fatias problema** lista na parte inferior. Você também pode ver as fatias recentes com erros no **fatias recentes** lista. Clique em uma fatia de **fatias problema** lista.

	![Blade de tabela com fatias de problema][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Se você clicar em **fatias problema** (não em um problema específico), você verá o **FATIAS de dados** blade e depois clique em um **fatia do problema específico** para ver o **FATIA de dados** slide da fatia de dados selecionada.

6. No **FATIA de dados** lâmina para **EmpSQLTable**, você vê todos **atividade executa** da fatia na lista na parte inferior. Clique em uma **atividade execute** da lista que falhou.

	![Blade de fatias de dados com execuções ativas][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. No **detalhes de execução da atividade** lâmina para a atividade executar selecionada, você deverá ver detalhes sobre o erro. Nesse cenário, você deve ver: **nome de objeto inválido 'emp'**.

	![Detalhes da execução com um erro de atividade][image-data-factory-troubleshoot-activity-run-with-error]

Para resolver esse problema, crie o **emp** de script de tabela usando o SQL [Introdução aos dados fábrica][adfgetstarted] artigo.


### Usar cmdlets do PowerShell do Azure para solucionar o erro
1.	Iniciar **PowerShell do Azure**. 
2.	Alterne para o **AzureResourceManager** o modo como os cmdlets de fábrica de dados estão disponíveis somente nesse modo.

         
		switch-azuremode AzureResourceManager

3. Execute o comando Get-AzureDataFactorySlice para ver as fatias e seus status. Você deve ver uma fatia com o status: falha.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]Substitua **StartDateTime** com o StartDateTime valor especificado para o **conjunto AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Observe o **Iniciar** tempo da fatia do problema (fatia com **Status** definida como **Falha**) na saída. 
4. Agora, execute o **Get-AzureDataFactoryRun** para obter detalhes sobre a atividade seja executada da fatia.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	O valor de **StartDateTime** é a hora de início da fatia de erro/problema observado na etapa anterior. A data e hora devem ser colocadas entre aspas duplas.
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

 

## <a name="pighivewalkthrough"></a> Passo a passo: Erro no processamento do Hive/Pig de solução de problemas
Este passo a passo fornece as etapas para solucionar problemas de erro no processamento do Hive/Pig usando o Portal de Visualização do Azure e o PowerShell do Azure.


### Passo a passo: Uso do Azure Portal para solucionar problemas de um erro no processamento do Pig/Hive
Nesse cenário, o conjunto de dados está em um estado de erro devido a uma falha no processamento do Hive em um cluster HDInsight.

1. Clique em **com erros** em **conjuntos de dados** lado a lado no **DATA FACTORY** página inicial.

	![Link de erros no bloco de conjuntos de dados][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. No **conjuntos de dados com erros** lâmina, clique o **tabela** que você está interessado.

	![Conjuntos de dados com a lâmina de erros][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. No **tabela** lâmina, clique na **fatia problema** com **STATUS** definida como **Falha**.

	![Tabela com fatias de problema][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. No **FATIA de dados** lâmina, clique o **atividade executada** que falhou.

	![Fatia de dados com falha de execução][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. No **detalhes de execução da atividade** lâmina, você pode baixar os arquivos associados com o processamento do HDInsight. Clique em **baixar** para **Status/stderr** para baixar o arquivo de log de erro que contém detalhes sobre o erro.

	![Detalhes da execução com o link de download de atividade][image-data-factory-troubleshoot-activity-run-details]

    
### Passo a passo: Usar o Azure PowerShell para solucionar problemas de um erro no processamento do Pig/Hive
1.	Iniciar **PowerShell do Azure**. 
2.	Alterne para o **AzureResourceManager** o modo como os cmdlets de fábrica de dados estão disponíveis somente nesse modo.

         
		switch-azuremode AzureResourceManager

3. Execute o comando Get-AzureDataFactorySlice para ver as fatias e seus status. Você deve ver uma fatia com o status: falha.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]Substitua **StartDateTime** com o StartDateTime valor especificado para o **conjunto AzureDataFactoryPipelineActivePeriod**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Observe o **Iniciar** tempo da fatia do problema (fatia com **Status** definida como **Falha**) na saída. 
4. Agora, execute o **Get-AzureDataFactoryRun** para obter detalhes sobre a atividade seja executada da fatia.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	O valor de **StartDateTime** é a hora de início da fatia de erro/problema observado na etapa anterior. A data e hora devem ser colocadas entre aspas duplas.
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

6. Você pode executar **Salvar AzureDataFactoryLog** cmdlet com valor de Id perceber na saída acima e baixar o log de arquivos usando o **- DownloadLogs** opção para o cmdlet.



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!---HONumber=GIT-SubDir--> 