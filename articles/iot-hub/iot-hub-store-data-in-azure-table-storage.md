---
title: Salvar suas mensagens do hub IoT no armazenamento de dados do Azure | Microsoft Docs
description: Use o roteamento de mensagens do Hub IoT para salvar as mensagens do Hub IoT em seu armazenamento de blobs do Azure. As mensagens do hub IoT contêm informações como dados de sensor, que são enviadas do seu dispositivo IoT.
author: rangv
manager: ''
keywords: armazenamento de dados iot, armazenamento de dados de sensor iot
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 04/11/2018
ms.author: rangv
ms.openlocfilehash: 84355453a5cb8d8f42abdcbde5432651c9c035b0
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/05/2018
ms.locfileid: "37856283"
---
# <a name="save-iot-hub-messages-that-contain-sensor-data-to-your-azure-blob-storage"></a>Salvar mensagens do Hub IoT que contêm dados de sensor em seu armazenamento de blobs do Azure

![Diagrama de ponta a ponta](./media/iot-hub-store-data-in-azure-table-storage/1_route-to-storage.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

## <a name="what-you-learn"></a>O que você aprenderá

Você aprenderá a criar uma conta de armazenamento do Azure e um aplicativo de funções do Azure para armazenar mensagens do Hub IoT no Armazenamento de Blobs do Azure.

## <a name="what-you-do"></a>O que fazer

- Crie uma conta de armazenamento do Azure.
- Configure seu Hub IoT para rotear mensagens para o armazenamento.

## <a name="what-you-need"></a>O que você precisa

- [Configure seu dispositivo](iot-hub-raspberry-pi-kit-node-get-started.md) para abranger os seguintes requisitos:
  - Uma assinatura ativa do Azure
  - Um hub IoT em sua assinatura 
  - Um aplicativo em execução que envia mensagens ao seu hub IoT

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Azure

1. No [portal do Azure](https://portal.azure.com/), clique em **Criar um recurso** > **Armazenamento** > **Conta de armazenamento**.

2. Insira as informações necessárias para a conta de armazenamento:

   ![Criar uma conta de armazenamento no portal do Azure](./media/iot-hub-store-data-in-azure-table-storage/1_azure-portal-create-storage-account.png)

   * **Name**: o nome da conta de armazenamento. O nome deve ser globalmente exclusivo.

   * **Tipo de Conta**: escolha `Storage (general purpose v1)`.

   * **Local**: escolha o mesmo local que o Hub IoT usa.

   * **Replicação**: escolha `Locally-redundant storage (LRS)`.

   * **Desempenho**: escolha `Standard`.

   * **Transferência segura necessária**: escolha `Disabled`.

   * **Assinatura**: selecione sua assinatura do Azure.

   * **Grupo de recursos**: use o mesmo grupo de recursos usado pelo seu hub IoT.

   * **Fixar no painel**: selecione essa opção para ter fácil acesso ao Hub IoT no painel.

3. Clique em **Criar**.

## <a name="prepare-your-iot-hub-to-route-messages-to-storage"></a>Preparar seu Hub IoT para rotear mensagens para o armazenamento

O Hub IoT nativamente dá suporte ao roteamento de mensagens para o armazenamento do Azure como blobs. Para saber mais sobre os pontos de extremidade personalizados do Hub IoT do Azure, você pode consultar a [Lista de pontos de extremidade do Hub IoT internos](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-endpoints#custom-endpoints).

### <a name="add-storage-as-a-custom-endpoint"></a>Adicionar o armazenamento como um ponto de extremidade personalizado

1. Vá ao Hub IoT no portal do Azure. 

2. Clique em **Pontos de extremidade** > **Adicionar**. 

3. Nomeie o ponto de extremidade e selecione **Contêiner de Armazenamento do Azure** como o tipo de ponto de extremidade. 

4. Use o seletor para selecionar a conta de armazenamento que você criou na seção anterior. Crie um contêiner de armazenamento, selecione-o e clique em **OK**.

   ![Criar um ponto de extremidade personalizado no Hub IoT](./media/iot-hub-store-data-in-azure-table-storage/2_custom-storage-endpoint.png)

### <a name="add-a-route-to-route-data-to-storage"></a>Adicionar uma rota para rotear dados para o armazenamento

1. Clique em **Rotas** > **Adicionar** e digite um nome para a rota. 

2. Selecione **Mensagens de Dispositivo** como a fonte de dados e selecione o ponto de extremidade de armazenamento que você acabou de criar como o ponto de extremidade na rota. 

3. Digite `true` como a cadeia de caracteres de consulta e, em seguida, clique em **Salvar**.

   ![Criar uma rota no Hub IoT](./media/iot-hub-store-data-in-azure-table-storage/3_create-route.png)
  
### <a name="add-a-route-for-hot-path-telemetry-optional"></a>Adicionar uma rota de telemetria de afunilamento (opcional)

Por padrão, o Hub IoT roteia todas as mensagens que não correspondem a nenhuma outra rota para o ponto de extremidade interno. Como todas as mensagens de telemetria agora correspondem à regra que roteia as mensagens para o armazenamento, você precisa adicionar outra rota para que mensagens sejam gravadas no ponto de extremidade interno. Não há nenhum custo adicional para rotear mensagens para vários pontos de extremidade.

> [!NOTE]
> Você pode ignorar esta etapa se não estiver fazendo um processamento adicional em suas mensagens de telemetria.

1. Clique em **Adicionar** no painel de Rotas e digite um nome para a rota. 

2. Selecione **Mensagens de Dispositivo** como a fonte de dados e **eventos** como o ponto de extremidade. 

3. Digite `true` como a cadeia de caracteres de consulta e, em seguida, clique em **Salvar**.

  ![Criar uma rota de afunilamento no Hub IoT](./media/iot-hub-store-data-in-azure-table-storage/4_hot-path-route.png)

## <a name="verify-your-message-in-your-storage-container"></a>Verificar a mensagem no contêiner de armazenamento

1. Execute o aplicativo de exemplo em seu dispositivo para enviar mensagens para o Hub IoT.

2. [Baixe e instale o Gerenciador de Armazenamento do Azure](http://storageexplorer.com/).

3. Abra o Gerenciador de Armazenamento, clique em **Adicionar uma Conta do Azure** > **Entrar** e entre em sua conta do Azure.

4. Clique em sua assinatura do Azure > **Contas de Armazenamento** > sua conta de armazenamento > **Contêineres de Blobs** > seu contêiner.

   Você deve ver as mensagens enviadas do seu dispositivo ao seu Hub IoT registradas no contêiner de blobs.

## <a name="clean-up-resources"></a>Limpar recursos 

Neste tutorial, você adicionou uma conta de armazenamento e adicionou o roteamento de mensagens do Hub IoT para gravação na conta de armazenamento. Para limpar os recursos que você criou, remova as informações de roteamento e, em seguida, exclua a conta de armazenamento. 

1. Faça logon no [Portal do Azure](https://portal.azure.com).

2. Clique em **Grupos de recursos** e selecione o grupo de recursos que você usou. A lista de recursos do grupo é exibida. 

   > [!NOTE]
   > Se você quiser remover todos os recursos do grupo de recursos, clique em **Excluir** para excluir o grupo de recursos e, em seguida, siga as instruções. Isso removerá tudo nesse grupo de recursos, concluindo a limpeza de todos os recursos e permitindo que você passe para a próxima seção.

3. Clique no Hub IoT usado para este tutorial. 

4. No painel do Hub IoT, clique em **Rotas**. Clique na caixa de seleção ao lado da regra de roteamento que você adicionou e selecione **Excluir**. Quando for perguntado se você tem certeza de que deseja excluir essa rota, clique em **Sim**.

   ![Remover regra de roteamento](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-routing.png)

   Feche o painel Roteamento. Você retornará para o painel Grupo de Recursos.

5. Clique novamente no Hub IoT. 

6. No painel do Hub IoT, clique em **Pontos de Extremidade**. Clique na caixa de seleção ao lado do ponto de extremidade que você adicionou para o contêiner de armazenamento e selecione **Excluir**. Quando for perguntado se você tem certeza de que deseja excluir o ponto de extremidade selecionado, clique em **Sim**.

    ![Remover ponto de extremidade](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-endpoint.png)

    Feche o painel Pontos de Extremidade. Você retornará para o painel Grupo de Recursos. 

7.  Clique na conta de armazenamento que você configurou para este tutorial. 

8.  No painel Conta de armazenamento, clique em **Excluir** para remover a conta de armazenamento. Você será direcionado ao painel **Excluir conta de armazenamento**.

   ![Remover conta de armazenamento](./media/iot-hub-store-data-in-azure-table-storage/cleanup-remove-storageaccount.png)

8.  Digite o nome da conta de armazenamento e, em seguida, clique em **Excluir** na parte inferior do painel. 

## <a name="next-steps"></a>Próximas etapas

Você criou com êxito sua conta de armazenamento do Azure e as mensagens roteadas do Hub IoT para um contêiner de blobs na conta de armazenamento.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]
