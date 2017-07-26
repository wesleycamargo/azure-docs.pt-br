---
title: "Introdução ao Azure Data Lake Store usando o SDK do Azure para Node.js | Microsoft Docs"
description: Saiba como usar o Node.js para trabalhar com contas do Data Lake Store e o sistema de arquivos.
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 2fee173c-69ae-4e1d-8773-48618cda9e16
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/06/2017
ms.author: nitinme
ms.translationtype: Human Translation
ms.sourcegitcommit: 09f24fa2b55d298cfbbf3de71334de579fbf2ecd
ms.openlocfilehash: 8c7a2e6ca061bbfa077592efb73d592906c3d070
ms.contentlocale: pt-br
ms.lasthandoff: 06/07/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-sdk-for-nodejs"></a>Introdução ao Azure Data Lake Store usando o SDK do Azure para Node.js
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [CLI 2.0 do Azure](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

> [!NOTE]
> Para carregar e baixar grandes quantidades de dados (arquivos grandes, um grande número de arquivos ou ambos), recomendamos o uso do [SDK do Python](data-lake-store-get-started-python.md), do [SDK do .NET](data-lake-store-get-started-net-sdk.md), da [CLI do Azure 2.0](data-lake-store-get-started-cli-2.0.md) ou do [Azure PowerShell](data-lake-store-get-started-powershell.md). Essas opções têm um desempenho melhor, pois usam vários threads para paralelizar a movimentação de dados.
> 
> 

Saiba como usar o SDK do Azure para Node.js para criar uma conta do Azure Data Lake Store e executar operações básicas, como criar pastas, carregar e baixar arquivos de dados, excluir sua conta, etc. Para saber mais sobre o Data Lake Store, veja [Visão geral do Data Lake Store](data-lake-store-overview.md). Atualmente, o SDK é compatível com

* **Versão do Node.js: 0.10.0 ou superior**
* **Versão da API REST para Conta: 2015-10-01-preview**
* **Versão da API REST para FileSystem: 2015-10-01-preview**

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Criar um aplicativo do Azure Active Directory**. Você pode usar o aplicativo Azure AD para autenticar o aplicativo Data Lake Store com o Azure AD. Há diferentes abordagens para autenticar com o Azure AD, que são a **autenticação de usuário final** ou a **autenticação serviço a serviço**. Para obter instruções e saber mais sobre como se autenticar, veja [Autenticação do usuário final](data-lake-store-end-user-authenticate-using-active-directory.md) ou [Autenticação de serviço a serviço](data-lake-store-authenticate-using-active-directory.md).

## <a name="how-to-install"></a>Como instalar
```bash
npm install azure-arm-datalake-store
```

## <a name="authenticate-using-azure-active-directory"></a>Autenticar usando o Azure Active Directory
Os trechos de código abaixo mostram duas maneiras diferentes de autenticação com o Data Lake Store usando o Azure AD. Para ver uma discussão detalhada sobre vários métodos de autenticação com o Data Lake Store, veja [Autenticar com o Data Lake Store usando o Azure Active Directory](data-lake-store-authenticate-using-active-directory.md).

O trecho abaixo também requer entradas, como nome de domínio do Azure AD, ID de cliente para um aplicativo do Azure AD, etc. Todos esses detalhes podem ser recuperados em um aplicativo do Azure AD que você deve ter criado, detalhes que também estão incluídos no link acima.

 ```javascript
 var msrestAzure = require('ms-rest-azure');
 //user authentication
 var credentials = new msRestAzure.UserTokenCredentials('your-client-id', 'your-domain', 'your-username', 'your-password', 'your-redirect-uri');
 //service principal authentication
 var credentials = new msRestAzure.ApplicationTokenCredentials('your-client-id', 'your-domain', 'your-secret');
 ```

## <a name="create-the-data-lake-store-clients"></a>Criar os clientes do Data Lake Store
```javascript
var adlsManagement = require("azure-arm-datalake-store");
var acccountClient = new adlsManagement.DataLakeStoreAccountClient(credentials, "your-subscription-id");
var filesystemClient = new adlsManagement.DataLakeStoreFileSystemClient(credentials);
```

## <a name="create-a-data-lake-store-account"></a>Criar uma conta do Repositório Data Lake
```javascript
var util = require('util');
var resourceGroupName = 'testrg';
var accountName = 'testadlsacct';
var location = 'eastus2';

// account object to create
var accountToCreate = {
  tags: {
    testtag1: 'testvalue1',
    testtag2: 'testvalue2'
  },
  name: accountName,
  location: location
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

## <a name="create-a-file-with-content"></a>Crie um arquivo com conteúdo
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var fileToCreate = '/myfolder/myfile.txt';
var options = {
  streamContents: new Buffer('some string content')
}

filesystemClient.fileSystem.listFileStatus(accountName, fileToCreate, options, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    // no result is returned, only a 201 response for success.
    console.log('response is: ' + util.inspect(response, {depth: null}));
  }
});
```

## <a name="get-a-list-of-files-and-folders"></a>Obter uma lista de arquivos e pastas
```javascript
var util = require('util');
var accountName = 'testadlsacct';
var pathToEnumerate = '/myfolder';
filesystemClient.fileSystem.listFileStatus(accountName, pathToEnumerate, function (err, result, request, response) {
  if (err) {
    console.log(err);
  } else {
    console.log('result is: ' + util.inspect(result, {depth: null}));
  }
});
```

## <a name="see-also"></a>Confira também
* [Microsoft Azure SDK para Node.js](https://github.com/azure/azure-sdk-for-node)
* [SDK do Microsoft Azure para Node.js - Gerenciamento da Análise de Data Lake](https://www.npmjs.com/package/azure-arm-datalake-analytics)


