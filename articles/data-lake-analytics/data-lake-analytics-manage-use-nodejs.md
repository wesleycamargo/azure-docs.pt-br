<properties
   pageTitle="Gerenciar a Análise Azure Data Lake usando o SDK do Azure para Node.js | Azure"
   description="Saiba como gerenciar contas, fontes de dados, trabalhos e usuários da Análise Data Lake usando o SDK do Azure para Node.js"
   services="data-lake-analytics"
   documentationCenter=""
   authors="edmacauley"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Gerenciar a Análise Azure Data Lake usando o SDK do Azure para Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

O SDK do Azure para Node.js pode ser usado para gerenciar contas, trabalhos e catálogos da Análise Azure Data Lake. Para ver o tópico de gerenciamento usando outras ferramentas, clique na guia Selecionar acima.

Agora, ele oferece suporte a:

  *  **Versão do Node.js: 0.10.0 ou superior**
  *  **Versão da API REST para Conta: 2015-10-01-preview**
  *  **Versão da API REST para Catálogo: 2015-10-01-preview**
  *  **Versão da API REST para Trabalho: 2016-03-20-preview**

## Recursos

- Gerenciamento de contas: criar, obter, listar, atualizar e excluir.
- Gerenciamento de trabalhos: enviar, obter, listar, cancelar.
- Gerenciamento de catálogos: obter, listar, criar (segredos), atualizar (segredos), excluir (segredos).

## Como instalar

```bash
npm install azure-arm-datalake-analytics
```

## Autenticar usando o Azure Active Directory

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## Criar o cliente da Análise Data Lake

```javascript
var adlaManagement = require("azure-arm-datalake-analytics");
var acccountClient = new adlaManagement.DataLakeAnalyticsAccountClient(credentials, 'your-subscription-id');
var jobClient = new adlaManagement.DataLakeAnalyticsJobClient(credentials, 'azuredatalakeanalytics.net');
var catalogClient = new adlaManagement.DataLakeAnalyticsCatalogClient(credentials, 'azuredatalakeanalytics.net');
```

## Criar uma conta da Análise Data Lake

```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlaacct';
var location = 'eastus2';

// A Data Lake Store account must already have been created to create
// a Data Lake Analytics account. See the Data Lake Store readme for
// information on doing so. For now, we assume one exists already.
var datalakeStoreAccountName = 'existingadlsaccount';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location,
  properties: {
    defaultDataLakeStoreAccount: datalakeStoreAccountName,
    dataLakeStoreAccounts: [
      {
        name: datalakeStoreAccountName
      }
    ]
  }
};

client.account.create(resourceGroupName, accountName, accountToCreate, function (err, result, request, response) {
  if (err) {
    console.log(err);
    /*err has reference to the actual request and response, so you can see what was sent and received on the wire.
      The structure of err looks like this:
      err: {
        code: 'Error Code',
        message: 'Error Message',
        body: 'The response body if any',
        request: reference to a stripped version of http request
        response: reference to a stripped version of the response
      }
    */
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Obter uma lista de trabalhos

```javascript
var util = require('util');
var accountName = 'testadlaacct';
jobClient.job.list(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Obter uma lista de bancos de dados no Catálogo da Análise Data Lake
```javascript
var util = require('util');
var accountName = 'testadlaacct';
catalogClient.catalog.listDatabases(accountName, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## Confira também

- [Microsoft Azure SDK para Node.js](https://github.com/azure/azure-sdk-for-node)
- [SDK do Microsoft Azure para Node.js - Gerenciamento do Repositório Data Lake](https://github.com/Azure/azure-sdk-for-node/tree/autorest/lib/services/dataLake.Store)

<!---HONumber=AcomDC_0914_2016-->