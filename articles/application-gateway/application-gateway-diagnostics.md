<properties 
   pageTitle="Monitorar logs de acesso e de desempenho para o Application Gateway | Microsoft Azure"
   description="Saiba como habilitar e gerenciar logs de acesso e de desempenho para o Application Gateway"
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/11/2016"
   ms.author="amitsriva" />

#Log de diagnóstico do Application Gateway

Você pode usar tipos diferentes de logs no Azure para gerenciar e solucionar problemas de Application Gateways. Alguns desses logs podem ser acessados por meio do portal, e todos os logs podem ser extraídos de um armazenamento de blob do Azure e exibidos em diferentes ferramentas, como o Excel e o PowerBI. Você pode saber mais sobre os diferentes tipos de logs na lista abaixo.

- **Logs de auditoria:** você pode usar os [Logs de Auditoria do Azure](../azure-portal/insights-debugging-with-events.md) (anteriormente conhecidos como Logs Operacionais) para exibir todas as operações que estão sendo enviadas à(s) sua(s) assinatura(s) do Azure, bem como seu status. Os logs de auditoria são habilitados por padrão e podem ser exibidos no portal de visualização do Azure.
- **Logs de acesso:** você pode usar esse log para exibir o padrão de acesso do Application Gateway e analisar informações importantes, incluindo o IP do chamador, a URL solicitada, a latência de resposta, o código de retorno, os bytes de entrada e de saída. O log de acesso é coletado a cada 300 segundos. Esse log contém um registro por instância do Application Gateway. A instância do Application Gateway pode ser identificada pela propriedade “instanceId”.
- **Logs de desempenho:** você pode usar esse log para exibir o desempenho das instâncias do Application Gateway. Esse log captura informações sobre o desempenho por instância, incluindo a solicitação total atendida, a taxa de transferência em bytes, o total de solicitações atendidas, a contagem de solicitações com falha, a contagem de instâncias de back-end íntegras ou não. O log de desempenho é coletado a cada 60 segundos.

>[AZURE.WARNING] Os logs estão disponíveis apenas para os recursos implantados no modelo de implantação do Gerenciador de Recursos. Você não pode usar logs para recursos do modelo de implantação clássico. Para obter uma melhor compreensão dos dois modelos, consulte o artigo [Noções básicas sobre a implantação do Gerenciador de Recursos e a implantação clássica](../resource-manager-deployment-model.md).

##Habilitar o registro em log
O log de auditoria é sempre habilitado automaticamente para todos os recursos do Gerenciador de Recursos. Você precisa habilitar o log de acesso e de desempenho para começar a coletar os dados disponíveis por meio desses logs. Para habilitar os logs, siga as etapas abaixo.

1. Observe a ID do Recurso da conta de armazenamento onde os dados de log serão armazenados. Isso deverá estar no formato: /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>. Todas as contas de armazenamento da assinatura podem ser usadas. Você pode usar o portal de visualização para encontrar essas informações.
![Portal de visualização - diagnóstico do Application Gateway](./media/application-gateway-diagnostics/diagnostics1.png)
 
2. Observe a ID do Recurso do Application Gateway para o qual o log deve ser habilitado. Isso deverá estar no formato: /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>. Você pode usar o portal de visualização para encontrar essas informações.
![Portal de visualização - diagnóstico do Application Gateway](./media/application-gateway-diagnostics/diagnostics2.png)

3. Habilite o log de diagnóstico usando o cmdlet do powershell a seguir.

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] Os logs de auditoria não exigem uma conta de armazenamento separada. O uso do armazenamento para logs de acesso e de desempenho gerará encargos de serviço.


## Log de auditoria
Por padrão, esse log (anteriormente conhecido como "log operacional") é gerado pelo Azure. Os logs são preservados por 90 dias no repositório de Logs de Eventos do Azure. Saiba mais sobre esses logs lendo o artigo [Exibir logs de eventos e de auditoria](../azure-portal/insights-debugging-with-events.md).

## Log de acesso
Esse log só será gerado se você o tiver habilitado por Application Gateway, como detalhado acima. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Cada acesso do Application Gateway é registrado em log no formato JSON, conforme mostrado abaixo.

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resoource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayAccess",
		"time": "2016-04-11T04:24:37Z",
		"category": "ApplicationGatewayAccessLog",
		"properties": {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIP":"37.186.113.170",
			"clientPort":"12345",
			"httpMethod":"HEAD",
			"requestUri":"/xyz/portal",
			"requestQuery":"",
			"userAgent":"-",
			"httpStatus":"200",
			"httpVersion":"HTTP/1.0",
			"receivedBytes":"27",
			"sentBytes":"202",
			"timeTaken":"359",
			"sslEnabled":"off"
		}
	}


## Log de desempenho
Esse log só será gerado se você o tiver habilitado por Application Gateway, como detalhado acima. Os dados são armazenados na conta de armazenamento especificada quando você habilitou o registro em log. Os seguintes dados são registrados em log:

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayPerformance",
		"time": "2016-04-09T00:00:00Z",
		"category": "ApplicationGatewayPerformanceLog",
		"properties": 
		{
			"instanceId":"ApplicationGatewayRole_IN_1",
			"healthyHostCount":"4",
			"unHealthyHostCount":"0",
			"requestCount":"185",
			"latency":"0",
			"failedRequestCount":"0",
			"throughput":"119427"
		}
	}

## Exibir e analisar o log de auditoria
Você pode exibir e analisar dados do log de auditoria usando qualquer um dos seguintes métodos:

- **Ferramentas do azure:** recupere informações dos logs de auditoria por meio do Azure PowerShell, a CLI (Interface de Linha de Comando) do Azure, a API REST do Azure ou o portal de visualização do Azure. Instruções passo a passo para cada método são detalhadas no artigo [Operações de auditoria com o Gerenciador de Recursos](../resource-group-audit.md).
- **Power BI:** se ainda não tiver uma conta do [Power BI](https://powerbi.microsoft.com/pricing), você poderá testá-lo gratuitamente. Usando o [Pacote de conteúdo dos Logs de Auditoria do Azure para Power BI](https://powerbi.microsoft.com/pt-BR/documentation/powerbi-content-pack-azure-audit-logs/), você pode analisar seus dados com painéis pré-configurados que você pode usar como estão ou personalizar.

## Exibir e analisar o log de eventos e de contador 
Você precisa se conectar à sua conta de armazenamento e recuperar as entradas de log JSON para logs de eventos e contador. Depois de baixar os arquivos JSON, você pode convertê-los em CSV e exibi-lo no Excel, no PowerBI ou em qualquer outra ferramenta de visualização de dados.

>[AZURE.TIP] Se estiver familiarizado com o Visual Studio e os conceitos básicos de alteração de valores de constantes e variáveis em C# , você poderá usar as [ferramentas de conversor de log](https://github.com/Azure-Samples/networking-dotnet-log-converter) disponíveis no Github.

## Próximas etapas

- Postagem de blog [Visualizar os logs de auditoria do Azure com o Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx).
- Postagem de blog [Exibir e analisar logs de auditoria do Azure no Power BI e muito mais](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/).

<!---HONumber=AcomDC_0413_2016-->