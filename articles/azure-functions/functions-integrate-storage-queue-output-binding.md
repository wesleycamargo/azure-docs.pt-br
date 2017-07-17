---
title: "Criar uma função no Azure disparada por mensagens na fila | Microsoft Docs"
description: "Use o Azure Functions para criar uma função sem servidor que é invocada por uma mensagem enviada para uma fila do Armazenamento do Azure."
services: azure-functions
documentationcenter: na
author: ggailey777
manager: erikre
editor: 
tags: 
ms.assetid: 0b609bc0-c264-4092-8e3e-0784dcc23b5d
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 05/02/2017
ms.author: glenga
ms.custom: mvc
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: d1ddfbe9a0a0c7c7e0a060776938bd68a87e1ba5
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
<a id="add-messages-to-an-azure-storage-queue-using-functions" class="xliff"></a>

# Adicionar mensagens a uma fila do Armazenamento do Azure usando o Functions

No Azure Functions, associações de entrada e saída fornecem uma maneira declarativa para se conectar a dados de serviço externo de sua função. Neste tópico, aprenda como atualizar uma função existente, adicionando uma associação de saída que envia mensagens para o Armazenamento de Filas do Azure.  

![Exiba a mensagem nos logs.](./media/functions-integrate-storage-queue-output-binding/functions-integrate-storage-binding-in-portal.png)

<a id="prerequisites" class="xliff"></a>

## Pré-requisitos 

[!INCLUDE [Previous topics](../../includes/functions-quickstart-previous-topics.md)]

* Instale o Experimente usar o [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/).

[!INCLUDE [functions-portal-favorite-function-apps](../../includes/functions-portal-favorite-function-apps.md)] 

## <a name="add-binding"></a>Adicionar uma associação de saída
 
1. Expanda seu aplicativo de funções e sua função.

2. Selecione **Integrar** e **+ Nova saída**, selecione **Armazenamento de filas do Azure** e selecione **Selecionar**.
    
    ![Adicione uma associação de saída de Armazenamento de Filas a uma função no Portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding.png)

3. Use então as configurações especificadas na tabela e selecione **Salvar**: 

    ![Adicione uma associação de saída de Armazenamento de Filas a uma função no Portal do Azure.](./media/functions-integrate-storage-queue-output-binding/function-add-queue-storage-output-binding-2.png)

    | Configuração      |  Valor sugerido   | Descrição                              |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nome da fila**   | myqueue-items    | Nome da fila à qual se conectar em sua conta de armazenamento. |
    | **Conexão da conta de armazenamento** | AzureWebJobStorage | Você pode usar a conexão da conta de armazenamento que já está sendo usada por seu aplicativo de funções ou criar uma nova.  |
    | **Nome do parâmetro de mensagem** | outQueueItem | O nome do parâmetro de associação de saída. | 

Agora que você tem uma associação de saída definida, você precisa atualizar o código para usar a associação para adicionar mensagens a uma fila.  

<a id="update-the-function-code" class="xliff"></a>

## Atualizar o código de função

1. Selecione sua função para exibir o código de função no editor. 

2. Para uma função C#, atualize sua definição de função como demonstrado a seguir para adicionar o parâmetro de associação de armazenamento **outQueueItem**. Ignore esta etapa para uma função JavaScript.

    ```cs   
    public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, 
        ICollector<string> outQueueItem, TraceWriter log)
    {
        ....
    }
    ```

3. Adicione o código a seguir à função logo antes do método retornar. Use o trecho de código apropriado para a linguagem de programação de sua função.

    ```javascript
    context.bindings.outQueueItem = "Name passed to the function: " + 
                (req.query.name || req.body.name);
    ```

    ```cs
    outQueueItem.Add("Name passed to the function: " + name);     
    ```

4. Selecione **Salvar** para salvar as alterações.

O valor passado para o gatilho HTTP é incluído em uma mensagem adicionada à fila.
 
<a id="test-the-function" class="xliff"></a>

## Testar a função 

1. Depois que as alterações de código forem salvas, selecione **Executar**. 

    ![Adicione uma associação de saída de Armazenamento de Filas a uma função no Portal do Azure.](./media/functions-integrate-storage-queue-output-binding/functions-test-run-function.png)

2. Confira os logs para verificar se a função foi bem-sucedida. Uma nova fila denominada **outqueue** é criada na sua conta de armazenamento pelo tempo de execução do Functions quando a associação de saída é usada pela primeira vez.

Em seguida, você pode se conectar à sua conta de armazenamento para verificar a nova fila e a mensagem que você adicionou a ela. 

<a id="connect-to-the-queue" class="xliff"></a>

## Conectar-se à fila

Ignore as três primeiras etapas se você já tiver instalado o Gerenciador de Armazenamento e o conectado à sua conta de armazenamento.    

1. Em sua função, selecione **Integrar** e na nova associação de saída **Armazenamento de Filas do Azure**, em seguida, expanda **Documentação**. Copie o **Nome da conta** e a **Chave de conta**. Você usa essas credenciais para conectar-se à conta de armazenamento.
 
    ![Obtenha as credenciais de conexão da conta de armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-get-storage-account-credentials.png)

2. Execute a ferramenta [Gerenciador de Armazenamento do Microsoft Azure](http://storageexplorer.com/), selecione o ícone conectar-se à esquerda, escolha **Usar um nome e chave de conta de armazenamento** e selecione **Avançar**.

    ![Execute a ferramenta Gerenciador de Conta de Armazenamento.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-1.png)
    
3. Cole o **Nome da conta** e a **Chave de conta** da etapa 1 em seus campos correspondentes e selecione **Avançar** e **Conectar**. 
  
    ![Cole as credenciais de armazenamento e conecte-se.](./media/functions-integrate-storage-queue-output-binding/functions-storage-manager-connect-2.png)

4. Expanda a conta de armazenamento anexada, clique com o botão direito do mouse em **Filas** e verifique se uma fila chamada **myqueue-items** existe. Você também deverá ver uma mensagem já presente na fila.  
 
    ![Crie uma fila de armazenamento.](./media/functions-integrate-storage-queue-output-binding/function-queue-storage-output-view-queue.png)
 

<a id="clean-up-resources" class="xliff"></a>

## Limpar recursos

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

<a id="next-steps" class="xliff"></a>

## Próximas etapas

Você adicionou uma associação de saída a uma função existente. 

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Para obter mais informações sobre associação a Armazenamento de Filas, consulte [Associações de fila do Armazenamento do Azure Functions](functions-bindings-storage-queue.md). 




