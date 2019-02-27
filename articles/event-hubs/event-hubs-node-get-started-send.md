---
title: Enviar eventos usando o Node.js – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo apresenta instruções passo a passo para a criação de um aplicativo Node.js que envia eventos de Hubs de Eventos do Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 02/19/2019
ms.author: spelluru
ms.openlocfilehash: ec3182d11f1b2ffa31acd05fa1f2db695f3f2cf7
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447711"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Enviar eventos para Hubs de Eventos do Azure usando Node.js

O Hubs de Eventos do Azure é uma plataforma de streaming de Big Data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como enviar eventos para um hub de eventos de um aplicativo escrito em Node.js.

> [!NOTE]
> Você pode baixar do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) este início rápido como um exemplo, substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos e executá-lo. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Versão do Node.js 8.x e superiores. Baixe a versão mais recente do LTS em [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE

## <a name="create-an-event-hubs-namespace-and-an-event-hub"></a>Criar um namespace de Hubs de Eventos e um hub de eventos
A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial.

Obtenha a cadeia de conexão para o namespace do hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no tutorial.

## <a name="clone-the-sample-git-repository"></a>Clonar o repositório Git de exemplo
Clone o repositório Git de exemplo do [GitHub](https://github.com/Azure/azure-event-hubs-node) em seu computador. 

## <a name="install-nodejs-package"></a>Instalar o pacote do Node.js
Instale o pacote de Node.js para Hubs de Eventos do Azure em seu computador. 

```shell
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Clonar o repositório Git
Baixe ou clone o [exemplo](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) do GitHub. 

## <a name="send-events"></a>Enviar eventos
O SDK que você clonou contém vários exemplos que mostram como enviar eventos a um hub de eventos usando o Node.js. Neste início rápido, você deve usar o exemplo **simpleSender.js**. Para observar os eventos que estão sendo recebidos, abra outro terminal e receba eventos usando [exemplo de recebimento](event-hubs-node-get-started-receive.md).

1. Abra o projeto no Visual Studio Code. 
2. Crie um arquivo chamado **.env** na pasta **cliente**. Copie e cole as variáveis de ambiente de exemplo do **sample.env** na pasta raiz.
3. Configure a cadeia de conexão do hub de eventos, o nome do hub de eventos e o ponto de extremidade de armazenamento. Para ver as instruções sobre como obter uma cadeia de conexão para um hub de eventos, confira [Obter cadeia de conexão](event-hubs-create.md#create-an-event-hubs-namespace).
4. Em sua CLI do Azure, navegue até o caminho da pasta do **cliente**. Instale os pacotes do Node e compile o projeto executando os comandos a seguir:

    ```shell
    npm i
    npm run build
    ```
5. Inicie o envio de eventos executando o seguinte comando: 

    ```shell
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Revisar o código de exemplo 
Examine o código de exemplo no arquivo simpleSender.js para enviar eventos para um hub de eventos.

```javascript
"use strict";
Object.defineProperty(exports, "__esModule", { value: true });
const lib_1 = require("../lib");
const dotenv = require("dotenv");
dotenv.config();
const connectionString = "EVENTHUB_CONNECTION_STRING";
const entityPath = "EVENTHUB_NAME";
const str = process.env[connectionString] || "";
const path = process.env[entityPath] || "";

async function main() {
    const client = lib_1.EventHubClient.createFromConnectionString(str, path);
    const data = {
        body: "Hello World!!"
    };
    const delivery = await client.send(data);
    console.log(">>> Sent the message successfully: ", delivery.tag.toString());
    console.log(delivery);
    console.log("Calling rhea-promise sender close directly. This should result in sender getting reconnected.");
    await Object.values(client._context.senders)[0]._sender.close();
    // await client.close();
}

main().catch((err) => {
    console.log("error: ", err);
});

```

Lembre-se de definir as variáveis de ambiente antes de executar o script. Você pode configurá-las na linha de comando, como mostrado no exemplo a seguir, ou usar o [pacote dotenv](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você enviou mensagens para um hub de eventos usando o Node.js. Para saber como receber eventos de um hub de eventos usando o Node.js, confira [Receber eventos de hub de eventos – Node.js](event-hubs-node-get-started-receive.md)

Confira outros exemplos de Node.js para Hubs de Eventos no [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
