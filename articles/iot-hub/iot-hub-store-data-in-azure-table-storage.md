---
title: Salvar suas mensagens do hub IoT no armazenamento de dados do Azure | Microsoft Docs
description: "Use um aplicativo de funções do Azure para salvar as mensagens do hub IoT para seu armazenamento de tabela do Azure. As mensagens do hub IoT contêm informações como dados de sensor, que são enviadas do seu dispositivo IoT."
services: iot-hub
documentationcenter: 
author: shizn
manager: timlt
tags: 
keywords: armazenamento de dados iot, armazenamento de dados de sensor iot
ms.assetid: 62fd14fd-aaaa-4b3d-8367-75c1111b6269
ms.service: iot-hub
ms.devlang: arduino
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/16/2017
ms.author: xshi
ms.translationtype: HT
ms.sourcegitcommit: 540180e7d6cd02dfa1f3cac8ccd343e965ded91b
ms.openlocfilehash: 06503f9564e00ef62587d02f2da4778974e246c5
ms.contentlocale: pt-br
ms.lasthandoff: 08/16/2017

---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-table-storage"></a>Salvar mensagens do hub IoT que contêm dados de sensor para seu armazenamento de tabela do Azure

![Diagrama de ponta a ponta](media/iot-hub-get-started-e2e-diagram/3.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Saiba como criar uma conta de armazenamento do Azure e um aplicativo de funções do Azure para armazenar mensagens do hub IoT no armazenamento de tabelas.

## <a name="what-you-do"></a>O que fazer

- Crie uma conta de armazenamento do Azure.
- Prepare a conexão do hub IoT para ler mensagens.
- Criar e implantar um aplicativo de funções do Azure.

## <a name="what-you-need"></a>O que você precisa

- [Configure seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) para abranger os seguintes requisitos:
  - Uma assinatura ativa do Azure
  - Um hub IoT em sua assinatura 
  - Um aplicativo em execução que envia mensagens ao seu hub IoT

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Armazenamento** > **Conta de armazenamento** > **Criar**.

2. Insira as informações necessárias para a conta de armazenamento:

   ![Criar uma conta de armazenamento no portal do Azure](media\iot-hub-store-data-in-azure-table-storage\1_azure-portal-create-storage-account.png)

   * **Name**: o nome da conta de armazenamento. O nome deve ser globalmente exclusivo.

   * **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu hub IoT.

   * **Fixar no painel**: selecione essa opção para ter fácil acesso ao Hub IoT no painel.

3. Clique em **Criar**.

## <a name="prepare-your-iot-hub-connection-to-read-messages"></a>Preparar a conexão do hub IoT para ler mensagens

O hub IoT expõe um ponto de extremidade compatível com o hub de eventos interno para permitir que os aplicativos leiam mensagens do hub IoT. Enquanto isso, os aplicativos usam grupos de consumidores para ler dados do hub IoT. Antes de criar um aplicativo de funções do Azure para ler dados do seu hub IoT, faça o seguinte:

- Obter a cadeia de conexão do ponto de extremidade de Hub IoT.
- Criar um grupo de consumidores para o Hub IoT.

### <a name="get-the-connection-string-of-your-iot-hub-endpoint"></a>Obter a cadeia de conexão do ponto de extremidade de Hub IoT

1. Abrir seu Hub IoT.

2. No painel **Hub IoT**, clique em **Pontos de Extremidade** em **Mensagens**.

3. No painel direito, clique em **Eventos** em **Pontos de extremidade internos**.

4. No painel **Propriedades**, observe os seguintes valores:
   - Ponto de extremidade compatível com o hub de eventos
   - Nome compatível com o hub de eventos

   ![Obter a cadeia de conexão do ponto de extremidade de Hub IoT no Portal do Azure](media\iot-hub-store-data-in-azure-table-storage\2_azure-portal-iot-hub-endpoint-connection-string.png)

5. No painel **Hub IoT**, clique em **Políticas de acesso compartilhado** em **Configurações**.

6. Clique em **iothubowner**.

7. Observe o valor da **Chave primária**.

8. Crie a cadeia de conexão do seu ponto de extremidade do hub IoT conforme descrito a seguir:

   `Endpoint=<Event Hub-compatible endpoint>;SharedAccessKeyName=iothubowner;SharedAccessKey=<Primary key>`

   > [!NOTE]
   > Substituir `<Event Hub-compatible endpoint>` e `<Primary key>` pelos valores que você anotou anteriormente.

### <a name="create-a-consumer-group-for-your-iot-hub"></a>Criar um grupo de consumidores para o Hub IoT

1. Abrir seu Hub IoT.

2. No painel **Hub IoT**, clique em **Pontos de Extremidade** em **Mensagens**.

3. No painel direito, clique em **Eventos** em **Pontos de extremidade internos**.

4. No painel **Propriedades**, em **Grupos de consumidores**, insira um nome e, em seguida, anote-o.

5. Clique em **Salvar**.

## <a name="create-and-deploy-an-azure-function-app"></a>Criar e implantar um aplicativo de funções do Azure

1. No [portal do Azure](https://portal.azure.com/), clique em **Novo** > **Computação** > **Aplicativo de Funções** > **Criar**.

2. Insira as informações necessárias para o aplicativo de funções.

   ![Criar um aplicativo de funções no portal do Azure](media\iot-hub-store-data-in-azure-table-storage\3_azure-portal-create-function-app.png)

   * **Nome do aplicativo**: o nome do aplicativo de funções. O nome deve ser globalmente exclusivo.

   * **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu hub IoT.

   * **Conta de Armazenamento**: a conta de armazenamento que você criou.

   * **Fixar ao painel**: marque essa opção para facilitar o acesso ao aplicativo de funções do painel.

3. Clique em **Criar**.

4. Após o aplicativo de funções ter sido criado, abra-o.

5. No aplicativo de funções, crie uma nova função fazendo o seguinte:

   a. Clique em **Nova Função**.

   b. Selecione **JavaScript** para **Linguagem** e **Processamento de Dados** para **Cenário**.

   c. Clique em **Criar essa função** e depois clique em **Nova Função**.

   d. Selecione **JavaScript** para linguagem e **Processamento de Dados** para o cenário.

   e. Clique no modelo **EventHubTrigger-JavaScript**.

   f. Insira as informações necessárias para o modelo.

      * **Nomeie a função**: o nome da função.

      * **Nome do Hub de Eventos**: nome compatível com o hub de eventos anotado anteriormente.

      * **Conexão do Hub de Eventos**: clique em **Novo** para adicionar a cadeia de conexão do ponto de extremidade do hub IoT que você criou.

   g. Clique em **Criar**.

6. Configure uma saída da função fazendo o seguinte:

   a. Clique em **Integrar** > **Nova Saída** > **Armazenamento de Tabela do Azure** > **Selecionar**.

      ![Adicionar um armazenamento de tabelas ao aplicativo de funções no portal do Azure](media\iot-hub-store-data-in-azure-table-storage\4_azure-portal-function-app-add-output-table-storage.png)

   b. Insira as informações necessárias.

      * **Nome do parâmetro de tabela**: use `outputTable`, que será usado no código da função do Azure.
      
      * **Nome da tabela**: use `deviceData`.

      * **Conexão da conta de armazenamento**: clique em **Novo** e selecione ou insira sua conta de armazenamento. Se a conta de armazenamento não for exibida, consulte [Requisitos da conta de armazenamento](https://docs.microsoft.com/azure/azure-functions/functions-create-function-app-portal#storage-account-requirements).
      
   c. Clique em **Salvar**.

7. Em **Gatilhos**, clique em **Hub de Eventos do Azure (eventHubMessages)**.

8. Em **Grupo de consumidores do Hub de Eventos**, digite o nome do grupo de consumidores que você criou anteriormente e clique em **Salvar**.

9. Clique na função que você criou no lado esquerdo e, em seguida, clique em **exibir arquivos** à direita.

10. Substitua o código em `index.js` pelo seguinte código:

   ```javascript
   'use strict';

   // This function is triggered each time a message is received in the IoT hub.
   // The message payload is persisted in an Azure storage table
 
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

11. Clique em **Salvar**.

Agora você criou o aplicativo de funções. Ele armazena as mensagens que seu hub IoT recebe em seu armazenamento de tabelas.

> [!NOTE]
> Você pode usar o botão **Executar** para testar o aplicativo de funções. Quando você clica em **Executar**, a mensagem de teste é enviada para o Hub IoT. A chegada da mensagem deve disparar o início do aplicativo de funções e, em seguida, salvar a mensagem no armazenamento de tabelas. O painel **Logs** registra os detalhes do processo.

## <a name="verify-your-message-in-your-table-storage"></a>Verificar a mensagem no armazenamento de tabelas

1. Execute o aplicativo de exemplo em seu dispositivo para enviar mensagens para o Hub IoT.

2. [Baixe e instale o Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).

3. Abra o Gerenciador de Armazenamento, clique em **Adicionar uma Conta do Azure** > **Entrar** e entre em sua conta do Azure.

4. Clique em sua assinatura do Azure > **Contas de Armazenamento** > sua conta de armazenamento > **Tabelas** > **deviceData**.

   Você deve ver as mensagens enviadas do seu dispositivo ao seu Hub IoT conectado à tabela `deviceData`.

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito sua conta de armazenamento do Azure e o aplicativo de funções do Azure para armazenar mensagens que seu hub IoT recebe em seu armazenamento de tabelas.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]

