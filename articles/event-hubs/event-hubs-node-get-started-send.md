---
title: Enviar eventos para os Hubs de Eventos do Azure usando o Node.js | Microsoft Docs
description: Comece a enviar eventos para os Hubs de Eventos usando o Node.js.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.date: 09/18/2018
ms.author: shvija
ms.openlocfilehash: 413f36a12dee135cc1a7dc99a34d8b7b2be6c46f
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/05/2018
ms.locfileid: "48801056"
---
# <a name="send-events-to-azure-event-hubs-using-nodejs"></a>Enviar eventos para Hubs de Eventos do Azure usando Node.js

Os Hubs de Eventos do Azure são um sistema gerenciamento de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por dispositivos conectados e outros sistemas. Depois de coletados em um hub de eventos, você pode receber e manipular eventos usando manipuladores em processo ou por encaminhamento para outros sistemas de análise.

Para saber mais sobre os Hubs de Eventos, consulte a [Visão geral dos Hubs de Eventos](event-hubs-about.md).

Este tutorial descreve como enviar eventos para um hub de eventos de um aplicativo escrito em Node.js. Para receber eventos usando o pacote de Host de Processador de Eventos do Node.js, veja o [artigo de Recebimento correspondente](event-hubs-node-get-started-receive.md).

O código deste início rápido está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client). 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Versão do Node.js 8.x e superiores. Baixe a versão mais recente do LTS em [https://nodejs.org](https://nodejs.org).
- Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.
- Visual Studio Code (recomendado) ou qualquer outro IDE

## <a name="create-a-namespace-and-event-hub"></a>Criar um hub de evento e de namespace
A primeira etapa é usar o portal do Azure para criar um namespace de Hubs de Eventos com um hub de eventos. Se ele ainda não existir, crie essas entidades seguindo as instruções em [Criar um namespace de Hubs de Eventos e um hub de eventos usando o portal do Azure](event-hubs-create.md).

## <a name="clone-the-sample-git-repository"></a>Clonar o repositório Git de exemplo
Clone o repositório de Git de exemplo do [GitHub](https://github.com/Azure/azure-event-hubs-node) em seu computador. 

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
3. Configure a cadeia de conexão do hub de eventos, o nome do hub de eventos e o ponto de extremidade de armazenamento. Você pode copiar a cadeia de conexão do hub de eventos da chave **Primária da cadeia de conexão** em **RootManageSharedAccessKey** na página do Hub de Eventos no portal do Azure. Para obter as etapas detalhadas, confira [Obter cadeia de conexão](event-hubs-quickstart-portal.md#create-an-event-hubs-namespace).
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

Veja os artigos a seguir para saber mais sobre os Hubs de Eventos:

* [Receber eventos usando o Node.js](event-hubs-node-get-started-receive.md)
* [Exemplos no GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/)
* [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)

<!-- Links -->
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
