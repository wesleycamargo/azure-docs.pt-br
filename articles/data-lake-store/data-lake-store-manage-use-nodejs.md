<properties 
   pageTitle="Gerenciar Repositórios do Azure Data Lake usando o SDK do Azure para Node.js | Azure" 
   description="Saiba como gerenciar contas do Repositório Data Lake e o sistema de arquivos." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Gerenciar o Repositório Azure Data Lake usando o SDK do Azure para Node.js

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)


O ADK do Azure para Node.js pode ser usado para gerenciar contas de armazenamento e o sistema de arquivos do Azure Data Lake:

- Gerenciamento de contas: criar, obter, listar, atualizar e excluir.
- Gerenciamento do sistema de arquivos: criar, obter, carregar, acrescentar, baixar, ler, excluir e listar.

**Pré-requisitos**

Antes de começar este tutorial, você deve ter o seguinte:

- **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Uma conta do Repositório Azure Data Lake**. Veja [Introdução ao Repositório Azure Data Lake usando o Portal do Azure](data-lake-store-get-started-portal.md) para criar uma conta.
- **Uma entidade de serviço com permissões para acessar a conta da Análise Data Lake**. Veja [Autenticando uma entidade de serviço com o Gerenciador de Recursos do Azure](resource-group-authenticate-service-principal.md).

## Instalar o SDK

Use as seguintes etapas para instalar o SDK:

1. Instale o [Node.js](https://nodejs.org/).
2. Execute os seguintes comandos na janela do Prompt de Comando, Terminal ou Bash:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Um exemplo do Node.js

O exemplo a seguir cria um arquivo em uma conta do Repositório Data Lake e acrescenta dados a ela.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
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
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##Consulte também 

- [SDK do Azure para Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Gerenciar a Análise Azure Data Lake usando o Node.js](data-lake-analytics-use-nodejs.md)

<!---HONumber=AcomDC_1217_2015-->