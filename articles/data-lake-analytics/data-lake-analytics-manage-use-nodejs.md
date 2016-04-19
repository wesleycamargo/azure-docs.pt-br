<properties
   pageTitle="Gerenciar a Análise Azure Data Lake usando o SDK do Azure para Node.js | Azure"
   description="Saiba como gerenciar contas, fontes de dados, trabalhos e usuários da Análise Data Lake usando o SDK do Azure para Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Gerenciar a Análise Azure Data Lake usando o SDK do Azure para Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

O ADK do Azure para Node.js pode ser usado para gerenciar contas, trabalhos e catálogos da Análise Azure Data Lake. Para ver o tópico de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Uma conta da Análise Azure Data Lake**. Veja [Introdução à Análise Azure Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md) para criar uma conta.
- **Uma entidade de serviço com permissões para acessar a conta da Análise Data Lake**. Veja [Autenticando uma entidade de serviço com o Gerenciador de Recursos do Azure](../resource-group-authenticate-service-principal.md).

## Instalar o SDK

Use as seguintes etapas para instalar o SDK:

1. Instale o [Node.js](https://nodejs.org/).
2. Execute os seguintes comandos na janela do Prompt de Comando, Terminal ou Bash:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics

## Um exemplo do Node.js

O exemplo a seguir obtém a lista de trabalho de uma determinada conta da Análise Azure Data Lake.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');

	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'

	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';

	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';

	var accountName = 'adla_account_name';

	var context = new adalNode.AuthenticationContext(loginUri+tenantId);

	var client;
	var response;

	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});

			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');

			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##Consulte também

- [SDK do Azure para Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Visão geral da Análise do Microsoft Azure Data Lake](data-lake-analytics-overview.md)
- [Introdução à Análise do Data Lake usando o Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Gerenciar a Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorar e solucionar problemas em trabalhos da Análise do Azure Data Lake usando o Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_0413_2016-->