<properties 
	pageTitle="Criar alertas em eventos do Azure Data Factory" 
	description="Saiba como você pode criar alertas para eventos gerados pelo Azure para operações do Data Factory." 
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
	ms.date="05/15/2015" 
	ms.author="spelluru"/>

# Criação de alertas em eventos do Azure
Eventos do Azure fornecem percepções úteis sobre o que está acontecendo em seus recursos do Azure. O Azure registra eventos do usuário quando um recurso do Azure (por exemplo, Data Factory) é criado, atualizado ou excluído. Ao usar o Azure Data Factory, os eventos são gerados quando:
 
1.	O Azure Data Factory é criado/atualizado/excluído.
2.	O processamento de dados (chamado de Execuções) foi iniciado/concluído.
3.	Quando um cluster de HDInsight sob demanda é criado e removido.

Você pode criar alertas para esses eventos de usuário e configurá-los para enviar notificações por email para o administrador e os coadministradores da assinatura. Além disso, você pode especificar endereços de email adicionais de usuários que precisem receber notificações por email quando as condições forem atendidas.

## Especificando uma definição de alerta
Para especificar uma definição de alerta, você deve criar um arquivo JSON que descreva as operações sobre as quais você deseja ser alertado. No exemplo abaixo, o alerta enviará uma notificação por email para a operação **RunFinished**. Para ser específico, uma notificação por email será enviado quando uma execução no Data Factory for concluída e essa execução falhar (Status = FailedExecution).

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

Consulte [operações disponíveis e status](#AvailableOperationsStatuses) para a lista de operações e status (e substatus).

## Implantando o Alerta
Para implantar o alerta, use o cmdlet do Azure PowerShell: **New-AzureResourceGroupDeployment**, conforme mostrado no exemplo a seguir:

	New-AzureResourceGroupDeployment -ResourceGroupName adf 	-TemplateFile .\ADFAlertFailedSlice.json -StorageAccountName testmetricsabc

O StorageAccountName especifica a conta de armazenamento para armazenar o arquivo JSON de alerta implantado.

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

## Recuperando a lista de implantações de grupo de recursos do Azure
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

## <a name="AvailableOperationsStatuses"></a>Valores de status e nomes de operação disponíveis

<table>
<th align="left">Nome de operação</th>
<th align="left">Status</th>
<th align="left">Sub Status</th>

<tr>
<td>RunStarted</td>
<td>Iniciado</td>
<td>Iniciando</td>
</tr>

<tr>
<td>RunFinished</td>
<td>Falhou / Bem-sucedido</td>
<td>
	<p>FailedResourceAllocation </p>
	<p>Bem-sucedido</p>
	<p>FailedExecution</p>
	<p>TimedOut</p>
	<p>Cancelado</p>
	<p>FailedValidation</p>
	<p>Abandonado</p>
</td>
</tr>

<tr>
<td>SliceOnTime</td>
<td>Em Andamento</td>
<td>OnTime</td>
</tr>

<tr>
<td>SliceDelayed</td>
<td>Em Andamento</td>
<td>Atrasado</td>
</tr>

<tr>
<td>OnDemandClusterCreateStarted</td>
<td>Iniciado</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterCreateSuccessful</td>
<td>Bem-sucedido</td>
<td></td>
</tr>

<tr>
<td>OnDemandClusterDeleted</td>
<td>Bem-sucedido</td>
<td></td>
</tr>

</table>

## Solucionando problemas de eventos de usuário
Execute o comando a seguir para ver os eventos gerados:

	Get-AzureResourceGroupLog –Name $ResourceGroup -All | Where-Object EventSource -eq "Microsoft.DataFactory"

<!---HONumber=GIT-SubDir--> 