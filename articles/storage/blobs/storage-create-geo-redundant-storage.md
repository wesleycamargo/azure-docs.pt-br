---
title: 'Tutorial: Criar um aplicativo altamente disponível com o armazenamento de Blobs – Armazenamento do Azure'
description: Use o armazenamento com redundância geográfica com acesso de leitura para tornar os dados do aplicativo altamente disponíveis
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 01/03/2019
ms.author: tamram
ms.custom: mvc
ms.subservice: blobs
ms.openlocfilehash: c4e81d9be09855cde986bfd21f8f688fa7d1341e
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "58793695"
---
# <a name="tutorial-build-a-highly-available-application-with-blob-storage"></a>Tutorial: Criar um aplicativo altamente disponível com o armazenamento de Blobs

Este tutorial é a primeira parte de uma série. Nele, você aprenderá a tornar os dados de seu aplicativo altamente disponíveis no Azure.

Quando concluir este tutorial, você terá um aplicativo de console que carrega e recupera um blob de uma conta de armazenamento [RA-GRS](../common/storage-redundancy-grs.md#read-access-geo-redundant-storage) (com redundância geográfica com acesso de leitura).

O RA-GRS funciona pela replicação de transações de uma região primária para uma região secundária. Esse processo de replicação garante que os dados na região secundária acabem sendo consistentes. O aplicativo usa o padrão de [Disjuntor](/azure/architecture/patterns/circuit-breaker) para determinar a qual ponto de extremidade ele se conectará, alternando automaticamente entre pontos de extremidade conforme as falhas e as recuperações são simuladas.

Se você não tiver uma assinatura do Azure, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

Na primeira parte da série, você aprenderá a:

> [!div class="checklist"]
> * Criar uma conta de armazenamento
> * Definir a cadeia de conexão
> * Executar o aplicativo de console

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

* Instale o [Visual Studio 2017](https://www.visualstudio.com/downloads/) com as cargas de trabalho a seguir:
  - **Desenvolvimento do Azure**

  ![Desenvolvimento do Azure (em Web e Nuvem)](media/storage-create-geo-redundant-storage/workloads.png)

# <a name="pythontabpython"></a>[Python](#tab/python)

* Instalar o [Python](https://www.python.org/downloads/)
* Baixe e instale o [SDK do Armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)

# <a name="java-v7-sdktabjava-v7"></a>[SDK do Java V7](#tab/java-v7)

* Instale e configure o [Maven](https://maven.apache.org/download.cgi) para trabalhar a partir da linha de comando
* Instalar e configurar um [JDK](https://www.oracle.com/technetwork/java/javase/downloads/index.html)

# <a name="java-v10-sdktabjava-v10"></a>[SDK V10 Java](#tab/java-v10)

* Instale e configure o [Maven](http://maven.apache.org/download.cgi) para trabalhar a partir da linha de comando
* Instalar e configurar um [JDK](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

---

## <a name="sign-in-to-the-azure-portal"></a>Entre no Portal do Azure

Entre no [Portal do Azure](https://portal.azure.com/).

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo para armazenar e acessar os objetos de dados do Armazenamento do Azure.

Siga estas etapas para criar uma conta de armazenamento com redundância geográfica com acesso de leitura:

1. Selecione o botão **Criar um recurso**, localizado no canto superior esquerdo do portal do Azure.
2. Selecione **Armazenamento** na página **Novo**.
3. Selecione **Conta de armazenamento – blob, arquivo, tabela, fila** em **Em destaque**.
4. Preencha o formulário de conta de armazenamento com as informações a seguir, conforme mostrado na imagem a seguir e selecione **Criar**:

   | Configuração       | Valor sugerido | Descrição |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Nome** | mystorageaccount | Um valor exclusivo para sua conta de armazenamento |
   | **Modelo de implantação** | Gerenciador de Recursos  | O Resource Manager contém os recursos mais recentes.|
   | **Tipo de conta** | StorageV2 | Para obter detalhes sobre os tipos de contas, consulte [tipos de contas de armazenamento](../common/storage-introduction.md#types-of-storage-accounts) |
   | **Desempenho** | Standard | O padrão é suficiente para o cenário de exemplo. |
   | **Replicação**| Armazenamento com redundância geográfica com acesso de leitura (RA-GRS) | Isso é necessário para que o exemplo funcione. |
   |**Assinatura** | sua assinatura |Para obter detalhes sobre suas assinaturas, consulte [Assinaturas](https://account.windowsazure.com/Subscriptions). |
   |**ResourceGroup** | myResourceGroup |Para ver os nomes do grupo de recursos válidos, consulte [Regras e restrições de nomenclatura](https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions). |
   |**Localidade** | Leste dos EUA | Escolha um local. |

![criar conta de armazenamento](media/storage-create-geo-redundant-storage/createragrsstracct.png)

## <a name="download-the-sample"></a>Baixar o exemplo

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (descompacte) o arquivo storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.zip. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo de console.

```bash
git clone https://github.com/Azure-Samples/storage-dotnet-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="pythontabpython"></a>[Python](#tab/python)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs/archive/master.zip) e extraia (descompacte) o arquivo storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.zip file. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo Python básico.

```bash
git clone https://github.com/Azure-Samples/storage-python-circuit-breaker-pattern-ha-apps-using-ra-grs.git
```

# <a name="java-v7-sdktabjava-v7"></a>[SDK do Java V7](#tab/java-v7)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-java-ha-ra-grs) e extraia o arquivo storage-java-ragrs.zip. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo Java básico.

```bash
git clone https://github.com/Azure-Samples/storage-java-ha-ra-grs.git
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK V10 Java](#tab/java-v10)

[Baixe o projeto de exemplo](https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs) e extraia o arquivo storage-java-ragrs.zip. Também é possível usar o [git](https://git-scm.com/) para baixar uma cópia do aplicativo para seu ambiente de desenvolvimento. O projeto de exemplo contém um aplicativo Java básico.

```bash
git clone https://github.com/Azure-Samples/storage-java-V10-ha-ra-grs
```

---

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. É recomendável que você armazene essa cadeia de conexão em uma variável de ambiente no computador local que executa o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar a variável de ambiente.

No portal do Azure, navegue até sua conta de armazenamento. Selecione **Chaves de acesso** em **Configurações** na sua conta de armazenamento. Copie a **cadeia de conexão** da chave primária ou secundária. Substitua \<yourconnectionstring\> com sua cadeia de conexão atual executando um dos comandos a seguir com base no seu sistema operacional. Este comando salva uma variável de ambiente no computador local. No Windows, a variável de ambiente não fica disponível enquanto você não recarrega o **Prompt de Comando** ou o shell que está sendo usado. Substitua **\<storageConnectionString\>** no exemplo a seguir:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a> Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="pythontabpython"></a>[Python](#tab/python)

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. É recomendável que você armazene essa cadeia de conexão em uma variável de ambiente no computador local que executa o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar a variável de ambiente.

No portal do Azure, navegue até sua conta de armazenamento. Selecione **Chaves de acesso** em **Configurações** na sua conta de armazenamento. Copie a **cadeia de conexão** da chave primária ou secundária. Substitua \<yourconnectionstring\> com sua cadeia de conexão atual executando um dos comandos a seguir com base no seu sistema operacional. Este comando salva uma variável de ambiente no computador local. No Windows, a variável de ambiente não fica disponível enquanto você não recarrega o **Prompt de Comando** ou o shell que está sendo usado. Substitua **\<storageConnectionString\>** no exemplo a seguir:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a> Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v7-sdktabjava-v7"></a>[SDK do Java V7](#tab/java-v7)

No aplicativo, você deve fornecer a cadeia de conexão para sua conta de armazenamento. É recomendável que você armazene essa cadeia de conexão em uma variável de ambiente no computador local que executa o aplicativo. Siga um dos exemplos abaixo, dependendo do seu sistema operacional para criar a variável de ambiente.

No portal do Azure, navegue até sua conta de armazenamento. Selecione **Chaves de acesso** em **Configurações** na sua conta de armazenamento. Copie a **cadeia de conexão** da chave primária ou secundária. Substitua \<yourconnectionstring\> com sua cadeia de conexão atual executando um dos comandos a seguir com base no seu sistema operacional. Este comando salva uma variável de ambiente no computador local. No Windows, a variável de ambiente não fica disponível enquanto você não recarrega o **Prompt de Comando** ou o shell que está sendo usado. Substitua **\<storageConnectionString\>** no exemplo a seguir:

### <a name="linux"></a>Linux

```
export storageconnectionstring=\<yourconnectionstring\>
```

### <a name="windows"></a> Windows

```PowerShell
setx storageconnectionstring "\<yourconnectionstring\>"
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK V10 Java](#tab/java-v10)

Esta amostra exige que você armazene o nome e a chave da conta de armazenamento com segurança. Armazene-as em variáveis de ambiente locais no computador que executará a amostra. Use o exemplo do Linux ou do Windows, dependendo do sistema operacional, para criar as variáveis de ambiente. No Windows, a variável de ambiente não fica disponível enquanto você não recarrega o **Prompt de Comando** ou o shell que está sendo usado.

### <a name="linux-example"></a>Exemplo do Linux

```
export AZURE_STORAGE_ACCOUNT="<youraccountname>"
export AZURE_STORAGE_ACCESS_KEY="<youraccountkey>"
```

### <a name="windows-example"></a>Exemplo do Windows

```
setx AZURE_STORAGE_ACCOUNT "<youraccountname>"
setx AZURE_STORAGE_ACCESS_KEY "<youraccountkey>"
```

---

## <a name="run-the-console-application"></a>Executar o aplicativo de console

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

No Visual Studio, pressione **F5** ou selecione **Iniciar** para iniciar a depuração do aplicativo. O Visual Studio automaticamente restaura os pacotes NuGet ausentes, se configurado. Acesse [Instalando e reinstalando pacotes com a restauração de pacote](https://docs.microsoft.com/nuget/consume-packages/package-restore#package-restore-overview) para saber mais.

Uma janela de console é inicializada e aplicativo começa a ser executado. O aplicativo carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. O aplicativo verifica para garantir que a imagem foi replicada para o ponto de extremidade RA-GRS secundário. Ele então começa a baixar a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. **P** representa o ponto de extremidade primário e **S** representa o ponto de extremidade secundário.

![Aplicativo de console em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, a tarefa `RunCircuitBreakerAsync` no arquivo `Program.cs` é usada para baixar uma imagem da conta de armazenamento usando o método [DownloadToFileAsync](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.blob.cloudblob.downloadtofileasync?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_CloudBlob_DownloadToFileAsync_System_String_System_IO_FileMode_Microsoft_WindowsAzure_Storage_AccessCondition_Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_Microsoft_WindowsAzure_Storage_OperationContext_). Antes da baixar, um [OperationContext](/dotnet/api/microsoft.windowsazure.storage.operationcontext?view=azure-dotnet) é definido. O contexto de operação define manipuladores de eventos que são acionados quando um download é concluído com êxito ou se um download falha e tenta novamente.

# <a name="pythontabpython"></a>[Python](#tab/python)

Para executar o aplicativo em um terminal ou prompt de comando, vá para o diretório **circuitbreaker.py**, e insira `python circuitbreaker.py`. O aplicativo carrega a imagem **HelloWorld.png** da solução para a conta de armazenamento. O aplicativo verifica para garantir que a imagem foi replicada para o ponto de extremidade RA-GRS secundário. Ele então começa a baixar a imagem até 999 vezes. Cada leitura é representada por um **P** ou um **S**. **P** representa o ponto de extremidade primário e **S** representa o ponto de extremidade secundário.

![Aplicativo de console em execução](media/storage-create-geo-redundant-storage/figure3.png)

No código de exemplo, o método `run_circuit_breaker` no arquivo `circuitbreaker.py` é usado para baixar uma imagem da conta de armazenamento usando o método [get_blob_to_path](https://azure.github.io/azure-storage-python/ref/azure.storage.blob.baseblobservice.html).

A função de repetição do Objeto de armazenamento é definida como uma política de repetição linear. A função de repetição determina se uma solicitação será repetida, além de especificar o número de segundos a aguardar antes de repeti-la. Defina o valor **retry\_to\_secondary** como true se a solicitação deve ser repetida para o secundário no caso de a solicitação inicial para o primário falhar. No aplicativo de exemplo, uma política de repetição personalizada é definida na função `retry_callback` do objeto de armazenamento.

Antes de baixar, o Objeto de serviço [retry_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) e a função [response_callback](https://docs.microsoft.com/python/api/azure.storage.common.storageclient.storageclient?view=azure-python) é definida. Essas funções definem manipuladores de eventos disparados quando um download é concluído com êxito ou caso um download falhe e seja tentado novamente.

# <a name="java-v7-sdktabjava-v7"></a>[SDK do Java V7](#tab/java-v7)

Você pode executar o aplicativo abrindo um terminal ou um prompt de comando de escopo para a pasta do aplicativo baixado. A partir daí, insira `mvn compile exec:java` para executar o aplicativo. Em seguida, o aplicativo carrega a imagem **HelloWorld.png** do diretório para sua conta de armazenamento e faz verificações para garantir que a imagem tenha sido replicada para o ponto de extremidade de RA-GRS secundário. Assim que a verificação estiver concluída, o aplicativo começará a baixar a imagem repetidamente, relatando de qual ponto de extremidade a está baixando.

A função de repetição do objeto de armazenamento é configurada para usar uma política de repetição linear. A função de repetição determina se uma solicitação será repetida, além de especificar o número de segundos a aguardar antes de cada repetição. A propriedade **LocationMode** do seu **BlobRequestOptions** é definida como **PRIMARY\_THEN\_SECONDARY**. Isso permite que o aplicativo alterne automaticamente para o local secundário caso não consiga acessar o local primário ao tentar baixar a imagem **HelloWorld.png**.

# <a name="java-v10-sdktabjava-v10"></a>[SDK V10 Java](#tab/java-v10)

Para executar a amostra, use o Maven na linha de comando.

1. abra um shell e navegue para **storage-blobs-java-v10-quickstart** no diretório clonado.
2. Digite `mvn compile exec:java`.

Esta amostra cria um arquivo de teste no diretório padrão; para os usuários do Windows, esse diretório é **AppData\Local\Temp**. Em seguida, a amostra apresenta as seguintes opções de comandos que podem ser inseridos:

- Insira **P** para executar uma operação put de blob; isso carrega um arquivo temporário em sua conta de armazenamento.
- Insira **L** para executar uma operação list de blob; isso lista os blobs atualmente no contêiner.
- Insira **G** para executar uma operação get de blob; isso baixa um arquivo de sua conta de armazenamento para o computador local.
- Insira **D** para executar uma operação delete de blob; isso exclui o blob de sua conta de armazenamento.
- Insira **E** para fechar a amostra; isso também exclui todos os recursos criados pela amostra.

Este exemplo mostra a saída se você executa o aplicativo no Windows.

```
Created quickstart container
Enter a command
(P)utBlob | (L)istBlobs | (G)etBlob | (D)eleteBlobs | (E)xitSample
# Enter a command :
P
Uploading the sample file into the container: https://<storageaccount>.blob.core.windows.net/quickstart
# Enter a command :
L
Listing blobs in the container: https://<storageaccount>.blob.core.windows.net/quickstart
Blob name: SampleBlob.txt
# Enter a command :
G
Get the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
The blob was downloaded to C:\Users\<useraccount>\AppData\Local\Temp\downloadedFile13097087873115855761.txt
# Enter a command :
D
Delete the blob: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt

# Enter a command :
>> Blob deleted: https://<storageaccount>.blob.core.windows.net/quickstart/SampleBlob.txt
E
Cleaning up the sample and exiting!
```

Você controla o exemplo e, portanto, insira os comandos para executar o código. As entradas diferenciam maiúsculas de minúsculas.

---

## <a name="understand-the-sample-code"></a>Entender o código de exemplo

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

### <a name="retry-event-handler"></a>Repetir o manipulador de eventos

O manipulador de eventos `OperationContextRetrying` é chamado quando o download da imagem falha e é definido para repetição. Se o número máximo de repetições definido no aplicativo for atingido o [LocationMode](/dotnet/api/microsoft.windowsazure.storage.blob.blobrequestoptions.locationmode?view=azure-dotnet#Microsoft_WindowsAzure_Storage_Blob_BlobRequestOptions_LocationMode) da solicitação será alterado para `SecondaryOnly`. Essa configuração força o aplicativo tentar baixar a imagem do ponto de extremidade secundário. Essa configuração reduz o tempo necessário para solicitar a imagem, uma vez que ponto de extremidade primário não é repetido indefinidamente.

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

### <a name="request-completed-event-handler"></a>Manipulador de eventos de solicitação concluída

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

# <a name="pythontabpython"></a>[Python](#tab/python)

### <a name="retry-event-handler"></a>Repetir o manipulador de eventos

O manipulador de eventos `retry_callback` é chamado quando o download da imagem falha e é definido para repetição. Se o número máximo de repetições definido no aplicativo for atingido o [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) da solicitação será alterado para `SECONDARY`. Essa configuração força o aplicativo tentar baixar a imagem do ponto de extremidade secundário. Essa configuração reduz o tempo necessário para solicitar a imagem, uma vez que ponto de extremidade primário não é repetido indefinidamente.

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

### <a name="request-completed-event-handler"></a>Manipulador de eventos de solicitação concluída

O manipulador de eventos `response_callback` é chamado quando o download da imagem é bem-sucedido. Se o aplicativo estiver usando o ponto de extremidade secundário, o aplicativo continuará a usar esse ponto de extremidade até 20 vezes. Depois de 20 vezes, o aplicativo definirá o [LocationMode](https://docs.microsoft.com/python/api/azure.storage.common.models.locationmode?view=azure-python) de volta para `PRIMARY` e repetirá o ponto de extremidade primário. Se uma solicitação for bem-sucedida, o aplicativo continuará a leitura do ponto de extremidade primário.

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

# <a name="java-v7-sdktabjava-v7"></a>[SDK do Java V7](#tab/java-v7)

Com o Java, não é preciso fazer o retorno de chamada dos manipuladores se a propriedade **LocationMode** do seu **BlobRequestOptions** estiver definida como **PRIMARY\_THEN\_SECONDARY**. Isso permite que o aplicativo alterne automaticamente para o local secundário caso não consiga acessar o local primário ao tentar baixar a imagem **HelloWorld.png**.

```java
    BlobRequestOptions myReqOptions = new BlobRequestOptions();
    myReqOptions.setRetryPolicyFactory(new RetryLinearRetry(deltaBackOff, maxAttempts));
    myReqOptions.setLocationMode(LocationMode.PRIMARY_THEN_SECONDARY);
    blobClient.setDefaultRequestOptions(myReqOptions);

    blob.downloadToFile(downloadedFile.getAbsolutePath(),null,blobClient.getDefaultRequestOptions(),opContext);
```

# <a name="java-v10-sdktabjava-v10"></a>[SDK V10 Java](#tab/java-v10)

Com o SDK do Java V10, a definição de manipuladores de retorno de chamada ainda é desnecessária e o SDK agora tem algumas diferenças fundamentais em relação ao SDK V7. Em vez de LocationMode, temos um **Pipeline** secundário. Você poderá definir um pipeline secundário por meio das **RequestRetryOptions** e, se ele for definido, você permitirá que o aplicativo alterne automaticamente para o pipeline secundário caso ele não consiga acessar os dados por meio do pipeline primário.

```java
// We create pipeline options here so that they can be easily used between different pipelines
PipelineOptions myOptions = new PipelineOptions();
myOptions.withRequestRetryOptions(new RequestRetryOptions(RetryPolicyType.EXPONENTIAL, 3, 10, 500L, 1000L, accountName + "-secondary.blob.core.windows.net"));
// We are using a default pipeline here, you can learn more about it at https://github.com/Azure/azure-storage-java/wiki/Azure-Storage-Java-V10-Overview
final ServiceURL serviceURL = new ServiceURL(new URL("https://" + accountName + ".blob.core.windows.net"), StorageURL.createPipeline(creds, myOptions));
```

---

## <a name="next-steps"></a>Próximas etapas

Na primeira parte da série, você aprendeu a tornar um aplicativo altamente disponível com contas de armazenamento RA-GRS.

Avance para a parte dois da série para saber como simular uma falha e forçar o aplicativo a usar o ponto de extremidade RA-GRS secundário.

> [!div class="nextstepaction"]
> [Simule uma falha na conexão ao ponto de extremidade de armazenamento primário](storage-simulate-failure-ragrs-account-app.md)
