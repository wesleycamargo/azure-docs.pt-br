---
title: Conectar-se à API de v3 de serviços de mídia do Azure - Node. js
description: Saiba como se conectar à API do serviços de mídia v3 com Node. js.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: juliako
ms.openlocfilehash: fbea3daec3d9ccac81f246d3dc762fc80cb4b18a
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759352"
---
# <a name="connect-to-media-services-v3-api---nodejs"></a>Conectar-se à API de v3 de serviços de mídia - Node. js

Este artigo mostra como conectar-se para o Azure Media Services v3 SDK Node. js usando o logon de entidade de serviço no método.

## <a name="prerequisites"></a>Pré-requisitos

- Instale o [Node.js](https://nodejs.org/en/download/).
- [Crie uma conta de Serviços de Mídia](create-account-cli-how-to.md). Certifique-se de lembrar o nome do grupo de recursos e o nome da conta dos serviços de mídia.

## <a name="create-packagejson"></a>Criar um Package. JSON

1. Crie um arquivo Package. JSON usando seu editor favorito.
1. Abra o arquivo e cole o seguinte código:

```json
{
  "name": "media-services-node-sample",
  "version": "0.1.0",
  "description": "",
  "main": "./index.js",
  "dependencies": {
    "azure-arm-mediaservices": "^4.1.0",
    "azure-storage": "^2.8.0",
    "ms-rest": "^2.3.3",
    "ms-rest-azure": "^2.5.5"
  }
}
```

Os seguintes pacotes devem ser especificados:

|Pacote|DESCRIÇÃO|
|---|---|
|`azure-arm-mediaservices`|SDK de serviços de mídia do Azure. <br/>Para verificar se você estiver usando o pacote de serviços de mídia do Azure mais recente, verifique [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/).|
|`azure-storage`|SDK de armazenamento. Usado quando o carregamento de arquivos em ativos.|
|`ms-rest-azure`| Usado para entrar.|

Você pode executar o comando a seguir para verificar se que você estiver usando o pacote mais recente:

```
npm install azure-arm-mediaservices
```

## <a name="connect-to-nodejs-client"></a>Conectar ao cliente do Node. js

1. Crie um arquivo. js usando seu editor favorito.
1. Abra o arquivo e cole o seguinte código.
1. Defina os valores na seção "configuração do ponto de extremidade" para valores você obteve [acessar as APIs](access-api-cli-how-to.md).

```js
'use strict';

const MediaServices = require('azure-arm-mediaservices');
const msRestAzure = require('ms-rest-azure');
const msRest = require('ms-rest');
const azureStorage = require('azure-storage');

// endpoint config
// make sure your URL values end with '/'
const armAadAudience = "";
const aadEndpoint = "";
const armEndpoint = "";
const subscriptionId = "";
const accountName = "";
const region = "";
const aadClientId = "";
const aadSecret = "";
const aadTenantId = "";
const resourceGroup = "";

let azureMediaServicesClient;

///////////////////////////////////////////
//     Entrypoint for sample script      //
///////////////////////////////////////////

msRestAzure.loginWithServicePrincipalSecret(aadClientId, aadSecret, aadTenantId, {
  environment: {
    activeDirectoryResourceId: armAadAudience,
    resourceManagerEndpointUrl: armEndpoint,
    activeDirectoryEndpointUrl: aadEndpoint
  }
}, async function(err, credentials, subscriptions) {
    if (err) return console.log(err);
    azureMediaServicesClient = new MediaServices(credentials, subscriptionId, armEndpoint, { noRetryPolicy: true });
    
    console.log("connected");

});
```

## <a name="run-your-app"></a>Executar seu aplicativo

Abra um prompt de comando. Navegue até o diretório do exemplo e execute os seguintes comandos:

```
npm install 
node index.js
```

## <a name="see-also"></a>Consulte também

[Referência do .NET](https://docs.microsoft.com/dotnet/api/overview/azure/mediaservices/management?view=azure-dotnet)

## <a name="next-steps"></a>Próximas etapas

- [Conceitos dos Serviços de Mídia](concepts-overview.md)
- [Início rápido]()
- [Referência do Node.js](https://docs.microsoft.com/javascript/api/azure-arm-mediaservices/?view=azure-node-latest)
- [NPM install azure-arm-mediaservices](https://www.npmjs.com/package/azure-arm-mediaservices/)<br>
