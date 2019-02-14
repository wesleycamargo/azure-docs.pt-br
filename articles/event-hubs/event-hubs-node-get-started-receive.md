---
title: Receber eventos usando o Node.js - Hubs de Eventos do Azure | Microsoft Docs
description: Este artigo fornece um passo a passo para a criação de um aplicativo Node.js que recebe eventos dos Hubs de Eventos do Azure.
services: event-hubs
author: ShubhaVijayasarathy
manager: kamalb
ms.service: event-hubs
ms.workload: core
ms.topic: article
ms.custom: seodec18
ms.date: 12/06/2018
ms.author: shvija
ms.openlocfilehash: 55e4ce4a59a498c5c22fe6e701e3140aa65e7a10
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/08/2019
ms.locfileid: "55895237"
---
# <a name="receive-events-from-azure-event-hubs-using-nodejs"></a>Receber eventos de Hubs de Eventos do Azure usando o Node.js

Os Hubs de Eventos do Azure são um sistema gerenciamento de inclusão altamente dimensionável que pode receber milhões de eventos por segundo, permitindo que um aplicativo processe e analise grandes quantidades de dados produzidos por dispositivos conectados e outros sistemas. Depois de coletados em um hub de eventos, você pode receber e manipular eventos usando manipuladores em processo ou por encaminhamento para outros sistemas de análise. Você também pode capturar os dados de eventos no Armazenamento do Azure ou no Azure Data Lake Storage antes que eles sejam processados.  

Para saber mais sobre os Hubs de Eventos, consulte a [Visão geral dos Hubs de Eventos](event-hubs-about.md).

Este tutorial mostra como receber eventos de um hub de eventos usando o [EventProcessorHost](event-hubs-event-processor-host.md) do Azure em um aplicativo Node.js. O EPH (EventProcessorHost) ajuda você a receber eventos de um hub de eventos com eficiência criando receptores em todas as partições no grupo de consumidores de um hub de eventos. Ele usa um ponto de verificação nos metadados das mensagens recebidas em intervalos regulares em um Azure Storage Blob. Com essa abordagem, fica fácil continuar a receber mensagens mais tarde exatamente no ponto em que você parou.

O código deste início rápido está disponível no [GitHub](https://github.com/Azure/azure-event-hubs-node/tree/master/processor).

> [!NOTE]
>  Para enviar eventos para Hubs de Eventos usando o Node.js, consulte este artigo: [Enviar eventos ao Hubs de Eventos do Azure usando Node.js](event-hubs-node-get-started-send.md). 

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, você precisará dos seguintes pré-requisitos:

- Versão do Node.js 8.x e superiores. Baixe a versão mais recente do LTS em [https://nodejs.org](https://nodejs.org). Não use a versão mais antiga do LTS do Node.js. 
- Uma conta ativa do Azure. Se você não tiver uma assinatura do Azure, crie uma [conta gratuita][] antes de começar.

## <a name="create-a-namespace-and-event-hub"></a>Criar um hub de evento e de namespace
A primeira etapa é usar o portal do Azure para criar um namespace de Hubs de Eventos com um hub de eventos. Se ele ainda não existir, crie essas entidades seguindo as instruções em [Criar um namespace de Hubs de Eventos e um hub de eventos usando o portal do Azure](event-hubs-create.md).

## <a name="create-a-storage-account-and-container"></a>Criar uma conta e um contêiner de armazenamento
Para usar o EventProcessorHost, você precisa ter uma conta de Armazenamento do Azure. As informações de estado, como as concessões em partições e os pontos de verificação no fluxo de eventos, são compartilhadas entre os receptores usando um contêiner de Armazenamento do Azure. Você pode criar uma conta de Armazenamento do Azure seguindo as instruções [neste artigo](../storage/common/storage-quickstart-create-account.md).

## <a name="clone-the-git-repository"></a>Clonar o repositório Git
Baixe ou clone o [exemplo](https://github.com/Azure/azure-event-hubs-node/tree/master/processor/examples/) do GitHub. 

## <a name="install-the-eventprocessorhost"></a>Instalar o EventProcessorHost
Instale o EventProcessorHost para o módulo de Hubs de Eventos. 

```shell
npm install @azure/event-processor-host
```

## <a name="receive-events-using-eventprocessorhost"></a>Receber eventos usando o EventProcessorHost
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

## <a name="review-the-sample-code"></a>Revisar o código de exemplo 
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

Neste início rápido, você criou um aplicativo Node.js que recebeu mensagens de um hub de eventos. Para saber como enviar eventos para um hub de eventos usando o Node.js, consulte [Enviar eventos do hub de eventos - Node.js](event-hubs-node-get-started-send.md).

<!-- Links -->
[conta gratuita]: https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio
