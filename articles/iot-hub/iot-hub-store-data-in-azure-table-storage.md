---
title: Salvar mensagens do Hub IoT para o armazenamento de dados do Azure | Microsoft Docs
description: "Use o Aplicativo de Funções do Azure para salvar as mensagens do Hub IoT no armazenamento de tabelas do Azure. As mensagens de Hub IoT contêm informações como dados de sensor que são enviadas do seu dispositivo IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timtl
tags: 
keywords: armazenamento de dados iot, armazenamento de dados de sensor iot
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/27/2017
ms.author: xshi
ms.translationtype: Human Translation
ms.sourcegitcommit: a1ba750d2be1969bfcd4085a24b0469f72a357ad
ms.openlocfilehash: c1f5d737b9718ead9c59794dae23798ef26aa42a
ms.contentlocale: pt-br
ms.lasthandoff: 06/20/2017


---
# <a name="save-iot-hub-messages-that-contain-information-like-sensor-data-to-azure-table-storage"></a>Salvar mensagens de Hub IoT que contêm informações como dados de sensor no armazenamento de tabelas do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-will-learn"></a>O que você aprenderá

Saiba como criar uma conta de armazenamento do Azure e um Aplicativo de Funções do Azure para armazenar mensagens de Hub IoT no armazenamento de tabelas do Azure.

## <a name="what-you-will-do"></a>O que você fará

- Crie uma conta de armazenamento do Azure.
- Prepare a conexão do Hub IoT para ler mensagens.
- Crie e implante um Aplicativo de Funções do Azure.

## <a name="what-you-will-need"></a>O que será necessário

- Tutorial [Configurar seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) concluído que aborda os seguintes requisitos:
  - Uma assinatura ativa do Azure.
  - Um Hub IoT do Azure em sua assinatura.
  - O aplicativo em execução que envia mensagens para o Hub IoT do Azure.

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Armazenamento** > **Conta de armazenamento**.
1. Insira as informações necessárias para a conta de armazenamento:

   ![Criar uma conta de armazenamento no Portal do Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   **Name**: o nome da conta de armazenamento. O nome deve ser globalmente exclusivo.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu Hub IoT.

   **Fixar no painel**: marque esta opção para facilitar o acesso ao seu Hub IoT do painel.
1. Clique em **Criar**.

## <a name="prepare-for-iot-hub-connection-to-read-messages"></a>Prepare a conexão do Hub IoT para ler mensagens

O Hub IoT expõe um ponto de extremidade compatível com o Hub de Eventos interno para permitir que aplicativos leiam mensagens do Hub IoT. Enquanto isso, aplicativos usam grupos de consumidores para ler dados do Hub IoT. Antes de criar um Aplicativo de Funções do Azure para ler dados do seu Hub IoT, você precisa:

- Obter a cadeia de conexão do ponto de extremidade de Hub IoT.
- Criar um grupo de consumidores para o Hub IoT.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Obter a cadeia de conexão do ponto de extremidade de Hub IoT

1. Abrir seu Hub IoT.
1. No painel **Hub IoT**, clique em **Pontos de extremidade** em **MENSAGENS**.
1. No painel direito, clique em **Eventos** em **Pontos de extremidade internos**.
1. No painel **Propriedades**, anote os valores a seguir:
   - Ponto de extremidade compatível com o Hub de Eventos
   - Nome compatível com o Hub de Eventos

   ![Obter a cadeia de conexão do ponto de extremidade de Hub IoT no Portal do Azure](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

1. No painel **Hub IoT**, clique em **Políticas de acesso compartilhado** em **CONFIGURAÇÕES**.
1. Clique em **iothubowner**.
1. Anote o valor da **Chave primária**.
1. Crie a cadeia de conexão do ponto de extremidade de Hub IoT conforme descrito a seguir:

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!Note]
   > Substitua `<Event Hub-compatible endpoint>` e `<Primary key>` pelos valores anotados.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Criar um grupo de consumidores para o Hub IoT

1. Abrir seu Hub IoT.
1. No painel **Hub IoT**, clique em **Pontos de extremidade** em **MENSAGENS**.
1. No painel direito, clique em **Eventos** em **Pontos de extremidade internos**.
1. No painel **Propriedades**, digite um nome em **Grupos de consumidores** e anote-o.
1. Clique em **Salvar**.

## <a name="create-and-deploy-an-azure-function-app"></a>Criar e implantar um Aplicativo de Funções do Azure

1. No [Portal do Azure](https://portal.azure.com/), clique em **Novo** > **Computação** > **Aplicativo de Funções**.
1. Insira as informações necessárias para o Aplicativo de Funções.

   ![Criar um Aplicativo de Funções no Portal do Azure](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   **Nome do Aplicativo**: o nome do Aplicativo de Funções. O nome deve ser globalmente exclusivo.

   **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu Hub IoT.

   **Conta de Armazenamento**: a conta de armazenamento que você criou.

   **Fixar no painel**: marque esta opção para facilitar o acesso ao Aplicativo de Funções do painel.
1. Clique em **Criar**.
1. Abra o Aplicativo de Funções depois que ela for criada.
1. Crie uma nova função no Aplicativo de Funções.
   1. Clique em **Nova Função**.
   1. Selecione **JavaScript** para **Linguagem** e **Processamento de Dados** para **Cenário**.
   1. Clique em **Criar esta função**, depois clique em **Nova Função**.
   1. Selecione **JavaScript** para linguagem e **Processamento de Dados** para o cenário.
   1. Clique no modelo **EventHubTrigger-JavaScript**.
   1. Insira as informações necessárias para o modelo.

      **Nomeie a função**: o nome da função.

      **Nome do Hub de Eventos**: nome compatível com o Hub de Eventos anotado.

      **Conexão de Hub de Eventos**: clique em novo para adicionar a cadeia de conexão do ponto de extremidade de Hub IoT que criou.
   1. Clique em **Criar**.
1. Configure uma saída da função.
   1. Clique em **Integrar** > **Nova Saída** > **Armazenamento de Tabela do Azure** > **Selecionar**.

      ![Adicionar um armazenamento de tabelas ao Aplicativo de Funções no Portal do Azure](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)
   1. Insira as informações necessárias.

      **Nome do parâmetro da tabela**: use `outputTable` para o nome que será usado no código do Azure Functions.
      
      **Nome da tabela**: use `deviceData` para o nome.

      **Conexão da conta de armazenamento**: clique em **novo** e selecione a conta de armazenamento. Se você não consegue ver a conta de armazenamento, consulte os [Requisitos da conta de armazenamento](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements)
      
   1. Clique em **Salvar**.
1. Em **Gatilhos**, clique em **Hub de Eventos do Azure (myEventHubTrigger)**.
1. Em **Grupo de consumidores do Hub de Eventos**, digite o nome do grupo de consumidores que você criou anteriormente e clique em **Salvar**.
1. Clique em **Desenvolver** e, em seguida, clique em **Exibir arquivos**.
1. Substitua o código em `index.js` pelo exibido a seguir e clique em **Salvar**.

   ```javascript
   'use strict';

   // This function is triggered each time a message is revieved in the IoTHub.
   // The message payload is persisted in an Azure Storage Table
 
   module.exports = function (context, iotHubMessage) {
    context.log('Message received: ' + JSON.stringify(iotHubMessage));
    var date = Date.now();
    var partitionKey = Math.floor(date / (24 * 60 * 60 * 1000)) + '';
    var rowKey = date + '';
    context.bindings.outputTable = {
     "partitionKey": partitionKey,
     "rowKey": rowKey,
     "message": JSON.stringify(iotHubMessage)
    };
    context.done();
   };
   ```

A essa altura, você já criou o Aplicativo de Funções. Ele armazena as mensagens que o Hub IoT recebe em seu Armazenamento de Tabelas do Azure.

> [!Note]
> Você pode usar o botão **Executar** para testar o Aplicativo de Funções. Quando você clica em **Executar**, a mensagem de teste é enviada para o Hub IoT. A chegada da mensagem deve disparar o início do Aplicativo de Funções e, em seguida, salvar a mensagem no Armazenamento de Tabelas do Azure. O painel **Logs** registra os detalhes do processo.

## <a name="verify-your-message-in-your-table-storage"></a>Verificar a mensagem no armazenamento de tabelas

1. Execute o aplicativo de exemplo em seu dispositivo para enviar mensagens para o Hub IoT.
1. [Baixe e instale o Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).
1. Abra o Gerenciador de Armazenamento do Microsoft Azure, clique em **Adicionar uma Conta do Azure** > **Entrar** e entre em sua conta do Azure.
1. Clique em sua assinatura do Azure > **Contas de Armazenamento** > sua conta de armazenamento > **Tabelas** > **deviceData**.

   Você deve ver as mensagens enviadas do seu dispositivo ao seu Hub IoT conectado à tabela `deviceData`.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito sua conta de armazenamento do Azure e o Aplicativo de Funções do Azure para armazenar mensagens que o Hub IoT recebe em seu Armazenamento de Tabelas do Azure.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

