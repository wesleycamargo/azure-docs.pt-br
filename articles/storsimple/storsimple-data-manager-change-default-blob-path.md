---
title: "Alterar o caminho de blob padrão | Microsoft Docs"
description: "Saiba como configurar uma Azure Function para renomear um caminho de arquivo de Blob (versão prévia particular)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 8a06e4b3f482943f55e998c3c68857d3530ff98a
ms.lasthandoff: 03/21/2017

---

# <a name="change-blob-path-from-the-default-private-preview"></a>Alterar o caminho de blob padrão (Versão prévia particular)

Este artigo descreve como configurar uma função do Azure para renomear um caminho de arquivo de Blob padrão. 

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, verifique se você tem:
* Uma definição de trabalho que foi configurada corretamente em um Recurso de Dados Híbridos dentro de um grupo de recursos.

## <a name="create-an-azure-function"></a>Criar uma função do Azure

Siga as etapas a seguir para criar uma função do Azure.

#### <a name="to-create-an-azure-function"></a>Para criar uma Azure Function

1. Vá para o [portal do Azure](http://portal.azure.com/).

2. Clique em **+ Novo** no canto superior esquerdo. Digite “Aplicativo de funções” na caixa de texto **Pesquisa** e pressione **Enter**.

    ![Acessar o recurso Aplicativo de funções](./media/storsimple-data-manager-change-default-blob-path/goto-function-app-resource.png)

3. Clique em **Aplicativo de funções** nos resultados.

    ![Selecionar recurso Aplicativo de funções](./media/storsimple-data-manager-change-default-blob-path/select-function-app-resource.png)

4. Abra a janela **Aplicativo de funções** e clique em **Criar**.

    ![Criar novo aplicativo de funções](./media/storsimple-data-manager-change-default-blob-path/create-new-function-app.png)

5. Na folha **Configuração**, insira todas as entradas e clique em **Criar**.

    1. Nome do aplicativo
    2. Assinatura
    3. Grupo de recursos
    4. Plano de hospedagem – **Plano de consumo**
    5. Local
    6. Conta de armazenamento – use uma conta de armazenamento existente ou crie uma nova. Uma conta de armazenamento é usada internamente para a função.

        ![Inserir novos dados de configuração do Aplicativo de funções](./media/storsimple-data-manager-change-default-blob-path/enter-new-funcion-app-data.png)

6. Depois de criar o aplicativo de funções, navegue para **Mais serviços >** na parte inferior esquerda. Digite “Serviços de Aplicativos” na caixa de texto **Filtro** e clique em **Serviços de Aplicativos**.

    ![Mais serviços >](./media/storsimple-data-manager-change-default-blob-path/more-services.png)

7. Clique em **Nome do aplicativo de funções** na lista de Serviços de aplicativos.

8. Clique em **+ Nova Função**. Selecione **C#** na lista suspensa **Linguagem**. Selecione a opção **QueueTrigger-CSharp** na lista de modelos. Insira todas as entradas.

   1. Nome – forneça um nome para a função.
   2. Nome da fila – insira o **nome da Definição de Trabalho de Transformação de Dados**.
   3. Conexão da conta de armazenamento – clique na opção **Novo**. Selecione a conta correspondente ao trabalho de Transformação de Dados.
      
      Anote `Connection name`. Esse nome é necessário mais tarde na função do Azure.

   4. Clique no botão **Criar**.

       ![Criar uma nova função C Sharp >](./media/storsimple-data-manager-change-default-blob-path/create-new-csharp-function.png)

9. Na janela **Função**, execute o arquivo _.csx_. Copie e cole o seguinte código:

    ```
        using System;
        using System.Configuration;
        using Microsoft.WindowsAzure.Storage.Blob;
        using Microsoft.WindowsAzure.Storage.Queue;
        using Microsoft.WindowsAzure.Storage;
        using System.Collections.Generic;
        using System.Linq;

        public static void Run(QueueItem myQueueItem, TraceWriter log)
        {
            log.Info($"Blob Uri: {myQueueItem.TargetLocation}");
            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(ConfigurationManager.AppSettings["STORAGE_CONNECTIONNAME"]);

            // Create the blob client.
            CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

            string uriString = "windows.net/";
            string containerName = myQueueItem.TargetLocation;
            containerName = containerName.Substring(containerName.IndexOf(uriString) + uriString.Length);
            containerName = containerName.Substring(0, containerName.IndexOf("/"));
            //log.Info($"Container name: {containerName}");

            CloudBlobContainer container = blobClient.GetContainerReference(containerName);

            if(!container.Exists())
            {
                log.Info($"Container - {containerName} not exists");
                return;
            }

            string containerUri = string.Format("{0}/", container.Uri.ToString());  // Reading container Uri
            string blobUri =  myQueueItem.TargetLocation;
            string blobName = blobUri.Replace(containerUri, string.Empty).Replace("%20", " "); // Reading existing file path after container name
            string newBlobName = blobName.Substring(blobName.IndexOf(string.Format("{0}/", container.Name)) + container.Name.Length + 1); // Reading actual File path after container name

            log.Info($"Blob name: {blobName}");
            log.Info($"New blob name: {newBlobName}");

            CloudBlockBlob blobCopy = container.GetBlockBlobReference(newBlobName);
            CloudBlockBlob blob = container.GetBlockBlobReference(blobName);
            if (!blobCopy.Exists())
            {
                blobCopy.StartCopy(blob);
                // Delete old blob, after copy the blob
                blob.DeleteIfExists();
                log.Info($"Blob file path renamed completed successfully");
            }
            else
            {
                log.Info($"Blob file path renamed already done");
                // Delete old blob, if already exists.
                blob.DeleteIfExists();
            }
        }

        public class QueueItem
        {
            public string SourceLocation {get;set;}
            public long SizeInBytes {get;set;}
            public string Status {get;set;}
            public string JobID {get;set;}
            public string TargetLocation {get; set;}
        }
    
    ```

   1. Substitua o **STORAGE_CONNECTIONNAME** exato na linha 12 pela Conexão da conta de armazenamento (consulte o ponto 8c).
   2. Clique no botão **Salvar** na parte superior esquerda.

       ![Salvar função >](./media/storsimple-data-manager-change-default-blob-path/save-function.png)

10.  Clique em **Exibir arquivos** no canto direito.

    ![Exibir arquivos](./media/storsimple-data-manager-change-default-blob-path/view-files.png)

   1. Clique em **+ Adicionar**. Digite **project.json** e pressione **Enter**.
   2. Copie e cole o código a seguir no arquivo **project.json**.

        ```
            {
            "frameworks": {
                "net46":{
                "dependencies": {
                    "windowsazure.storage": "8.1.1"
                }
                }
            }
            }
        
        ```

   2. Clique em **Salvar**.

Você criou uma função do Azure. Essa função é disparada sempre que um novo blob é gerado pelo trabalho de Transformação de Dados.

## <a name="next-steps"></a>Próximas etapas

[Use a interface do usuário do Gerenciador de Dados StorSimple para transformar seus dados](storsimple-data-manager-ui.md).

