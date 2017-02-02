---
title: "Introdução ao Armazenamento de Filas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET) | Microsoft Docs"
description: "Como começar a usar o Armazenamento de Filas do Azure em um projeto do ASP.NET no Visual Studio após a conexão a uma conta de armazenamento usando os Serviços Conectados do Visual Studio"
services: storage
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 94ca3413-5497-433f-abbe-836f83a9de72
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 12/02/2016
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: f58c2b522f81dc2dc86f0d2c6bc4872504cf7377
ms.openlocfilehash: 70875287e79aaf49e1b8802cf2953cd5381b97f4


---
# <a name="get-started-with-azure-queue-storage-and-visual-studio-connected-services-aspnet"></a>Introdução ao Armazenamento de Filas do Azure e aos Serviços Conectados do Visual Studio (ASP.NET)
[!INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-queues.md)]

## <a name="overview"></a>Visão geral

Armazenamento de Filas do Azure é um serviço para armazenar grandes quantidades de dados não estruturados que podem ser acessados via HTTP ou HTTPS. Uma única mensagem de fila pode ter até 64 KB de tamanho e uma fila pode conter uma quantidade ilimitada de mensagens, até o limite de capacidade total de uma conta de armazenamento.

Este artigo descreve como gerenciar programaticamente entidades do Armazenamento de Filas do Azure, executar tarefas comuns como criar uma fila do Azure e adicionar, modificar, ler e remover mensagens da fila.

> [!NOTE]
> 
> As seções de código neste artigo pressupõem que você já se conectou a uma conta de armazenamento do Azure usando os Serviços Conectados. Os Serviços Conectados são configurados abrindo o Gerenciador de Soluções do Visual Studio, clicando no projeto e, no menu de contexto, selecionando a opção **Adicionar->Serviço Conectado**. A partir daí, siga as instruções da caixa de diálogo para conectar-se à conta de armazenamento desejada do Azure.      

## <a name="create-a-queue"></a>Criar uma fila

As etapas a seguir ilustram como criar programaticamente uma fila. Em um aplicativo ASP.NET MVC, o código iria em um controlador.

1. Adicione as seguintes diretivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto **CloudQueue** que representa uma referência ao nome da fila desejada. (Mude *<queue-name>* para o nome da fila que você deseja criar.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chame o método **CloudQueue.CreateIfNotExists** para criar a fila se ela ainda não existir. 

        queue.CreateIfNotExists();


## <a name="add-a-message-to-a-queue"></a>Adicionar uma mensagem a uma fila

As etapas a seguir ilustram como adicionar programaticamente uma mensagem a uma fila. Em um aplicativo ASP.NET MVC, o código iria em um controlador.

1. Adicione as seguintes diretivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto **CloudQueue** que representa uma referência ao nome da fila desejada. (Mude *<queue-name>* para o nome da fila a qual você deseja adicionar a mensagem.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Crie o objeto **CloudQueueMessage** que representa a mensagem que você deseja adicionar à fila. Um objeto **CloudQueueMessage** pode ser criado por meio de uma cadeia de caracteres (em formato UTF-8) ou de uma matriz de bytes. (Mude * <queue-message> * para a mensagem que você deseja adicionar.

        CloudQueueMessage message = new CloudQueueMessage(<queue-message>);

6. Chame o método **CloudQueue.AddMessage** para adicionar a mensagem à fila.

        queue.AddMessage(message);

## <a name="read-a-message-from-a-queue-without-removing-it"></a>Ler uma mensagem de uma fila sem removê-la

As etapas a seguir ilustram como espiar programaticamente uma mensagem em fila (ler a primeira mensagem sem removê-la). Em um aplicativo ASP.NET MVC, o código iria em um controlador. 

1. Adicione as seguintes diretivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto **CloudQueue** que representa uma referência à fila. (Mude *<queue-name>* para o nome da fila da qual você deseja ler uma mensagem.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chame o método **CloudQueue.PeekMessage** para ler a mensagem no início de uma fila sem removê-la da fila.

        CloudQueueMessage message = queue.PeekMessage();

6. Acesse o valor do objeto **CloudQueueMessage** usando a propriedade **CloudQueueMessage.AsBytes** ou **CloudQueueMessage.AsString**.

        string messageAsString = message.AsString;
        byte[] messageAsBytes = message.AsBytes;

## <a name="read-and-remove-a-message-from-a-queue"></a>Ler e remover uma mensagem de uma fila

As etapas a seguir ilustram como ler programaticamente uma mensagem em fila e, depois, exclui-la. Em um aplicativo ASP.NET MVC, o código iria em um controlador. 

1. Adicione as seguintes diretivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto **CloudQueue** que representa uma referência à fila. (Mude *<queue-name>* para o nome da fila da qual você deseja ler uma mensagem.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chame o método **CloudQueue.GetMessage** para ler a primeira mensagem na fila. O método **CloudQueue.GetMessage** torna a mensagem invisível por 30 segundos (por padrão) para qualquer outro código que esteja lendo as mensagens, para que nenhum outro código possa modificar ou excluir a mensagem durante o seu processamento. Para alterar o tempo durante o qual a mensagem fica invisível, modifique o parâmetro **visibilityTimeout** que está sendo passado para o método **CloudQueue.GetMessage**.

        // This message will be invisible to other code for 30 seconds.
        CloudQueueMessage message = queue.GetMessage();     

6. Chame o método **CloudQueueMessage.Delete** para excluir a mensagem da fila.

        queue.DeleteMessage(message);

## <a name="get-the-queue-length"></a>Obter o tamanho da fila

As etapas a seguir ilustram como obter programaticamente o tamanho da fila (número de mensagens). Em um aplicativo ASP.NET MVC, o código iria em um controlador. 

1. Adicione as seguintes diretivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto **CloudQueue** que representa uma referência à fila. (Mude *<queue-name>* para o nome da fila cujo tamanho você está consultando.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chame o método **CloudQueue.FetchAttributes** para recuperar os atributos da fila (incluindo seu tamanho). 

        queue.FetchAttributes();

6. Acesse a propriedade **CloudQueue.ApproximateMessageCount** para alterar o tamanho da fila.
 
        int? nMessages = queue.ApproximateMessageCount;

## <a name="delete-a-queue"></a>Excluir uma fila
As etapas a seguir ilustram como excluir programaticamente uma fila. 

1. Adicione as seguintes diretivas *using*:
   
        using Microsoft.Azure;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Queue;

2. Obtenha um objeto **CloudStorageAccount** que represente as informações da conta de armazenamento. Use o seguinte código para obter a cadeia de conexão de armazenamento e informações de conta de armazenamento da configuração do serviço do Azure. (Mude *<storage-account-name>* para o nome da conta de armazenamento do Azure que você está acessando.)

         CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
           CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

3. Obtenha um objeto **CloudQueueClient** que representa um cliente do serviço de fila.

        CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

4. Obtenha um objeto **CloudQueue** que representa uma referência à fila. (Mude *<queue-name>* para o nome da fila cujo tamanho você está consultando.)

        CloudQueue queue = queueClient.GetQueueReference(<queue-name>);

5. Chame o método **CloudQueue.Delete** para excluir a fila representada pelo objeto **CloudQueue**.

        messageQueue.Delete();

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]




<!--HONumber=Dec16_HO2-->


