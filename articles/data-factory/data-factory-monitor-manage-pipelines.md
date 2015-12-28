<properties 
	pageTitle="Monitorar e gerenciar pipelines do Azure Data Factory" 
	description="Saiba como usar o Portal Clássico do Azure e o Azure PowerShell para monitorar e gerenciar as data factories e os pipelines do Azure que você criou." 
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
	ms.date="10/20/2015" 
	ms.author="spelluru"/>

# Monitorar e gerenciar pipelines do Azure Data Factory
O serviço de Fábrica de Dados fornece uma exibição confiável e completa de seus serviços de armazenamento, processamento e movimentação de dados. Ele ajuda a avaliar rapidamente a integridade de dados de ponta a ponta do pipeline, identificar problemas e tomar uma ação corretiva, se necessário. Você também pode controlar visualmente a linhagem de dados e as relações entre os dados em qualquer uma de suas fontes e ver um histórico completo de execução de trabalhos, integridade do sistema e dependências em um único painel de monitoramento.

Este artigo descreve como monitorar, gerenciar e depurar seus pipelines. Ele também fornece informações sobre como criar alertas e ser notificado sobre falhas.

## Entenda os pipelines e os estados de atividade
Com o Portal do Azure, você pode exibir seu data factory como um diagrama, exibir atividades em um pipeline, exibir conjuntos de dados de entrada e saída e muito mais. Esta seção também explica como uma fatia faz a transição de um estado para outro estado.

### Navegue até sua data factory
1.	Entre no [Portal do Azure](http://portal.azure.com).
2.	Clique em **Procurar tudo** e selecione **Data factories**.
	
	![Procurar tudo -> fata Factories](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

	Você deve ver todas as data factories na folha **Data factories**. 
4. Na folha Data factories, selecione a data factory em que está interessado e você deverá ver a home page (folha **Data factory**) da data factory.

	![Folha Data factory](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### Modo de exibição de diagrama de uma data factory
O Modo de exibição de diagrama de uma data factory fornece um único painel onde você pode monitorar e gerenciar a data factory e seus ativos.

Clique em **Diagrama** na home page da data factory acima para ver a exibição de diagrama de sua data factory.

![Exibição de diagrama](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

É possível ampliar, reduzir, aplicar zoom de 100%, ajustar nível de zoom, bloquear o layout do diagrama, posicionar pipelines e tabelas automaticamente, bem como mostrar informações de linhagem (exibe itens upstream e downstream dos itens selecionados).
 

### Atividades dentro de um pipeline 
1. Clique com o botão direito no pipeline e clique em **Abrir pipeline** para ver todas as atividades no pipeline junto com conjuntos de dados de entrada e saída para as atividades. Isso é útil quando o pipeline é composto de mais de uma atividade e você deseja compreender a linhagem operacional de um único pipeline.

	![Menu do pipeline aberto](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)	 
2. No exemplo a seguir, você vê duas atividades no pipeline com suas entradas e saídas. A atividade denominada **JoinData**, do tipo atividade de Hive do HDInsight, e **EgressDataAzure**, do tipo atividade de cópia, estão neste pipeline de exemplo. 
	
	![Atividades dentro de um pipeline](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png) 
3. Você pode navegar de volta para a home page da Data Factory clicando no link da Data factory trilha no canto superior esquerdo.

	![Navegue de volta para a data factory](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### Exibir o estado de cada atividade dentro de um pipeline
Você pode exibir o estado atual de uma atividade exibindo o status de qualquer um dos conjuntos de dados produzidos pela atividade.

Por exemplo: no exemplo a seguir, **BlobPartitionHiveActivity** foi executado com êxito e produziu um conjunto de dados chamado **PartitionedProductsUsageTable**, que está no estado **Pronto**.

![Estado do pipeline](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

Clicar duas vezes em **PartitionedProductsUsageTable** no diagrama exibirá todas as fatias produzidas por diferentes execuções de atividade dentro de um pipeline. Você pode ver que **BlobPartitionHiveActivity** foi executado com êxito todos os meses nos últimos 8 meses e produziu as fatias com estado **Pronto**.

As fatias de conjunto de dados na data factory podem ter um dos seguintes status:

<table>
<tr>
	<th align="left">Estado</th><th align="left">Subestado</th><th align="left">Descrição</th>
</tr>
<tr>
	<td rowspan="8">Aguardando</td><td>ScheduleTime</td><td>Não é o momento de a fatia executar.</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>As dependências de upstream não estão prontas.</td>
</tr>
<tr>
<td>ComputeResources</td><td>Os recursos de computação não estão disponíveis.</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>Todas as instâncias de atividade estão ocupadas executando outras fatias.</td>
</tr>
<tr>
<td>ActivityResume</td><td>A atividade é pausada e não é possível executar as fatias até que seja retomada.</td>
</tr>
<tr>
<td>Retry</td><td>A execução da atividade será repetida.</td>
</tr>
<tr>
<td>Validação</td><td>A validação ainda não foi iniciada.</td>
</tr>
<tr>
<td>ValidationRetry</td><td>Aguardando a validação ser repetida.</td>
</tr>
<tr>
<td rowspan="2">InProgress</td><td>Validando</td><td>Validação em andamento.</td>
</tr>
<td></td>
<td>A fatia está sendo processada.</td>
</tr>
<tr>
<td rowspan="4">Falha</td><td>TimedOut</td><td>A execução demorou mais do que o permitido pela atividade.</td>
</tr>
<tr>
<td>Cancelado</td><td>Cancelado por ação do usuário.</td>
</tr>
<tr>
<td>Validação</td><td>A validação falhou.</td>
</tr>
<tr>
<td></td><td>Falha ao gerar e/ou validar a fatia.</td>
</tr>
<td>Ready</td><td></td><td>A fatia está pronta para consumo.</td>
</tr>
<tr>
<td>Ignorado</td><td></td><td>A fatia não é processada.</td>
</tr>
<tr>
<td>Nenhum</td><td></td><td>Uma fatia que costumava existir com um status diferente, mas foi redefinida.</td>
</tr>
</table>



Você pode exibir os detalhes de uma fatia clicando em uma entrada de fatia na folha **Fatias Atualizadas Recentemente**.

![Detalhes da fatia](./media/data-factory-monitor-manage-pipelines/slice-details.png)
 
Se a fatia tiver sido executada várias vezes, você verá várias linhas na lista **Execuções de atividade**.

![Execuções de atividade de uma fatia](./media/data-factory-monitor-manage-pipelines/activity-runs-for-a-slice.png)

Você pode exibir detalhes sobre uma execução de atividade clicando na entrada da execução na lista **Execuções de atividade**. Isso exibirá todos os arquivos de log junto com uma mensagem de erro, se houver. Isso é muito útil para exibir e depurar logs sem precisar sair de sua data factory.

![Detalhes da execução da atividade](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

Se a fatia não estiver no estado **Pronto**, você poderá ver as fatias de upstream que não estão prontas e que estão impedindo a execução da fatia atual na lista **Fatias de upstream que não estão prontas**. Isso é bastante útil quando a fatia está no estado **Aguardando** e você deseja entender as dependências de upstream nas quais a fatia está aguardando.

![As fatias upstream não estão prontas](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### Diagrama de estado do conjunto de dados
Quando você implanta uma data factory e os pipelines têm um período de atividade válido, as fatias do conjunto de dados fatias fazem a transição de um estado para outro. Atualmente, o status da fatia segue o seguinte diagrama de estado:

![Diagrama de estado](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

O fluxo de transição de estado do conjunto de dados na data factory envolve o seguinte: Waiting-> In-Progress/In-Progress (Validating) -> Ready/Failed

As fatias começam com um estado **Aguardando** para que as pré-condições sejam atendidas antes da execução. Depois disso, a atividade começa a ser executada e a fatia passa para o estado **Em Andamento**. A execução da atividade pode ser bem-sucedida ou falhar e, com base nisso, a fatia passará para o estado **Pronto** ou **Com falha**.

O usuário pode redefinir a fatia para voltar do estado **Pronto** ou **Com falha** para o estado **Aguardando**. O usuário também pode marcar o estado da fatia como **Ignorar**, o que impedirá a execução da atividade e não processará a fatia.


## Gerenciar pipelines
Você pode gerenciar seus pipelines usando o Azure PowerShell. Por exemplo, você pode pausar e retomar pipelines executando cmdlets do Azure PowerShell.

### Pausar e retomar pipelines
Você pode pausar/suspender pipelines usando o cmdlet **Suspend-AzureDataFactoryPipeline** do Powershell. Isso é útil quando você já descobriu um problema com os dados e não quer executar o pipeline para processar dados até que o problema seja corrigido.

Por exemplo: na captura de tela abaixo, foi identificado um problema com **PartitionProductsUsagePipeline** no data factory **productrecgamalbox1dev** e queremos suspender o pipeline.

![Pipeline a ser suspenso](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

Execute o comando do PowerShell a seguir para suspender **PartitionProductsUsagePipeline**.

	Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Por exemplo:

	Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 

Depois que o problema com **PartitionProductsUsagePipeline** for corrigido, o pipeline suspenso poderá ser retomado executando o comando do PowerShell abaixo.

	Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>

Por exemplo:

	Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline 


## Depurar pipelines
O Azure Data Factory fornece recursos avançados por meio do Portal Clássico do Azure e do Azure PowerShell para depurar e solucionar problemas com pipelines.

### Localizar erros em um pipeline
Se a execução da atividade falhar em um pipeline, o conjunto de dados produzido pelo pipeline ficará em um estado de erro devido à falha. Você pode depurar e solucionar e erros no Azure Data Factory usando os seguintes mecanismos.

#### Use o Portal Clássico do Azure para depurar um erro:

1.	Clique em **Com erros** no bloco **Conjuntos de Dados** na home page do data factory.
	
	![Bloco de conjuntos de dados com erro](./media/data-factory-monitor-manage-pipelines/datasets-tile-with-errors.png)
2.	Na folha **Conjuntos de dados com erros**, clique na tabela em que você está interessado.

	![Folha Conjuntos de dados com erros](./media/data-factory-monitor-manage-pipelines/datasets-with-errors-blade.png)
3.	Na folha **TABELA**, clique na fatia de problema com o **STATUS** definido como **Com falha**.

	![Folha de tabela com fatia com problema](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
4.	Na folha **FATIA DE DADOS,** clique na execução de atividade com falha.
	
	![Fatia de dados com erro](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
5.	Na folha **DETALHES DE EXECUÇÃO DA ATIVIDADE**, você pode baixar os arquivos associados ao processamento do HDInsight. Clique em Download para que Status/stderr baixe o arquivo de log de erros que contém detalhes sobre o erro.

	![Folha de detalhes da execução da atividade com erro](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)

#### Usar o PowerShell para depurar um erro
1.	Inicie o **PowerShell do Azure**.
2.	Alterne para o modo **AzureResourceManager**, pois os cmdlets da Data Factory estão disponíveis somente nesse modo.

		switch-azuremode AzureResourceManager
3.	Execute o comando **Get-AzureDataFactorySlice** para ver as fatias e seus status. Você deve ver uma fatia com o status: **Falha**.

		Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Por exemplo:


		Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	Substitua **StartDateTime** pelo valor StartDateTime especificado para Set-AzureRmDataFactoryPipelineActivePeriod.
4. Agora, execute o cmdlet **Get-AzureRmDataFactoryRun** para obter detalhes sobre a execução da atividade para a fatia.

		Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-
		DataFactoryName] <String> [-TableName] <String> [-StartDateTime] 
		<DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
	
	Por exemplo:


		Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	O valor de StartDateTime é a hora de início do erro/fatia com problema observada na etapa anterior. A data e hora devem ser colocadas entre aspas duplas.
5. 	Você deve ver a saída com detalhes sobre o erro (semelhante ao seguinte):

		Id                  	: 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   	: ADF
		DataFactoryName     	: LogProcessingFactory3
		TableName           	: EnrichedGameEventsTable
		ProcessingStartTime 	: 10/10/2014 3:04:52 AM
		ProcessingEndTime   	: 10/10/2014 3:06:49 AM
		PercentComplete     	: 0
		DataSliceStart      	: 5/5/2014 12:00:00 AM
		DataSliceEnd        	: 5/6/2014 12:00:00 AM
		Status              	: FailedExecution
		Timestamp           	: 10/10/2014 3:04:52 AM
		RetryAttempt        	: 0
		Properties          	: {}
		ErrorMessage        	: Pig script failed with exit code '5'. See wasb://		adfjobs@spestore.blob.core.windows.net/PigQuery
		                        		Jobs/841b77c9-d56c-48d1-99a3-
					8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
					more details.
		ActivityName        	: PigEnrichLogs
		PipelineName        	: EnrichGameLogsPipeline
		Type                	:
	
	
6. 	Você pode executar o cmdlet **Save-AzureRmDataFactoryLog** com o valor de Id que você vê na saída acima e baixar os arquivos de log usando a opção **-DownloadLogsoption** para o cmdlet.

	Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\\Test"


## Executar novamente as falhas em um pipeline

### Usando o Portal Clássico do Azure

Depois de solucionar problemas e depurar falhas em um pipeline, é possível executar as falhas novamente navegando até a fatia com erro e clicando no botão **Executar** na barra de comandos.

![Executar novamente uma fatia com falha](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

Em caso de falha na validação da fatia devido a uma falha de política (por exemplo, dados não disponíveis), é possível corrigir a falha e validar novamente clicando no botão **Validar** na barra de comandos. ![Corrigir os erros e validar](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### Usando o PowerShell do Azure

Você pode executar novamente as falhas usando o cmdlet 'Set-AzureRmDataFactorySliceStatus'.

	Set-AzureRmDataFactorySliceStatus [-ResourceGroupName] <String> [-DataFactoryName] <String> [-TableName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Status] <String> [[-UpdateType] <String> ] [-Profile <AzureProfile> ] [ <CommonParameters>]

**Exemplo:** o exemplo a seguir define o status de todas as fatias da tabela 'DAWikiAggregatedData' para 'PendingExecution' na Azure Data Factory 'WikiADF'.

**Observação:** o UpdateType é definido como UpstreamInPipeline, o que significa que o status de cada fatia da tabela e todas as tabelas dependentes (upstream) que são usadas como tabelas de entrada para as atividades no pipeline é definido como "PendingExecution". Outro valor possível para esse parâmetro é "Individual".

	Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -TableName DAWikiAggregatedData -Status PendingExecution -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00


## Criar alertas
O Azure registra eventos do usuário quando um recurso do Azure (por exemplo, Data Factory) é criado, atualizado ou excluído. Você pode criar alertas para esses eventos. O Data Factory permite que você capture várias métricas e crie alertas para as métricas. Recomendamos que você use os eventos para monitoramento em tempo real e métricas para fins de histórico.

### Alertas de eventos
Eventos do Azure fornecem percepções úteis sobre o que está acontecendo em seus recursos do Azure. O Azure registra eventos do usuário quando um recurso do Azure (por exemplo, Data Factory) é criado, atualizado ou excluído. Ao usar o Azure Data Factory, os eventos são gerados quando:

- O Azure Data Factory é criado/atualizado/excluído.
- O processamento de dados (chamado de Execuções) foi iniciado/concluído.
- Quando um cluster de HDInsight sob demanda é criado e removido.

Você pode criar alertas para esses eventos de usuário e configurá-los para enviar notificações por email para o administrador e os coadministradores da assinatura. Além disso, você pode especificar endereços de email adicionais de usuários que precisem receber notificações por email quando as condições forem atendidas. Isso é muito útil quando você deseja ser notificado sobre falhas e não quer monitorar continuamente sua data factory.

#### Especificando uma definição de alerta:
Para especificar uma definição de alerta, você deve criar um arquivo JSON que descreva as operações sobre as quais você deseja ser alertado. No exemplo abaixo, o alerta enviará uma notificação por email para a operação RunFinished. Para ser específico, uma notificação por email será enviado quando uma execução no Data Factory for concluída e essa execução falhar (Status = FailedExecution).

	{
	    "contentVersion": "1.0.0.0",
	     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters": {},
	    "resources": 
	    [
	        {
	            "name": "ADFAlertsSlice",
	            "type": "microsoft.insights/alertrules",
	            "apiVersion": "2014-04-01",
	            "location": "East US",
	            "properties": 
	            {
	                "name": "ADFAlertsSlice",
	                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
	                "isEnabled": true,
	                "condition": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
	                    "dataSource": 
	                    {
	                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
	                        "operationName": "RunFinished",
	                        "status": "Failed",
	                        "subStatus": "FailedExecution"   
	                    }
	                },
	                "action": 
	                {
	                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails": [ "<your alias>@contoso.com" ]
	                }
	            }
	        }
	    ]
	}

Da definição do JSON acima, **subStatus** pode ser removido se você não desejar ser alertado sobre uma falha específica.

O exemplo acima configura o alerta para todas as fábricas de dados em sua assinatura. Se você quiser que o alerta seja configurado para um data factory específico, é possível especificar o **resourceUri** do data factory no bloco **dataSource** como mostrado abaixo:

	"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"

A tabela a seguir fornece a lista de operações e status (e substatus) disponíveis.

Nome da operação | Status | Substatus
-------------- | ------ | ----------
RunStarted | Iniciado | Iniciando
RunFinished | Falhou / Bem-sucedido | <p>FailedResourceAllocation</p><p>Succeeded</p><p>FailedExecution</p><p>TimedOut</p><p><Canceled/p><p>FailedValidation</p><p>Abandoned</p>
OnDemandClusterCreateStarted | Iniciado
OnDemandClusterCreateSuccessful | Bem-sucedido
OnDemandClusterDeleted | Bem-sucedido

Veja [Criar Regra de Alerta](https://msdn.microsoft.com/library/azure/dn510366.aspx) para obter detalhes sobre os elementos JSON usados no exemplo acima.

#### Implantando o Alerta 
Para implantar o alerta, use o cmdlet do Azure PowerShell: **New-AzureResourceGroupDeployment**, conforme mostrado no exemplo a seguir:

	New-AzureResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  

Depois que a implantação do grupo de recursos for concluída com êxito, você verá as seguintes mensagens:

	VERBOSE: 7:00:48 PM - Template is valid.
	WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
	Please update scripts to remove this parameter.
	VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
	VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :

#### Recuperando a lista de implantações de grupo de recursos do Azure
Para recuperar a lista de implantações do grupo de recursos do Azure já implantadas, use o cmdlet **Get-AzureResourceGroupDeployment**, conforme mostrado no exemplo a seguir:

	Get-AzureResourceGroupDeployment -ResourceGroupName adf
	
	DeploymentName    : ADFAlertFailedSlice
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 10/11/2014 2:01:00 AM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           :


#### Solucionando problemas de eventos de usuário


- Você pode ver todos os eventos gerados depois de clicar no bloco **Operações**, e os alertas podem ser configurados em qualquer uma das operações visíveis na folha **Eventos**:

	![Operações](./media/data-factory-monitor-manage-pipelines/operations.png)


- Veja o artigo [Cmdlets do Azure Insight](https://msdn.microsoft.com/library/mt282452.aspx) sobre os cmdlets do PowerShell que você pode usar para adicionar/obter/remover alertas. Aqui estão alguns exemplos de como usar o cmdlet **Get-AlertRule**:


		PS C:\> get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
			
				Properties :
		        Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
		        Condition   :
				DataSource :
				EventName             :
				Category              :
				Level                 :
				OperationName         : RunFinished
				ResourceGroupName     :
				ResourceProviderName  :
				ResourceId            :
				Status                : Failed
				SubStatus             : FailedExecution
				Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
		        Condition  	:
				Description : One or more of the data slices for the Azure Data Factory has failed processing.
				Status      : Enabled
				Name:       : ADFAlertsSlice
				Tags       :
				$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
				Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
				Location   : West US
				Name       : ADFAlertsSlice
		
		PS C:\> Get-AlertRule -res $resourceGroup
	
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
				Location   : West US
				Name       : FailedExecutionRunsWest3
	
		PS C:\> Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
		
				Properties : Microsoft.Azure.Management.Insights.Models.Rule
				Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
				Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
				Location   : West US
				Name       : FailedExecutionRunsWest0

	Execute os seguintes comandos get-help para ver detalhes e exemplos para o cmdlet Get-AlertRule.

		get-help Get-AlertRule -detailed 
		get-help Get-AlertRule -examples


- Se você vir os eventos de geração de alerta na folha do portal, mas não receber notificações por email, verifique se o endereço de email especificado está configurado para receber emails de remetentes externos. Os emails de alerta podem ter sido bloqueados por suas configurações de email.

### Alertas de métricas
O Data Factory permite que você capture várias métricas e crie alertas para as métricas. Você pode monitorar e criar alertas nas seguintes métricas para as fatias em sua data factory.
 
- Execuções com falha
- Execuções com êxito

Essas métricas são muito úteis e permitem que os usuários tenham uma visão geral das execuções com falha e êxito em sua fábrica de dados. Métricas são emitidas sempre que há uma fatia de execução. Ao final de uma hora, essas métricas são agregadas e enviadas para sua conta de armazenamento. Portanto, para habilitar as métricas, você precisará configurar uma conta de armazenamento.

#### Habilitando métricas:
Para habilitar as métricas, clique no seguinte na folha do Data Factory:

**Monitoramento** -> **Métrica** -> **Configurações de diagnóstico** -> **Diagnóstico**

Na folha **Diagnóstico**, clique em **Ativar**, selecione a conta de armazenamento e salve.

![Habilitar a métrica](./media/data-factory-monitor-manage-pipelines/enable-metrics.png)

Uma vez salvas, pode levar uma hora para que as métricas fiquem visíveis na folha de monitoramento, porque a agregação de métricas acontece de hora em hora.


### Configurando alertas no Metrics:

Para configurar alertas no Metrics, siga esta ordem de cliques na folha Data Factory: **Monitoramento** -> **Métrica** -> **Adicionar alerta** -> **Adicionar uma regra de alerta**.

Preencha os detalhes da regra de alerta, especifique os emails e clique em **OK**.


![Configurando alertas em métricas](./media/data-factory-monitor-manage-pipelines/setting-up-alerts-on-metrics.png)

Quando terminar, você deverá ver uma nova regra de alerta habilitada no bloco de regras de Alerta, da seguinte forma:

![Regras de alerta habilitadas](./media/data-factory-monitor-manage-pipelines/alert-rule-enabled.png)

Parabéns! Você configurou o primeiro alerta de Métricas. Agora, você deve obter notificações sempre que a regra de alerta corresponder no intervalo de tempo determinado.

### Notificações de alerta:
Quando a regra configurada corresponder à condição, você deverá receber um email de alerta ativo. Depois que o problema for resolvido e a condição de alerta deixar de corresponder, você receberá um email de alerta resolvido.

Esse comportamento é diferente dos eventos, em que uma notificação será enviada em cada caso de falha para o qual a regra de alerta de qualificar.

### Implantando alertas usando o PowerShell
Você pode implantar alertas para métricas da mesma maneira como faz para eventos.

**Definição do alerta:**

	{
	    "contentVersion" : "1.0.0.0",
	    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
	    "parameters" : {},
	    "resources" : [
		{
	            "name" : "FailedRunsGreaterThan5",
	            "type" : "microsoft.insights/alertrules",
	            "apiVersion" : "2014-04-01",
	            "location" : "East US",
	            "properties" : {
	                "name" : "FailedRunsGreaterThan5",
	                "description" : "Failed Runs greater than 5",
	                "isEnabled" : true,
	                "condition" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
	                    "dataSource" : {
	                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
	                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
	                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
	>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
	                        "metricName" : "FailedRuns"
	                    },
	                    "threshold" : 5.0,
	                    "windowSize" : "PT3H",
	                    "timeAggregation" : "Total"
	                },
	                "action" : {
	                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
	                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
	                    "customEmails" : ["abhinav.gpt@live.com"]
	                }
	            }
	        }
	    ]
	}
 
Substitua subscriptionId, resourceGroupName e dataFactoryName no exemplo acima pelos valores adequados.

A partir de agora, *metricName* dá suporte a dois valores:
- FailedRuns
- SuccessfulRuns

**Implantando o alerta:**

Para implantar o alerta, use o cmdlet do Azure PowerShell: **New-AzureResourceGroupDeployment**, conforme mostrado no exemplo a seguir:

	New-AzureResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json

Você verá a seguinte mensagem após a implantação bem-sucedida:

	VERBOSE: 12:52:47 PM - Template is valid.
	VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
	VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded
	
	
	DeploymentName    : FailedRunsGreaterThan5
	ResourceGroupName : adf
	ProvisioningState : Succeeded
	Timestamp         : 7/27/2015 7:52:56 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	Outputs           

<!---HONumber=AcomDC_1217_2015-->