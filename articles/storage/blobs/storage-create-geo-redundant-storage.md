---
title: "Tornar os dados de aplicativos altamente disponíveis no Azure | Microsoft Docs"
description: "Use o armazenamento com redundância geográfica com acesso de leitura para tornar os dados do aplicativo altamente disponíveis"
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.workload: web
ms.topic: tutorial
ms.date: 02/20/2018
ms.author: tamram
ms.custom: mvc
ms.openlocfilehash: 7b7625b3250d7e5b4cdb4090f34072eb58dda07c
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/22/2018
---
# <a name="make-your-application-data-highly-available-with-azure-storage"></a>Torne os dados do aplicativo altamente disponíveis com o armazenamento do Azure

Este tutorial é a parte um de uma série, a qual mostra como tornar os dados do aplicativo altamente disponíveis no Azure. Quando você terminar, terá um aplicativo de console que carrega e recupera um blob para uma conta de armazenamento RA-GRS ([redundância de área geográfica com acesso de leitura](../common/storage-redundancy.md#read-access-geo-redundant-storage)). RA-GRS funciona por meio da replicação de transações da região primária para a secundária. Esse processo de replicação garante que os dados na região secundária acabem sendo consistentes. O aplicativo usa o padrão de [Disjuntor](/azure/architecture/patterns/circuit-breaker) para determinar a qual ponto de extremidade se conectar. O aplicativo muda para um ponto de extremidade secundário quando uma falha é simulada.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Baixar o exemplo
> * Definir a cadeia de conexão
> * Executar o aplicativo de console

## <a name="prerequisites"></a>pré-requisitos

Para concluir este tutorial:
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
* Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
  - **Desenvolvimento do Azure**

  ![Desenvolvimento do Azure (em Web e Nuvem)](media/storage-create-geo-redundant-storage/workloads.png)

* (Opcional) Baixe e instale o [Fiddler](https://www.telerik.com/download/fiddler)
 
# <a name="python-tabpython"></a>[Python] (#tab/python) 

* Instalar o [Python](https://www.python.org/downloads/)
* Baixe e instale o [SDK do Armazenamento do Azure para Python](storage-python-how-to-use-blob-storage.md#download-and-install-azure-storage-sdk-for-python)
* (Opcional) Baixe e instale o [Fiddler](https://www.telerik.com/download/fiddler)

---

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="log-in-to-the-azure-portal"></a>Faça logon no Portal do Azure

Faça logon no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure.

Siga estas etapas para criar uma conta de armazenamento com redundância geográfica com acesso de leitura:

1. Selecione o botão **Criar um recurso**, localizado no canto superior esquerdo do portal do Azure.

2. Selecione **Armazenamento** na página **Novo** e selecione **Conta de armazenamento – blob, arquivo, tabela, fila** em **Em destaque**.
3. Preencha o formulário de conta de armazenamento com as informações a seguir, conforme mostrado na imagem a seguir e selecione **Criar**:

   | Configuração       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome** | mystorageaccount | Um valor exclusivo para sua conta de armazenamento |
   | **Modelo de implantação** | Gerenciador de Recursos  | O Resource Manager contém os recursos mais recentes.|
   | **Tipo de conta** | Propósito geral | Para obter detalhes sobre os tipos de contas, consulte [tipos de contas de armazenamento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Desempenho** | Standard | O padrão é suficiente para o cenário de exemplo. |
   | **Replicação**| Armazenamento com redundância geográfica com acesso de leitura (RA-GRS) | Isso é necessário para que o exemplo funcione. |
   |**Transferência segura é necessária** | Desabilitado| Transferência segura não é necessária para este cenário. |
   |**Assinatura** | sua assinatura |Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Localidade** | Leste dos EUA | Escolha um local. |

![criar conta de armazenamento](media/storage-create-geo-redundant-storage/figure1.png)

## <a name="download-the-sample"></a>Baixar o exemplo

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (descompacte) o arquivo storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo de console.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git 
```
# <a name="python-tabpython"></a>[Python] (#tab/python) 

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (descompacte) o arquivo storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip file. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo Python básico.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```
---


## <a name="set-the-connection-string"></a>Definir a cadeia de conexão

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. É recomendável armazenar essa cadeia de caracteres de conexão dentro de uma variável de ambiente no computador local que está executando o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar a variável de ambiente.

No portal do Azure, navegue até sua conta de armazenamento. Selecione **Chaves de acesso** em **Configurações** na sua conta de armazenamento. Copie a **cadeia de conexão** da chave primária ou secundária. Substitua \<yourconnectionstring\> com sua cadeia de conexão atual executando um dos comandos a seguir com base no seu sistema operacional. Este comando salva uma variável de ambiente no computador local. No Windows, a variável de ambiente não está disponível até que recarregue o **Prompt de comando** ou o shell que você estiver usando. Substitua **\<storageConnectionString\>** no exemplo a seguir:

# <a name="linux-tablinux"></a>[Linux] (#tab/linux) 
export storageconnectionstring=\<yourconnectionstring\> 

# <a name="windows-tabwindows"></a>[Windows] (#tab/windows) 
setx storageconnectionstring "\<yourconnectionstring\>"

---


## <a name="run-the-console-application"></a>Executar o aplicativo de console

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)
No Visual Studio, pressione **F5** ou selecione **Iniciar** para iniciar a depuração do aplicativo. O Visual Studio automaticamente restaura pacotes NuGet ausentes, se configurado; acesse [Instalando e reinstalando pacotes com a restauração de pacote](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) para saber mais.

Uma janela de console é inicializada e aplicativo começa a ser executado. O aplicativo carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. O aplicativo verifica para garantir que a imagem foi replicada para o ponto de extremidade RA-GRS secundário. Ele então começa a baixar a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. **P** representa o ponto de extremidade primário e **S** representa o ponto de extremidade secundário.

![Aplicativo de console em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no arquivo `Program.cs` é usada para baixar uma imagem da conta de armazenamento usando o método [DownloadToFileAsync](/dotnet/api/microsoft.windowsazure.storage.blob.cloudblockblob.downloadtofileasync?view=azure-dotnet). Antes da baixar, um [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) é definido. O contexto de operação define manipuladores de eventos que são acionados quando um download é concluído com êxito ou se um download falha e tenta novamente.

# <a name="python-tabpython"></a>[Python] (#tab/python) 
Para executar o aplicativo em um terminal ou prompt de comando, vá para o diretório **circuitbreaker.py**, e insira `python circuitbreaker.py`. O aplicativo carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. O aplicativo verifica para garantir que a imagem foi replicada para o ponto de extremidade RA-GRS secundário. Ele então começa a baixar a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. **P** representa o ponto de extremidade primário e **S** representa o ponto de extremidade secundário.

![Aplicativo de console em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, o método `run_circuit_breaker` no arquivo `circuitbreaker.py` é usado para baixar uma imagem da conta de armazenamento usando o método [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html). 

A função de repetição do Objeto de armazenamento é definida como uma política de repetição linear. A função de repetição determina se uma solicitação será repetida, além de especificar o número de segundos a aguardar antes de repeti-la. Defina o valor **retry\_to\_secondary** como true se a solicitação deve ser repetida para o secundário no caso de a solicitação inicial para o primário falhar. No aplicativo de exemplo, uma política de repetição personalizada é definida na função `retry_callback` do objeto de armazenamento.
 
Antes de baixar, o Objeto de serviço [retry_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) e a função [response_callback](https://docs.microsoft.com/en-us/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) é definida. Essas funções definem manipuladores de eventos disparados quando um download é concluído com êxito ou caso um download falhe e seja tentado novamente.  

---

### <a name="retry-event-handler"></a>Repetir o manipulador de eventos

# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

O manipulador de eventos `OperationContextRetrying` é chamado quando o download da imagem falha e é definido para repetição. Se o número máximo de repetições definido no aplicativo for atingido o [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) da solicitação será alterado para `SecondaryOnly`. Essa configuração força o aplicativo tentar baixar a imagem do ponto de extremidade secundário. Essa configuração reduz o tempo necessário para solicitar a imagem, uma vez que ponto de extremidade primário não é repetido indefinidamente.

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no arquivo `Program.cs` é usada para baixar uma imagem da conta de armazenamento usando o método [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet). Antes da baixar, um [OperationContext](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) é definido. O contexto de operação define manipuladores de eventos que são acionados quando um download é concluído com êxito ou se um download falha e tenta novamente.
 
```csharp
private static void OperationContextRetrying(object sender, RequestEventArgs e)
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 
O manipulador de eventos `retry_callback` é chamado quando o download da imagem falha e é definido para repetição. Se o número máximo de repetições definido no aplicativo for atingido o [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) da solicitação será alterado para `SECONDARY`. Essa configuração força o aplicativo tentar baixar a imagem do ponto de extremidade secundário. Essa configuração reduz o tempo necessário para solicitar a imagem, uma vez que ponto de extremidade primário não é repetido indefinidamente.  

```python
def retry_callback(retry_context):
    global retry_count
    retry_count = retry_context.count
    sys.stdout.write("\nRetrying event because of failure reading the primary. RetryCount= {0}".format(retry_count))
    sys.stdout.flush()

    # Check if we have more than n-retries in which case switch to secondary
    if retry_count >= retry_threshold:

        # Check to see if we can fail over to secondary.
        if blob_client.location_mode != LocationMode.SECONDARY:
            blob_client.location_mode = LocationMode.SECONDARY
            retry_count = 0
        else:
            raise Exception("Both primary and secondary are unreachable. "
                            "Check your application's network connection.")
```

---


### <a name="request-completed-event-handler"></a>Manipulador de eventos de solicitação concluída
 
# <a name="net-tabdotnet"></a>[.NET] (#tab/dotnet)

O manipulador de eventos `OperationContextRequestCompleted` é chamado quando o download da imagem é bem-sucedido. Se o aplicativo estiver usando o ponto de extremidade secundário, o aplicativo continuará a usar esse ponto de extremidade até 20 vezes. Depois de 20 vezes, o aplicativo definirá o [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) de volta para `PrimaryThenSecondary` e repetirá o ponto de extremidade primário. Se uma solicitação for bem-sucedida, o aplicativo continuará a leitura do ponto de extremidade primário.
 
```csharp
private static void OperationContextRequestCompleted(object sender, RequestEventArgs e)
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

# <a name="python-tabpython"></a>[Python] (#tab/python) 

O manipulador de eventos `response_callback` é chamado quando o download da imagem é bem-sucedido. Se o aplicativo estiver usando o ponto de extremidade secundário, o aplicativo continuará a usar esse ponto de extremidade até 20 vezes. Depois de 20 vezes, o aplicativo definirá o [LocationMode](https://docs.microsoft.com/en-us/python/api/azure.storage.common.models.locationmode?view=azure-python) de volta para `PRIMARY` e repetirá o ponto de extremidade primário. Se uma solicitação for bem-sucedida, o aplicativo continuará a leitura do ponto de extremidade primário.

```python
def response_callback(response):
    global secondary_read_count
    if blob_client.location_mode == LocationMode.SECONDARY:

        # You're reading the secondary. Let it read the secondary [secondaryThreshold] times,
        # then switch back to the primary and see if it is available now.
        secondary_read_count += 1
        if secondary_read_count >= secondary_threshold:
            blob_client.location_mode = LocationMode.PRIMARY
            secondary_read_count = 0
```

---

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
