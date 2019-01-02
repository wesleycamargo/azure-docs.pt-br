---
title: Enviar eventos usando o Node.js – Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo apresenta instruções passo a passo para a criação de um aplicativo Node.js que envia eventos de Hubs de Eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: c75d4404cb0892c3d90261af2fb4982ac84041c4
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 12/10/2018
ms.locfileid: "53163779"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Enviar eventos para Hubs de Eventos do Azure usando Node.js

Os Hubs de Eventos do Azure são uma plataforma de streaming de Big Data e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

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

```nodejs
npm install @azure/event-hubs
```

## <a name="clone-the-git-repository"></a>Clonar o repositório Git
Baixe ou clone o [exemplo](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) do GitHub. 

## <a name="send-events"></a>Enviar eventos
O SDK que você clonou contém vários exemplos que mostram como enviar eventos a um hub de eventos usando o Node.js. Neste início rápido, você deve usar o exemplo **simpleSender.js**. Para observar os eventos que estão sendo recebidos, abra outro terminal e receba eventos usando [exemplo de recebimento](event-hubs-node-get-started-receive.md).

1. Abra o projeto no Visual Studio Code. 
2. Crie um arquivo chamado **.env** na pasta **cliente**. Copie e cole as variáveis de ambiente de exemplo do **sample.env** na pasta raiz.
3. Configure a cadeia de conexão do hub de eventos, o nome do hub de eventos e o ponto de extremidade de armazenamento. Você pode copiar a cadeia de conexão do hub de eventos da chave **Primária da cadeia de conexão** em **RootManageSharedAccessKey** na página do Hub de Eventos no portal do Azure. Para obter as etapas detalhadas, confira [Obter cadeia de conexão](event-hubs-create.md#create-an-event-hubs-namespace).
4. Em sua CLI do Azure, navegue até o caminho da pasta do **cliente**. Instale os pacotes do Node e compile o projeto executando os comandos a seguir:

    ```nodejs
    npm i
    npm run build
    ```
5. Inicie o envio de eventos executando o seguinte comando: 

    ```nodejs
    node dist/examples/simpleSender.js
    ```


## <a name="review-the-sample-code"></a>Revisar o código de exemplo 
Aqui está o código de exemplo para enviar eventos para um hub de eventos usando o node.js. Manualmente, você pode criar um arquivo sampleSender.js e executá-lo para enviar eventos para um hub de eventos. 


```nodejs
const { EventHubClient, EventPosition } = require('@azure/event-hubs');

const client = EventHubClient.createFromConnectionString(process.env["EVENTHUB_CONNECTION_STRING"], process.env["EVENTHUB_NAME"]);

async function main() {
    // NOTE: For receiving events from Azure Stream Analytics, please send Events to an EventHub where the body is a JSON object/array.
    // const eventData = { body: { "message": "Hello World" } };
    const data = { body: "Hello World 1" };
    const delivery = await client.send(data);
    console.log("message sent successfully.");
}

main().catch((err) => {
    console.log(err);
});

```

Lembre-se de definir as variáveis de ambiente antes de executar o script. Você pode configurá-las na linha de comando, conforme é mostrado no exemplo a seguir, ou usar o [dotenv package](https://www.npmjs.com/package/dotenv#dotenv). 

```
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
