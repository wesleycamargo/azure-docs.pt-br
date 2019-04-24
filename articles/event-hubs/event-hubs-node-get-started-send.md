---
title: Enviar e receber eventos usando o Node. js – os Hubs de eventos do Azure | Microsoft Docs
description: Este artigo apresenta instruções passo a passo para a criação de um aplicativo Node.js que envia eventos de Hubs de Eventos do Azure.
services: event-hubs
author: spelluru
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 04/15/2019
ms.author: spelluru
ms.openlocfilehash: f03bfde8f7ea37989756ad47678369e94b831438
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60203220"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-nodejs"></a>Enviar eventos para ou receber eventos de Hubs de eventos do Azure usando Node. js

O Hubs de Eventos do Azure é uma plataforma de streaming de Big Data e um serviço de ingestão de eventos que pode receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos pelos dispositivos e software distribuídos. Os dados enviados para um Hub de Eventos podem ser transformados e armazenados usando qualquer provedor de análise em tempo real ou adaptadores de envio em lote/armazenamento. Para obter uma visão detalhada dos Hubs de Eventos, confira [Visão geral de Hubs de Eventos](event-hubs-about.md) e [Recursos de Hubs de Eventos](event-hubs-features.md).

Este tutorial descreve como criar aplicativos Node. js para enviar eventos para ou receber eventos de um hub de eventos.

> [!NOTE]
> Você pode baixar do [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client) este início rápido como um exemplo, substituir as cadeias de caracteres `EventHubConnectionString` e `EventHubName` pelos valores do hub de eventos e executá-lo. Como alternativa, é possível seguir as etapas deste tutorial para criar sua própria solução.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de começar.
- Versão do Node.js 8.x e superiores. Baixe a versão mais recente do LTS em [https://nodejs.org](https://nodejs.org).
- Visual Studio Code (recomendado) ou qualquer outro IDE
- **Criar um namespace de Hubs de eventos e um hub de eventos**. A primeira etapa é usar o [portal do Azure](https://portal.azure.com) para criar um namespace do tipo Hubs de eventos e obter as credenciais de gerenciamento das quais que seu aplicativo precisa para se comunicar com o hub de eventos. Para criar um namespace e um hub de eventos, siga o procedimento [neste artigo](event-hubs-create.md) e então continue com as etapas a seguir neste tutorial. Em seguida, obtenha a cadeia de conexão para o namespace de hub de eventos seguindo as instruções do artigo: [Obter a cadeia de conexão](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Você usa a cadeia de conexão posteriormente no tutorial.
- Clone o [repositório GitHub de exemplo](https://github.com/Azure/azure-event-hubs-node) em seu computador. 


## <a name="send-events"></a>Enviar eventos
Esta seção mostra como criar um aplicativo Node. js que envia eventos para um hub de eventos. 

### <a name="install-nodejs-package"></a>Instalar o pacote do Node.js
Instale o pacote de Node.js para Hubs de Eventos do Azure em seu computador. 

```shell
npm install @azure/event-hubs
```

Se você ainda não clonou o repositório de Git, conforme mencionado nos pré-requisitos, baixe o [amostra](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples) do GitHub. 

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


### <a name="review-the-sample-code"></a>Revisar o código de exemplo 
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

## <a name="receive-events"></a>Receber eventos
Este tutorial mostra como receber eventos de um hub de eventos usando o [EventProcessorHost](event-hubs-event-processor-host.md) do Azure em um aplicativo Node.js. O EPH (EventProcessorHost) ajuda você a receber eventos de um hub de eventos com eficiência criando receptores em todas as partições no grupo de consumidores de um hub de eventos. Ele usa um ponto de verificação nos metadados das mensagens recebidas em intervalos regulares em um Azure Storage Blob. Com essa abordagem, fica fácil continuar a receber mensagens mais tarde exatamente no ponto em que você parou.

O código deste início rápido está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

### <a name="clone-the-git-repository"></a>Clonar o repositório Git
Baixe ou clone o [exemplo](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) do GitHub. 

### <a name="install-the-eventprocessorhost"></a>Instalar o EventProcessorHost
Instale o EventProcessorHost para o módulo de Hubs de Eventos. 

```shell
npm install @azure/event-processor-host
```

### <a name="receive-events-using-eventprocessorhost"></a>Receber eventos usando o EventProcessorHost
O SDK que você clonou contém vários exemplos que mostram como receber eventos de um hub de eventos usando o Node.js. Neste início rápido, você usará o exemplo **singleEPH.js**. Para observar os eventos que estão sendo recebidos, abra outro terminal e envie eventos usando [Enviar exemplo](event-hubs-node-get-started-send.md).

1. Abra o projeto no Visual Studio Code. 
2. Crie um arquivo chamado **.env** na pasta **processador**. Copie e cole as variáveis de ambiente de exemplo do **sample.env** na pasta raiz.
3. Configure a cadeia de conexão do hub de eventos, o nome do hub de eventos e o ponto de extremidade de armazenamento. Você pode copiar a cadeia de conexão do hub de eventos da chave **Primária da cadeia de conexão** em **RootManageSharedAccessKey** na página do Hub de Eventos no portal do Azure. Para obter as etapas detalhadas, confira [Obter cadeia de conexão](event-hubs-create.md#create-an-event-hubs-namespace).
4. Na CLI do Azure, navegue até o caminho da pasta **processador**. Instale os pacotes do Node e compile o projeto executando os comandos a seguir:

    ```shell
    npm i
    npm run build
    ```
5. Receba eventos com o host do processador de eventos executando o seguinte comando:

    ```shell
    node dist/examples/singleEph.js
    ```

### <a name="review-the-sample-code"></a>Revisar o código de exemplo 
Aqui está o código de exemplo para receber eventos de um hub de eventos usando o Node.js. Você pode criar manualmente um arquivo sampleEph.js e executá-lo para receber eventos em um hub de eventos. 

  ```javascript
  const { EventProcessorHost, delay } = require("@azure/event-processor-host");

  const path = process.env.EVENTHUB_NAME;
  const storageCS = process.env.STORAGE_CONNECTION_STRING;
  const ehCS = process.env.EVENTHUB_CONNECTION_STRING;
  const storageContainerName = "test-container";
  
  async function main() {
    // Create the Event Processor Host
    const eph = EventProcessorHost.createFromConnectionString(
      EventProcessorHost.createHostName("my-host"),
      storageCS,
      storageContainerName,
      ehCS,
      {
        eventHubPath: path,
        onEphError: (error) => {
          console.log("This handler will notify you of any internal errors that happen " +
          "during partition and lease management: %O", error);
        }
      }
    );
    let count = 0;
    // Message event handler
    const onMessage = async (context/*PartitionContext*/, data /*EventData*/) => {
      console.log(">>>>> Rx message from '%s': '%s'", context.partitionId, data.body);
      count++;
      // let us checkpoint every 100th message that is received across all the partitions.
      if (count % 100 === 0) {
        return await context.checkpoint();
      }
    };
    // Error event handler
    const onError = (error) => {
      console.log(">>>>> Received Error: %O", error);
    };
    // start the EPH
    await eph.start(onMessage, onError);
    // After some time let' say 2 minutes
    await delay(120000);
    // This will stop the EPH.
    await eph.stop();
  }
  
  main().catch((err) => {
    console.log(err);
  });
      
  ```

Lembre-se de definir as variáveis de ambiente antes de executar o script. Você pode configurá-las na linha de comando, conforme é mostrado no exemplo a seguir, ou usar o [dotenv package](https://www.npmjs.com/package/dotenv#dotenv). 

```shell
// For windows
set EVENTHUB_CONNECTION_STRING="<your-connection-string>"
set EVENTHUB_NAME="<your-event-hub-name>"

// For linux or macos
export EVENTHUB_CONNECTION_STRING="<your-connection-string>"
export EVENTHUB_NAME="<your-event-hub-name>"
```

Encontre mais exemplos [aqui](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples).


## <a name="next-steps"></a>Próximas etapas
Leia os seguintes artigos:

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Recursos e a terminologia nos Hubs de eventos do Azure](event-hubs-features.md)
- [Perguntas frequentes sobre os Hubs de Eventos](event-hubs-faq.md)
- Confira outros exemplos de Node.js para Hubs de Eventos no [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/client/examples/).
