---
title: "Criar uma Função do Azure vinculada a um serviço do Azure | Microsoft Docs"
description: "Crie uma Função do Azure, um aplicativo sem servidor, que interage com outros Serviços do Azure."
services: functions
documentationcenter: dev-center-name
author: yochay
manager: manager-alias
editor: 
tags: 
keywords: "funções do azure, funções, processamento de eventos, webhooks, computação dinâmica, arquitetura sem servidor"
ms.assetid: ab86065d-6050-46c9-a336-1bfc1fa4b5a1
ms.service: functions
ms.devlang: multiple
ms.topic: get-started-article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 10/25/2016
ms.author: rachelap@microsoft.com
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9ffd199c9e3c621a808ade109ed044b6c9b689b7


---
# <a name="create-an-azure-function-which-binds-to-an-azure-service"></a>Criar uma Função do Azure vinculada a um serviço do Azure
[!INCLUDE [Getting Started Note](../../includes/functions-getting-started.md)]

Neste breve vídeo, você aprenderá como criar uma Função do Azure que escuta mensagens em uma Fila do Azure e copia as mensagens para um Blob do Azure.

## <a name="watch-the-video"></a>Assista ao vídeo
>[!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Web-Sites-Tutorials/Create-an-Azure-Function-which-binds-to-an-Azure-service/player]
>
>

## <a name="create-an-input-queue-trigger-function"></a>Criar uma função de gatilho de fila de entrada
O objetivo dessa função é gravar uma mensagem em uma fila a cada 10 segundos. Para fazer isso, você deve criar filas de mensagem e de função e adicionar o código para gravar mensagens nas filas recém-criadas.

1. Acesse o Portal do Azure e localize o Aplicativo de Função do Azure.
2. Clique em **Nova Função** > **TimerTrigger - Nó**. Nomeie a função como **FunctionsBindingsDemo1**
3. Digite um valor de "0/10 * * * * *" para a Agenda. Esse valor está na forma de uma expressão cron. Isso agendará o temporizador para execução a cada 10 segundos.
4. Clique no botão **Criar** para criar a função.
   
    ![Adicionar funções de temporizador de gatilho](./media/functions-create-an-azure-connected-function/new-trigger-timer-function.png)
5. Verifique se a função funciona exibindo a atividade no log. Você talvez precise clicar no link **Logs** no canto superior direito para exibir o painel de log.
   
   ![Verifique se a função funciona exibindo o log](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-view-log.png)

### <a name="add-a-message-queue"></a>Adicionar uma fila de mensagens
1. Vá para a guia **Integrar**.
2. Escolha **Nova Saída** > **Fila de Armazenamento do Azure** > **Selecionar**.
3. Digite **myQueueItem** na caixa de texto **Nome de parâmetro de mensagem**.
4. Selecione uma conta de armazenamento ou clique em **novo** para criar uma conta de armazenamento, se não tiver uma.
5. Digite **functions-bindings** na caixa de texto **Nome da fila**.
6. Clique em **Salvar**.  
   
   ![Adicionar funções de temporizador de gatilho](./media/functions-create-an-azure-connected-function/functionsbindingsdemo1-integrate-tab.png)

### <a name="write-to-the-message-queue"></a>Gravar na fila de mensagens
1. Volte para a guia **Desenvolver** e adicione o seguinte código para a função após o código existente:
   
    ```javascript
   
    function myQueueItem() 
      {
        return {
        msg: "some message goes here",
        time: "time goes here"
      }
    }
   
    ```
2. Modifique o código de função existente para chamar o código adicionado na Etapa 1. Insira o seguinte código ao redor da linha 9 da função, após a instrução *if*.
   
    ```javascript
   
    var toBeQed = myQueueItem();
    toBeQed.time = timeStamp;
    context.bindings.myQueue = toBeQed;
   
    ```
   
    Esse código cria um **myQueueItem** e define sua propriedade **time** com o carimbo de data/hora atual. Em seguida, adiciona o novo item de fila à associação de myQueue do contexto.
3. Clique em **Salvar e Executar**.
4. Verifique se o código funciona exibindo a fila no Visual Studio.
   
   * Abra o Visual Studio e vá para **Exibir** > **Nuvem** **Explorador**.
   * Localize a conta de armazenamento e a fila **functions-bindings** usada ao criar a fila myQueue. Você deve ver linhas de dados de log. Talvez seja necessário entrar no Azure por meio do Visual Studio.  

## <a name="create-an-output-queue-trigger-function"></a>Criar uma função de gatilho de fila de saída
1. Clique em **Nova Função** > **QueueTrigger - C#**. Nomeie a função como **FunctionsBindingsDemo2**. Observe que você pode combinar linguagens no mesmo aplicativo de função (Nó e C#, neste caso).
2. Digite **functions-bindings** no campo **Nome da fila**. "
3. Selecione uma conta de armazenamento para usar ou crie uma nova.
4. Clique em **Criar**
5. Verifique se a nova função funciona exibindo o log da função e o Visual Studio para ver atualizações. O log da função mostra que a função está em execução e os itens são removidos da fila. Como a função está associada à fila de saída **functions-bindings** como um gatilho de entrada, atualizar a fila **functions-bindings** no Visual Studio deve revelar que os itens desapareceram. Eles foram removidos da fila.   
   
   ![Adicionar funções de temporizador de fila de saída](./media/functions-create-an-azure-connected-function/functionsbindingsdemo2-integrate-tab.png)   

### <a name="modify-the-queue-item-type-from-json-to-object"></a>Modificar o tipo de item de fila de JSON para objeto
1. Substitua o código em **FunctionsBindingsDemo2** pelo seguinte código:    
   
    ```cs
   
    using System;
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
   
    public class QItem
    {
      public string Msg { get; set;}
      public string Time { get; set;}
    }
   
    ```
   
    Este código adiciona duas classes, **TableItem** e **QItem**, que você usa para ler e gravar em filas. Além disso, a função **Run** foi modificada para aceitar os parâmetros **QItem** e **TraceWriter**, em vez de uma **cadeia de caracteres** e um **TraceWriter**. 
2. Clique no botão **Salvar** .
3. Verifique se o código funciona conferindo o log. Observe que as funções do Azure automaticamente serializam e desserializam o objeto para você, facilitando o acesso à fila de forma orientada a objetos para passar dados. 

## <a name="store-messages-in-an-azure-table"></a>Armazenar mensagens em uma Tabela do Azure
Agora que as filas estão trabalhando juntas, é hora de adicionar uma tabela do Azure para armazenamento permanente dos dados de fila.

1. Vá para a guia **Integrar**.
2. Crie uma Tabela de Armazenamento do Azure para saída e nomeie-a como **myTable**.
3. Responda **functionsbindings** à pergunta "Em qual tabela os dados devem ser gravados?".
4. Altere a configuração **PartitionKey** de **{project-id}** para **{partition}**.
5. Escolha uma conta de armazenamento ou crie uma nova.
6. Clique em **Salvar**.
7. Vá para a guia **Desenvolver**.
8. Crie uma classe **TableItem** para representar uma tabela do Azure e modifique a função Run para aceitar o objeto TableItem recém-criado. Observe que você deve usar as propriedades **PartitionKey** e **RowKey** para que ela funcione.
   
    ```cs
   
    public static void Run(QItem myQueueItem, ICollector<TableItem> myTable, TraceWriter log)
    {    
      TableItem myItem = new TableItem
      {
        PartitionKey = "key",
        RowKey = Guid.NewGuid().ToString(),
        Time = DateTime.Now.ToString("hh.mm.ss.ffffff"),
        Msg = myQueueItem.Msg,
        OriginalTime = myQueueItem.Time    
      };
   
      log.Verbose($"C# Queue trigger function processed: {myQueueItem.RowKey} | {myQueueItem.Msg} | {myQueueItem.Time}");
    }
   
    public class TableItem
    {
      public string PartitionKey {get; set;}
      public string RowKey {get; set;}
      public string Time {get; set;}
      public string Msg {get; set;}
      public string OriginalTime {get; set;}
    }
    ```
9. Clique em **Salvar**.
10. Verifique se o código funciona exibindo os logs da função e no Visual Studio. Para verificar no Visual Studio, use o **Gerenciador de Nuvem** para navegar até a Tabela do Azure **functionbindings** e verifique se há linhas nela.

[!INCLUDE [Getting Started Note](../../includes/functions-bindings-next-steps.md)]

[!INCLUDE [Getting Started Note](../../includes/functions-get-help.md)]




<!--HONumber=Nov16_HO2-->


