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
	ms.date="11/12/2015" 
	ms.author="spelluru"/>

# Solucionar problemas do Data Factory
Você pode solucionar problemas do Azure Data Factory usando o Portal Clássico do Azure (ou) os cmdlets do Azure PowerShell. Este tópico tem explicações passo a passo que mostram como usar o Portal Clássico do Azure para solucionar rapidamente os erros encontrados no Data Factory.

## Problema: não é possível executar os cmdlets da Data Factory
Para resolver esse problema, alterne o modo do Azure para **AzureResourceManager**:

Inicie o **PowerShell do Azure** e execute o seguinte comando para alternar para o modo **AzureResourceManager**. Os cmdlets da Azure Data Factory estão disponíveis no modo **AzureResourceManager**.

         switch-azuremode AzureResourceManager

## Problema: Erro não autorizado ao executar um cmdlet da Data Factory
Você provavelmente não está usando a assinatura ou conta do Azure correta com o PowerShell do Azure. Use os cmdlets a seguir para selecionar a assinatura e conta do Azure corretas a usar com o PowerShell do Azure.

1. Add-AzureAccount - Use a ID de usuário e senha corretas
2. Get-AzureSubscription - Exiba todas as assinaturas para a conta. 
3. Select-AzureSubscription <subscription name> - Selecione a assinatura correta. Use a mesma que você usa para criar um data factory no Portal do Azure.

## Problema: falha ao inicializar a Instalação Expressa de Gateway de Dados no Portal Clássico do Azure
A instalação expressa do Gateway de dados requer o Internet Explorer ou um navegador da Web compatível com Microsoft ClickOnce. Se você não conseguir iniciar a Instalação expressa, você pode:

1. Alterne para o Internet Explorer se falhar com outros navegadores. Ou
2. Use os links de "Instalação Manual" mostrados na mesma folha no portal para fazer a instalação e, em seguida, copie a chave fornecida na tela e cole quando a configuração do Gateway de gerenciamento de dados estiver pronta. Se ele não iniciar, procure "Gateway de gerenciamento de dados Microsoft" em seu menu Iniciar e cole a chave quando ele for iniciado. 


## Problema: falha ao iniciar o Gerenciador de Credenciais no Portal Clássico do Azure
Ao configurar ou atualizar um Serviço Vinculado do SQL Server por meio do Portal Clássico do Azure, o aplicativo Gerenciador de Credenciais será iniciado para garantir a segurança. Ele requer o Internet Explorer ou um navegador da Web compatível com Microsoft ClickOnce. Você pode alternar para o Internet Explorer se falhar com outros navegadores.

## Problema: falha ao se conectar ao SQL Server local 
Verifique se o SQL Server está acessível por meio do computador em que o gateway está instalado. No computador no qual o gateway está instalado, você pode:

1. Executar ping do computador onde o SQL Server está instalado. Ou
2. Tente se conectar à instância do SQL Server usando as credenciais especificadas por você no Portal Clássico do Azure usando o SSMS (SQL Server Management Studio).


## Problema: Fatias de entrada estão permanentemente no estado PendingExecution ou PendingValidation

As fatias poderiam estar no estado **PendingExecution** ou **PendingValidation** devido a vários motivos, e um dos mais comuns é que a propriedade **external** não é especificada como **true**. Qualquer conjunto de dados que é produzido fora do escopo da Azure Data Factory deve ser marcado com a propriedade **external**. Isso indica que os dados são externos e não têm suporte por quaisquer pipelines dentro da data factory. As fatias de dados são marcadas como **Pronto** depois que os dados estão disponíveis no respectivo armazenamento.

Consulte o exemplo a seguir para o uso da propriedade **external**. Como opção, você pode especificar **externalData*** quando definir external como true.

Consulte o tópico Tabelas na [Referência de script JSON][json-scripting-reference] para obter mais detalhes sobre essa propriedade.
	
	{
	  "name": "CustomerTable",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "MyLinkedService",
	    "typeProperties": {
	      "folderPath": "MyContainer/MySubFolder/",
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    },
	    "policy": {
	      "externalData": {
	        "dataDelay": "00:10:00",
	        "retryInterval": "00:01:00",
	        "retryTimeout": "00:10:00",
	        "maximumRetry": 3
	      }
	    }
	  }
	}

 Para resolver o erro, adicione a propriedade **external** e a seção opcional **externalData** à definição de JSON da tabela de entrada e recrie a tabela.

## Problema: Falha na operação de cópia híbrida
Para obter mais detalhes:

1. Inicie o Gerenciador de configuração de gateway de gerenciamento de dados no computador no qual o gateway foi instalado. Verifique se o **Nome do gateway** está definido como o nome lógico do gateway no **Portal Clássico do Azure**, se o **Status da chave do gateway** é **registrado** e se o **Status do serviço** é **Iniciado**. 
2. Inicie o **Visualizador de Eventos**. Expanda **Logs de aplicativos e serviços** e clique em **Gateway de gerenciamento de dados**. Verifique se há erros relacionados ao Gateway de Gerenciamento de Dados. 

## Problema: Falha no provisionamento sob demanda do HDInsight com erro

Ao usar um serviço vinculado do tipo HDInsightOnDemandLinkedService, você deve especificar um linkedServiceName que aponta para o armazenamento de Blob do Azure. Essa conta de armazenamento será usada para copiar todos os logs e arquivos de suporte para o cluster HDInsight sob demanda. Às vezes, a atividade que faz o provisionamento sob demanda no HDInsight pode falhar com o seguinte erro:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Esse erro normalmente indica que o local da conta de armazenamento especificado no linkedServiceName não está no mesmo local de datacenter onde o provisionamento do HDInsight está ocorrendo. Por exemplo, se o local da Azure Data Factory é Oeste dos EUA e o provisionamento do HDInsight sob demanda acontece no Oeste dos EUA, mas o local de conta de armazenamento de Blobs do Azure é definido como Leste dos EUA, o provisionamento sob demanda falhará.

Além disso, há uma segunda propriedade JSON additionalLinkedServiceNames, em que as contas de armazenamento adicionais podem ser especificadas no HDInsight sob demanda. Essas contas de armazenamento adicionais vinculadas devem estar no mesmo local que o cluster HDInsight, ou falharão com o mesmo erro.



## Problema: Falha de atividade personalizada
Ao usar uma atividade personalizada na Azure Data Factory (tipo de atividade de pipeline CustomActivity), o aplicativo personalizado é executado no serviço vinculado especificado para HDInsight como um trabalho MapReduce de streaming apenas de mapeamento.

Quando a atividade personalizada é executada, a Azure Data Factory poderá capturar essa saída do cluster HDInsight e salvá-la no contêiner de armazenamento *adfjobs* em sua conta de armazenamento de Blobs do Azure. Em caso de erro, você pode ler o texto do arquivo de texto de saída **stderr** após a ocorrência de uma falha. Os arquivos podem ser acessados e lidos no próprio Portal Clássico do Azure no navegador da Web, ou usando ferramentas do gerenciador de armazenamento para acessar os arquivos mantidos no contêiner de armazenamento, diretamente no Armazenamento de Blobs do Azure.

Para enumerar e ler os logs para uma determinada atividade personalizada, você pode seguir uma das explicações passo a passo ilustradas mais adiante nesta página. Em resumo:

1.  No Portal Clássico do Azure, use **Procurar** para localizar o Data Factory.
2.  Use o botão **Diagrama** para exibir o diagrama de data factory e, em seguida, clique na tabela **Conjunto de dados** que segue o **Pipeline** específico que tem a atividade personalizada. 
3.  Na folha **Tabela**, clique na fatia de interesse nas **Fatias com problema** para o intervalo de tempo a ser investigado.
4.  A folha detalhada **Fatia de dados** será exibida e pode listar várias **Execuções de atividade** para a fatia. Clique em uma **Atividade** da lista. 
5.  A folha **Detalhes de execução da atividade** será exibida. Ela listará a **Mensagem de erro** no meio da folha, além de vários **Arquivos de log** listados na parte inferior da folha afiliada a essa execução de atividade.
	- Logs/system-0.log
	- Status
	- Status/exit
	- Status/stderr
	- Status/stdout

6. Clique no primeiro item de **Arquivo de log** na lista e o log será aberto em uma nova folha com o texto completo exibido para a leitura. Examine o texto de cada log clicando em cada um deles. A folha de visualizador de texto será aberta. Você pode clicar no botão **Download** para baixar o arquivo de texto para exibição offline opcional.

Um **erro comum** de uma atividade personalizada é Execução do pacote falhou com código de saída “1”. Consulte “wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/Status/stderr“ para obter mais detalhes.

Para ver mais detalhes sobre esse tipo de erro, abra o arquivo **stderr**. Um erro comum visto lá é uma condição de tempo limite como esta: INFO mapreduce.Job: Task Id : attempt\_1424212573646\_0168\_m\_000000\_0, Status : FAILED AttemptID:attempt\_1424212573646\_0168\_m\_000000\_0 expirou após 600 s

Esse mesmo erro pode aparecer várias vezes, se foram realizadas 3 tentativas para o trabalho por exemplo, ao longo do período de 30 minutos ou mais.

Esse erro de tempo limite indica que o tempo limite de 600 segundos (10 minutos) foi atingido. Geralmente isso significa que o aplicativo .Net personalizado não emitiu nenhuma atualização de status por 10 minutos. Se o aplicativo está pendente ou interrompida aguardando por algo por muito tempo, o período de tempo limite de 10 minutos é um mecanismo de segurança para impedi-lo de esperar para sempre e atrasar o pipeline da Azure Data Factory.

Esse tempo limite se origina na configuração do cluster HDInsight que está vinculado à atividade personalizada. A configuração é **mapred.task.timeout**, cujo padrão é 600.000 milissegundos, conforme documentado nas configurações Apache padrão descritas aqui: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Você pode substituir esse padrão alterando os padrões no momento do provisionamento do seu cluster de provisionamento do HDInsight. Ao usar a Azure Data Factory e o serviço vinculado **HDInsight sob demanda**, a propriedade JSON pode ser adicionada perto de suas propriedades JSON HDInsightOnDemandLinkedService. Por exemplo, você pode aumentar o valor para 20 minutos usando essa propriedade JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Para mais contexto e um exemplo completo de JSON para editar essas propriedades de configuração de redução do mapa, consulte o Exemplo 3 na documentação do MSDN aqui https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problema: Falha na solicitação do PowerShell com erro - solicitação inválida com erro 400 - "Nenhum provedor de recurso registrado encontrado..."

A partir de 10 de março de 2015, as versões de visualização privada anteriores do PowerShell da Azure Data Factory 2014-05-01-preview, 2014-07-01-preview e 2014-08-01-preview serão descontinuadas. Recomendamos que você use a versão mais recente dos cmdlets ADF, que agora fazem parte do Download do PowerShell do Azure, como o download dessa URL http://go.microsoft.com/?linkid=9811175&clcid=0x409

Se você usar as versões descontinuadas do SDK do PowerShell do Azure, poderá receber os seguintes erros:

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


## <a name="copywalkthrough"></a> Passo a passo: Solucionando problemas de um erro na cópia de dados
Neste passo a passo, você apresentará um erro no tutorial do artigo Introdução ao Data Factory e aprenderá a usar o Portal Clássico do Azure para solucionar o erro.

### Pré-requisitos
1. Conclua o tutorial no artigo [Introdução à Azure Data Factory][adfgetstarted].
2. Confirme que o **ADFTutorialDataFactory** produz dados na tabela **emp** no Banco de Dados SQL do Azure.  
3. Agora, exclua a tabela **emp** (**drop table emp**) do Banco de Dados SQL do Azure. Isso apresentará um erro.
4. Execute o seguinte comando no **PowerShell do Azure** para atualizar o período ativo para o pipeline de forma que ele tente gravar dados na tabela **emp**, que não existe mais.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	Substitua o valor **StartDateTime** pelo dia atual e o valor **EndDateTime** pelo dia seguinte.


### Usar o Portal do Azure para solucionar o erro

1.	Faça logon no [Portal do Azure][azure-portal]. 
2.	Clique em **ADFTutorialDataFactory** no **Quadro Inicial**. Se você não vir o link da data factory no **Quadro Inicial**, clique no hub **PROCURAR** e clique em **Tudo**. Clique em **Data factories…** na folha **Procurar** e clique em **ADFTutorialDataFactory**.
3.	Observe que você verá **Com erros** no bloco **Conjuntos de dados**. Clique em **Com erros**. Você deve ver a folha **Conjuntos de dados com erros**.

	![Link Data Factory com erros][image-data-factory-troubleshoot-with-error-link]

4. Na folha **Conjuntos de dados** com erros, clique em **EmpSQLTable** para ver a folha **TABELA**.

	![Folha Conjuntos de dados com erros][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. Na folha **TABELA**, você verá as fatias do problema, ou seja, as fatias com um erro na lista **Fatias com problema** na parte inferior. Você também pode ver as fatias recentes com erros na lista **Fatias recentes**. Clique em uma fatia na lista **Fatias com problema**.

	![Folha Tabela com fatias com problema][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Se você clicar em **Fatias com problema** (não em um problema específico), verá a folha **FATIAS DE DADOS** e, em seguida, clique em uma **fatia do problema específico** para ver o slide **FATIA DE DADOS** da fatia de dados selecionada.

6. Na folha **FATIA DE DADOS** para **EmpSQLTable**, você verá todas as **execuções de atividade** para a fatia na lista na parte inferior. Clique em uma **execução de atividade** na lista com falha.

	![Folha Fatia de dados com execuções ativas][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. Na folha **Detalhes de Execução da Atividade** da execução de atividade selecionada, você verá detalhes sobre o erro. Nesse cenário, você deve ver: **Nome de objeto inválido “emp”**.

	![Detalhes da execução de atividade com um erro][image-data-factory-troubleshoot-activity-run-with-error]

Para resolver esse problema, crie a tabela **emp** usando o script SQL do artigo [Introdução à Data Factory][adfgetstarted].


### Usar cmdlets do PowerShell do Azure para solucionar o erro
1.	Inicie o **PowerShell do Azure**. 
2.	Alterne para o modo **AzureResourceManager**, pois os cmdlets da Data Factory estão disponíveis somente nesse modo.

         
		switch-azuremode AzureResourceManager

3. Execute o comando Get-AzureDataFactorySlice para ver as fatias e seus status. Você deve ver uma fatia com o status: Falha.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	Substitua **StartDateTime** pelo valor StartDateTime especificado para **Set-AzureDataFactoryPipelineActivePeriod**.

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

 

## <a name="pighivewalkthrough"></a> Passo a passo: Solucionando problemas de um erro no processamento do Hive/Pig
Este passo a passo fornece as etapas para solucionar problemas de erro no processamento do Hive/Pig usando o Portal do Azure e o Azure PowerShell.


### Passo a passo: usar o Portal Clássico do Azure para solucionar problemas de um erro no processamento do Pig/Hive
Nesse cenário, o conjunto de dados está em um estado de erro devido a uma falha no processamento do Hive em um cluster HDInsight.

1. Clique em **Com erros** no bloco **Conjuntos de Dados** na página inicial **DATA FACTORY**.

	![Link Com erros no bloco Conjuntos de dados][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. Na folha **Conjuntos de dados com erros**, clique na **tabela** em que você está interessado.

	![Folha Conjuntos de dados com erros][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. Na folha **TABELA**, clique na **fatia com problema** com **STATUS** definido como **Falha**.

	![Tabela com fatias com problema][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. Na folha **FATIA DE DADOS,** clique na **Execução de Atividade** com falha.

	![Fatia de dados com execuções com falha][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. Na folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**, você pode baixar os arquivos associados ao processamento do HDInsight. Clique em **Download** para que **Status/stderr** baixe o arquivo de log de erros que contém detalhes sobre o erro.

	![Detalhes da execução de atividade com link para download][image-data-factory-troubleshoot-activity-run-details]

    
### Passo a passo: Usar o PowerShell do Azure para solucionar problemas de um erro no processamento do Pig/Hive
1.	Inicie o **PowerShell do Azure**. 
2.	Alterne para o modo **AzureResourceManager**, pois os cmdlets da Data Factory estão disponíveis somente nesse modo.

         
		switch-azuremode AzureResourceManager

3. Execute o comando Get-AzureDataFactorySlice para ver as fatias e seus status. Você deve ver uma fatia com o status: Falha.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Substitua **StartDateTime** pelo valor StartDateTime especificado para **Set-AzureDataFactoryPipelineActivePeriod**.

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



[adfgetstarted]: data-factory-get-started.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-portal]: https://portal.azure.com/

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
 

<!---HONumber=AcomDC_1203_2015-->