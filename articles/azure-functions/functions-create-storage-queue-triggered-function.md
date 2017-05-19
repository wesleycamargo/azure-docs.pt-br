---
title: "Criar uma função no Azure disparada por mensagens na fila | Microsoft Docs"
description: "Use o Azure Functions para criar uma função sem servidor que é invocada por uma mensagem enviada para uma fila do Armazenamento do Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 361da2a4-15d1-4903-bdc4-cc4b27fc3ff4
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 04/25/2017
ms.author: glenga
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 93b3910ba2f95bb6f3228926a51f71f96ccd1e23
ms.contentlocale: pt-br
ms.lasthandoff: 05/10/2017


---
# <a name="create-a-function-triggered-by-azure-queue-storage"></a>Criar uma função disparada pelo Armazenamento de Filas do Azure

Saiba como criar uma função disparada quando as mensagens são enviadas para uma fila do Armazenamento do Azure.  

![Exiba a mensagem nos logs.](./media/functions-create-storage-queue-triggered-function/function-app-in-portal-editor.png)

Deve levar menos de cinco minutos para concluir todas as etapas neste tópico.

## <a name="prerequisites"></a>Pré-requisitos

[!INCLUDE [Previous quickstart note](../../includes/functions-quickstart-previous-topics.md)]

Você também precisa baixar e instalar o [Gerenciador do Armazenamento do Microsoft Azure](http://storageexplorer.com/). 

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="create-function"></a>Criar uma função disparada por Filas

Expanda seu aplicativo de funções, clique no botão **+** ao lado de **Funções**, clique no modelo **QueueTrigger** para a linguagem de programação desejada. Use então as configurações especificadas na tabela e, em seguida, clique em **Criar**.

![Crie a função disparada por filas de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-create-queue-storage-trigger-portal.png)
    
| Configuração      |  Valor sugerido   | Descrição                                        |
| ------------ |  ----------------- | -------------------------------------------------- |
| **Nome da fila**   | myqueue-items    | Nome da fila à qual se conectar em sua conta de armazenamento. |
| **Conexão da conta de armazenamento** | AzureWebJobStorage | Você pode usar a conexão da conta de armazenamento que já está sendo usada por seu aplicativo de funções ou criar uma nova.  |
| **Nomeie sua função** | Exclusivo no aplicativo de funções | O nome dessa função disparada por filas. |  

Em seguida, você pode se conectar à sua conta de armazenamento do Azure e criar a fila de armazenamento **myqueue-items**.

## <a name="create-the-queue"></a>Criar a fila

1. Em sua função, clique em **Integrar**, expanda **Documentação**e copie **Nome da conta** e **Chave de conta**. Você usa essas credenciais para conectar-se à conta de armazenamento. Se você já tiver se conectado à conta de armazenamento, vá para a etapa 4.
 
    ![Obtenha as credenciais de conexão da conta de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-account-connection.png)v

2. Execute a ferramenta [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/), clique no ícone conectar-se à esquerda, escolha **Usar um nome e chave de conta de armazenamento** e clique em **Avançar**.

    ![Execute a ferramenta Gerenciador de Conta de Armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-1.png)
    
3. Insira o **Nome da conta** e **Chave de conta** da etapa 1, clique em **Avançar** e em **Conectar**. 
  
    ![Insira as credenciais de armazenamento e conecte-se.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-connect-2.png)

4. Expanda a conta de armazenamento anexada, clique com o botão direito do mouse em **Filas**, clique em **Criar fila**, digite `myqueue-items` e pressione enter.
 
    ![Crie uma fila de armazenamento.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-create-queue.png)

Agora que você tem uma fila de armazenamento, você pode testar a função adicionando uma mensagem à fila.  

## <a name="test-the-function"></a>Testar a função

1. De volta ao Portal do Azure, navegue até sua função, expanda os **Logs** na parte inferior da página e verifique se o streaming de log não está em pausa.

2. No Gerenciador de Armazenamento, expanda sua conta de armazenamento, **Filas** e **myqueue-items**; em seguida, clique em **Adicionar mensagem**. 

    ![Adicione uma mensagem à fila.](./media/functions-create-storage-queue-triggered-function/functions-storage-manager-add-message.png)

2. Digite sua mensagem "Olá, Mundo!" em **Texto da mensagem** e clique em **OK**.
 
3. Aguarde alguns segundos, depois volte para seus logs de função e verifique se a nova mensagem foi lida da fila. 

    ![Exiba a mensagem nos logs.](./media/functions-create-storage-queue-triggered-function/functions-queue-storage-trigger-view-logs.png)

4. No Gerenciador de Armazenamento, clique em **Atualizar** e verifique se a mensagem foi processada e se não está mais na fila.

## <a name="clean-up-resources"></a>Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Próximas etapas

Você criou uma função que é executada quando uma mensagem é adicionada a uma fila de armazenamento. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre gatilhos de Armazenamento de Filas, consulte [Associações de fila do Armazenamento do Azure Functions](functions-bindings-storage-queue.md). 




