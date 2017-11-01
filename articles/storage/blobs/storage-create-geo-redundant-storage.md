---
title: "Tornar os dados de aplicativos altamente disponíveis no Azure | Microsoft Docs"
description: "Use o armazenamento com redundância geográfica com acesso de leitura para tornar os dados do aplicativo altamente disponíveis"
services: storage
documentationcenter: 
author: georgewallace
manager: timlt
editor: 
ms.service: storage
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: csharp
ms.topic: tutorial
ms.date: 10/12/2017
ms.author: gwallace
ms.custom: mvc
ms.openlocfilehash: 547ca7843f53bd11fdb922af8e0ae77e38f813d9
ms.sourcegitcommit: a7c01dbb03870adcb04ca34745ef256414dfc0b3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/17/2017
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Torne os dados do aplicativo altamente disponíveis com o armazenamento do Azure

Este tutorial é a primeira parte de uma série. Este tutorial mostra como tornar os dados do aplicativo altamente disponíveis no Azure. Quando você terminar, terá um aplicativo de console que carrega e recupera um blob para uma conta de armazenamento RA-GRS ([redundância de área geográfica com acesso de leitura](../common/storage-redundancy.md#read-access-geo-redundant-storage)). RA-GRS funciona por meio da replicação de transações da região primária para a secundária. Esse processo de replicação garante que os dados na região secundária acabem sendo consistentes. O aplicativo usa o padrão de [Disjuntor](/azure/architecture/patterns/circuit-breaker.md) para determinar a qual ponto de extremidade se conectar. O aplicativo muda para um ponto de extremidade secundário quando uma falha é simulada.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Baixar o exemplo
> * Definir a cadeia de conexão
> * Executar o aplicativo de console

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

* Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
  - **Desenvolvimento do Azure**

  ![Desenvolvimento do Azure (em Web e Nuvem)](media/storage-create-geo-redundant-storage/workloads.png)

* Baixe e instale o [Fiddler](https://www.telerik.com/download/fiddler)

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure.

Siga estas etapas para criar uma conta de armazenamento com redundância geográfica com acesso de leitura:

1. Selecione no botão **Novo** encontrado à esquerda superior do Portal do Azure.

2. Selecione **Armazenamento** na página **Novo** e selecione **Conta de armazenamento – blob, arquivo, tabela, fila** em **Em destaque**.
3. Preencha o formulário de conta de armazenamento com as informações a seguir, conforme mostrado na imagem a seguir e selecione **Criar**:

   | Configuração       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome** | mystorageaccount | Um valor exclusivo para sua conta de armazenamento |
   | **Modelo de implantação** | Gerenciador de Recursos  | O Resource Manager contém os recursos mais recentes.  |
   | **Tipo de conta** | Propósito geral | Para obter detalhes sobre os tipos de contas, consulte [tipos de contas de armazenamento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Desempenho** | Standard | O padrão é suficiente para o cenário de exemplo. |
   | **Replicação**| Armazenamento com redundância geográfica com acesso de leitura (RA-GRS) | Isso é necessário para que o exemplo funcione. |
   |**Transferência segura é necessária** | Desabilitado| Transferência segura não é necessária para este cenário. |
   |**Assinatura** | sua assinatura |Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Localidade** | Leste dos EUA | Escolha um local. |

![criar conta de armazenamento](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>Baixar o exemplo

[Baixar o projeto de exemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip).

Extrair (descompacte) o arquivo storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip.
O projeto de exemplo contém um aplicativo de console.

## <a name="set-the-connection-string"></a>Definir a cadeia de conexão

Abra o aplicativo de console *storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs* no Visual Studio.

Sob o nó **appSettings** no arquivo **App.config**, substitua o valor da _StorageConnectionString_ pela cadeia de conexão da conta de armazenamento. Esse valor é recuperado selecionando **Chaves de acesso** em **Configurações** em sua conta de armazenamento no portal do Azure. Copiar o **cadeia de conexão** da chave primária ou secundária e cole-a no arquivo **App.config**. Selecione **Salvar** para salvar o arquivo ao concluir.

![arquivo de configuração de aplicativo](media/storage-create-geo-redundant-storage/figure2.png)

## <a name="run-the-console-application"></a>Executar o aplicativo de console

No Visual Studio, pressione **F5** ou selecione **Iniciar** para iniciar a depuração do aplicativo. O Visual Studio automaticamente restaura pacotes Nuget ausentes, se configurado; acesse [Instalando e reinstalando pacotes com a restauração de pacote](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) para saber mais. 

Uma janela de console é inicializada e aplicativo começa a ser executado. O aplicativo carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. O aplicativo verifica para garantir que a imagem foi replicada para o ponto de extremidade RA-GRS secundário. Ele então começa a baixar a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. **P** representa o ponto de extremidade primário e **S** representa o ponto de extremidade secundário.

![Aplicativo de console em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no arquivo `Program.cs` é usada para baixar uma imagem da conta de armazenamento usando o método [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet). Antes da baixar, um [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) é definido. O contexto de operação define manipuladores de eventos que são acionados quando um download é concluído com êxito ou se um download falha e tenta novamente.

### <a name="retry-event-handler"></a>Repetir o manipulador de eventos

O manipulador de eventos `Operation_context_Retrying` é chamado quando o download da imagem de falha e é definido para repetição. Se o número máximo de repetições definido no aplicativo for atingido, o [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) da solicitação será alterado para `SecondaryOnly`. Essa configuração força o aplicativo tentar baixar a imagem do ponto de extremidade secundário. Essa configuração reduz o tempo necessário para solicitar a imagem, uma vez que ponto de extremidade primário não é repetido indefinidamente.

```csharp
private static void Operation_context_Retrying(object sender, RequestEventArgs e)
{
    retryCount++;
    Console.WriteLine("Retrying event because of failure reading the primary. RetryCount = " + retryCount);

    // Check if we have had more than n retries in which case switch to secondary.
    if (retryCount >= retryThreshold)
    {

        // Check to see if we can fail over to secondary.
        if (blobClient.DefaultRequestOptions.LocationMode != LocationMode.SecondaryOnly)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.SecondaryOnly;
            retryCount = 0;
        }
        else
        {
            throw new ApplicationException("Both primary and secondary are unreachable. Check your application's network connection. ");
        }
    }
}
```

### <a name="request-completed-event-handler"></a>Manipulador de eventos de solicitação concluída

O manipulador de eventos `Operation_context_RequestCompleted` é chamado quando o download da imagem é bem-sucedido. Se o aplicativo estiver usando o ponto de extremidade secundário, o aplicativo continuará a usar esse ponto de extremidade até 20 vezes. Depois de 20 vezes, o aplicativo definirá o [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) de volta para `PrimaryThenSecondary` e repetirá o ponto de extremidade primário. Se uma solicitação for bem-sucedida, o aplicativo continuará a leitura do ponto de extremidade primário.

```csharp
private static void Operation_context_RequestCompleted(object sender, RequestEventArgs e)
{
    if (blobClient.DefaultRequestOptions.LocationMode == LocationMode.SecondaryOnly)
    {
        // You're reading the secondary. Let it read the secondary [secondaryThreshold] times, 
        //    then switch back to the primary and see if it's available now.
        secondaryReadCount++;
        if (secondaryReadCount >= secondaryThreshold)
        {
            blobClient.DefaultRequestOptions.LocationMode = LocationMode.PrimaryThenSecondary;
            secondaryReadCount = 0;
        }
    }
}
```

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu sobre como tornar um aplicativo altamente disponível com contas de armazenamento RA-GRS, como:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Baixar o exemplo
> * Definir a cadeia de conexão
> * Executar o aplicativo de console

Avance para a parte dois da série para saber como simular uma falha e forçar o aplicativo a usar o ponto de extremidade RA-GRS secundário.

> [!div class="nextstepaction"]
> [Simule uma falha na conexão ao ponto de extremidade de armazenamento primário](storage-simulate-failure-ragrs-account-app.md)
