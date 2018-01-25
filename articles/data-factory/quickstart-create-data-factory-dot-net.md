---
title: Criar um Azure Data Factory usando o .NET | Microsoft Docs
description: Crie um Azure Data Factory para copiar dados de um local em um Armazenamento de Blobs do Azure para outro local no mesmo Armazenamento de Blobs.
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 01/22/2018
ms.author: jingwang
ms.openlocfilehash: b430f5932bcd54d5e2ab787fb6c4491b48dbc416
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/23/2018
---
# <a name="create-a-data-factory-and-pipeline-using-net-sdk"></a>Criar um data factory e um pipeline usando o SDK do .NET
> [!div class="op_single_selector" title1="Select the version of Data Factory service you are using:"]
> * [Versão 1 – já disponível](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Versão 2 – Versão prévia](quickstart-create-data-factory-dot-net.md)

Este guia de início rápido descreve como usar o SDK do .NET para criar um Azure Data Factory. O pipeline que você criar nesse data factory **copia** dados de uma pasta para outra em um Armazenamento de Blobs do Azure. Para obter um tutorial sobre como **transformar** dados usando o Azure Data Factory, consulte [Tutorial: transformar dados usando Spark](transform-data-using-spark.md). 

> [!NOTE]
> Este artigo aplica-se à versão 2 do Data Factory, que está atualmente em versão prévia. Se você estiver usando a versão 1 do serviço Data Factory, que já está disponível (GA), confira [introdução ao Data Factory versão 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md).
>
> Este artigo não fornece uma introdução detalhada do serviço Data Factory. Para obter uma introdução do serviço do Azure Data Factory, consulte [Introdução ao Azure Data Factory](introduction.md).

Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>pré-requisitos

### <a name="azure-subscription"></a>Assinatura do Azure
Se você não tiver uma assinatura do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

### <a name="azure-roles"></a>Funções do Azure
Para criar instâncias de Data Factory, a conta de usuário usada para fazer logon no Azure deve ser um membro das funções **colaborador** ou **proprietário**, ou um **administrador** da assinatura do Azure. No Portal do Azure, clique no seu **nome de usuário** no canto superior direito e selecione **Permissões** para exibir as permissões que você tem com a assinatura. Se tiver acesso a várias assinaturas, selecione a que for adequada. Para obter instruções de exemplo sobre como adicionar um usuário a uma função, consulte o artigo [Adicionar funções](../billing/billing-add-change-azure-subscription-administrator.md).

### <a name="azure-storage-account"></a>Conta de Armazenamento do Azure
Use uma Conta de Armazenamento do Azure para fins gerais (especificamente o Armazenamento de Blobs) como armazenamento de dados de **fonte** e **destino** neste guia de início rápido. Se você não tiver uma conta de fins gerais de armazenamento do Azure, consulte [Criar uma conta de armazenamento](../storage/common/storage-create-storage-account.md#create-a-storage-account) para saber sobre como criar uma. 

#### <a name="get-storage-account-name-and-account-key"></a>Obter o nome da conta de armazenamento e a chave da conta
Você usa o nome e a chave da sua conta de armazenamento do Azure neste início rápido. O procedimento a seguir fornece as etapas para obter o nome e a chave da sua conta de armazenamento. 

1. Abra um navegador da Web e navegue até o [portal do Azure](https://portal.azure.com). Faça logon usando seu nome de usuário e senha do Azure. 
2. Clique em **Mais serviços >** no menu à esquerda, filtre pela palavra-chave **Armazenamento** e selecione **Contas de armazenamento**.

    ![Pesquisar conta de armazenamento](media/quickstart-create-data-factory-dot-net/search-storage-account.png)
3. Na lista de contas de armazenamento, filtre pela sua conta de armazenamento (se necessário) e, em seguida, selecione a **sua conta de armazenamento**. 
4. Na página da **Conta de armazenamento**, selecione **Chaves de acesso** no menu.

    ![Obter nome e chave da conta de armazenamento](media/quickstart-create-data-factory-dot-net/storage-account-name-key.png)
5. Copie os valores dos campos **Nome da conta de armazenamento** e **key1** para a área de transferência. Cole-os em um bloco de notas ou qualquer outro editor e salve-os.  

#### <a name="create-input-folder-and-files"></a>Criar arquivos e pasta de entrada
Nesta seção, você cria um contêiner de blob chamado **adftutorial** no armazenamento de blobs do Azure. Em seguida, você cria uma pasta chamada **entrada** no contêiner e, em seguida, carrega um arquivo de exemplo na pasta de entrada. 

1. Na página **Conta de armazenamento**, alterne para a **Visão geral** e depois clique em **Blobs**. 

    ![Selecione a opção Blobs](media/quickstart-create-data-factory-dot-net/select-blobs.png)
2. Na página **Serviço Blob**, clique em **+ Contêiner** na barra de ferramentas. 

    ![Botão Adicionar contêiner](media/quickstart-create-data-factory-dot-net/add-container-button.png)    
3. Na caixa de diálogo **Novo contêiner**, insira **adftutorial** como o nome e clique em **OK**. 

    ![Inserir o nome do contêiner](media/quickstart-create-data-factory-dot-net/new-container-dialog.png)
4. Clique em **adftutorial** na lista de contêineres. 

    ![Selecione o contêiner](media/quickstart-create-data-factory-dot-net/select-adftutorial-container.png)
1. Na página **Contêiner**, clique em **Carregar** na barra de ferramentas.  

    ![Botão Carregar](media/quickstart-create-data-factory-dot-net/upload-toolbar-button.png)
6. Na página **Carregar blob**, clique em **Avançado**.

    ![Clique no link Avançado](media/quickstart-create-data-factory-dot-net/upload-blob-advanced.png)
7. Inicialize o **Bloco de notas** e crie um arquivo chamado **emp.txt** com o seguinte conteúdo: salve-o na pasta **c:\ADFv2QuickStartPSH**. Crie a pasta **ADFv2QuickStartPSH** caso ela ainda não exista.
    
    ```
    John, Doe
    Jane, Doe
    ```    
8. No Portal Azure, na página **Carregar blob**, procure e selecione o arquivo **emp.txt** para o arquivo **Campos**. 
9. Insira **entrada** como um valor do campo **Carregar para a pasta**. 

    ![Carregar configurações de blob](media/quickstart-create-data-factory-dot-net/upload-blob-settings.png)    
10. Confirme que a pasta é **entrada** e o arquivo é **emp.txt** e clique em **Carregar**.
11. O arquivo **emp.txt** e o status do carregamento devem estar na lista. 
12. Feche a página **Carregar blob** clicando no **X** no canto superior. 

    ![Fechar a página Carregar blob](media/quickstart-create-data-factory-dot-net/close-upload-blob.png)
1. Mantenha a página **Contêiner** aberta. Você a usa para verificar a saída no final do guia de início rápido.

### <a name="visual-studio"></a>Visual Studio
Este artigo passo a passo usa o Visual Studio 2017. Você também pode usar o Visual Studio 2013 ou 2015.

### <a name="azure-net-sdk"></a>SDK do Azure .NET
Baixe e instale o [SDK .NET do Azure](http://azure.microsoft.com/downloads/) no seu computador.

### <a name="create-an-application-in-azure-active-directory"></a>Criar um aplicativo no Azure Active Directory
Siga as instruções em [neste artigo](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application) para executar as seguintes tarefas: 

1. **Criar um aplicativo do Azure Active Directory**. Criar um aplicativo no Azure Active Directory que represente o aplicativo .NET que você está criando neste tutorial. Para a URL de logon, você pode fornecer uma URL fictícia, como mostrado no artigo (`https://contoso.org/exampleapp`).
2. Obter a **ID do aplicativo** e a **chave de autenticação**** usando as instruções da seção **Obter a ID do aplicativo e a chave de autenticação** no artigo. Anote esses valores pois serão usados posteriormente neste tutorial. 
3. Obter a **ID do locatário** usando as instruções da seção **Obter a ID de locatário** no artigo. Anote esse valor. 
4. Atribuir o aplicativo à função **Colaborador** no nível da assinatura para que o aplicativo possa criar os data factories na assinatura. Siga as instruções da seção **Atribuir aplicativos a função** no artigo. 

## <a name="create-a-visual-studio-project"></a>Criar um projeto do Visual Studio

Usando o Visual Studio 2013/2015/2017, crie um aplicativo de console C# .NET.

1. Inicie o **Visual Studio**.
2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
3. Selecione **Visual C#** -> **Aplicativo de Console (.NET Framework)** na lista de tipos de projeto à direita. O .NET versão 4.5.2 ou superior é necessário.
4. Digite **ADFv2QuickStart** para o Nome.
5. Clique em **OK** para criar o projeto.

## <a name="install-nuget-packages"></a>Instalar os pacotes NuGet

1. Clique em **Ferramentas** -> **Gerenciador de Pacotes do NuGet** -> **Console do Gerenciador de Pacotes**.
2. No **Console do Gerenciador de Pacotes**, execute os comandos a seguir para instalar os pacotes:

    ```
    Install-Package Microsoft.Azure.Management.DataFactory -Prerelease
    Install-Package Microsoft.Azure.Management.ResourceManager -Prerelease
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

    ```

## <a name="create-a-data-factory-client"></a>Criar um cliente data factory

1. Abra **Program.cs** e inclua as instruções a seguir para adicionar referências aos namespaces.

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using Microsoft.Rest;
    using Microsoft.Azure.Management.ResourceManager;
    using Microsoft.Azure.Management.DataFactory;
    using Microsoft.Azure.Management.DataFactory.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

2. Adicione o código a seguir, que define as variáveis, ao método **Main**. Substitua os espaços reservados por seus próprios valores. Atualmente, o Data Factory V2 permite que você crie os data factories somente nas regiões Leste dos EUA, Leste dos EUA 2 e Europa Ocidental. Os armazenamentos de dados (Armazenamento do Azure, Banco de Dados SQL do Azure, etc.) e serviços de computação (HDInsight, etc.) usados pelo data factory podem estar em outras regiões.

    ```csharp
    // Set variables
    string tenantID = "<your tenant ID>";
    string applicationId = "<your application ID>";
    string authenticationKey = "<your authentication key for the application>";
    string subscriptionId = "<your subscription ID where the data factory resides>";
    string resourceGroup = "<your resource group where the data factory resides>";
    string region = "East US 2";
    string dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>";
    string storageAccount = "<your storage account name to copy data>";
    string storageKey = "<your storage account key>";
    // specify the container and input folder from which all files need to be copied to the output folder. 
    string inputBlobPath = "<the path to existing blob(s) to copy data from, e.g. containername/foldername>";
    //specify the contains and output folder where the files are copied
    string outputBlobPath = "<the blob path to copy data to, e.g. containername/foldername>";

    string storageLinkedServiceName = "AzureStorageLinkedService";  // name of the Azure Storage linked service
    string blobDatasetName = "BlobDataset";             // name of the blob dataset
    string pipelineName = "Adfv2QuickStartPipeline";    // name of the pipeline
    ```

3. Adicione o código a seguir, que cria uma instância da classe **DataFactoryManagementClient**, ao método **Main**. Você usa esse objeto para criar um data factory, um serviço vinculado, conjuntos de dados e um pipeline. Você também pode usar esse objeto para monitorar os detalhes da execução de pipeline.

    ```csharp
    // Authenticate and create a data factory management client
    var context = new AuthenticationContext("https://login.windows.net/" + tenantID);
    ClientCredential cc = new ClientCredential(applicationId, authenticationKey);
    AuthenticationResult result = context.AcquireTokenAsync("https://management.azure.com/", cc).Result;
    ServiceClientCredentials cred = new TokenCredentials(result.AccessToken);
    var client = new DataFactoryManagementClient(cred) { SubscriptionId = subscriptionId };
    ```

## <a name="create-a-data-factory"></a>Criar uma data factory

Adicione o código a seguir, que cria um **data factory**, ao método **Main**. 

```csharp
// Create a data factory
Console.WriteLine("Creating data factory " + dataFactoryName + "...");
Factory dataFactory = new Factory
{
    Location = region,
    Identity = new FactoryIdentity()
};
client.Factories.CreateOrUpdate(resourceGroup, dataFactoryName, dataFactory);
Console.WriteLine(SafeJsonConvert.SerializeObject(dataFactory, client.SerializationSettings));

while (client.Factories.Get(resourceGroup, dataFactoryName).ProvisioningState == "PendingCreation")
{
    System.Threading.Thread.Sleep(1000);
}
```

## <a name="create-a-linked-service"></a>Criar um serviço vinculado

Adicione o código a seguir, que cria um **serviço vinculado do Armazenamento do Azure**, ao método **Main**.

Os serviços vinculados são criados em um data factory para vincular seus armazenamentos de dados e serviços de computação ao data factory. Neste guia de início rápido, você só precisa criar um serviço vinculado do Armazenamento do Azure para ambos a origem da cópia e o repositório de coletor, denominado "AzureStorageLinkedService" na amostra.

```csharp
// Create an Azure Storage linked service
Console.WriteLine("Creating linked service " + storageLinkedServiceName + "...");

LinkedServiceResource storageLinkedService = new LinkedServiceResource(
    new AzureStorageLinkedService
    {
        ConnectionString = new SecureString("DefaultEndpointsProtocol=https;AccountName=" + storageAccount + ";AccountKey=" + storageKey)
    }
);
client.LinkedServices.CreateOrUpdate(resourceGroup, dataFactoryName, storageLinkedServiceName, storageLinkedService);
Console.WriteLine(SafeJsonConvert.SerializeObject(storageLinkedService, client.SerializationSettings));
```

## <a name="create-a-dataset"></a>Criar um conjunto de dados

Adicione o código a seguir, que cria um **Conjunto de Dados do Blob do Azure**, ao método **Main**.

Você define um conjunto de dados que representa os dados a copiar de uma origem para um coletor. Neste exemplo, esse conjunto de dados de Blob faz referência ao serviço vinculado do Armazenamento do Azure que você criou na etapa anterior. O conjunto de dados usa um parâmetro cujo valor é definido em uma atividade que consome o conjunto de dados. O parâmetro é usado para construir o "folderPath" que aponta para o local em que os dados residem/estão armazenados.

```csharp
// Create a Azure Blob dataset
Console.WriteLine("Creating dataset " + blobDatasetName + "...");
DatasetResource blobDataset = new DatasetResource(
    new AzureBlobDataset
    {
        LinkedServiceName = new LinkedServiceReference
        {
            ReferenceName = storageLinkedServiceName
        },
        FolderPath = new Expression { Value = "@{dataset().path}" },
        Parameters = new Dictionary<string, ParameterSpecification>
        {
            { "path", new ParameterSpecification { Type = ParameterType.String } }

        }
    }
);
client.Datasets.CreateOrUpdate(resourceGroup, dataFactoryName, blobDatasetName, blobDataset);
Console.WriteLine(SafeJsonConvert.SerializeObject(blobDataset, client.SerializationSettings));
```

## <a name="create-a-pipeline"></a>Criar uma pipeline

Adicione o código a seguir, que cria um **pipeline com uma atividade de cópia**, ao método **Main**.

Neste exemplo, este pipeline contém uma atividade e leva dois parâmetros – caminho do blob de entrada e caminho do blob de saída. Os valores para esses parâmetros são definidos quando o pipeline é disparado/executado. A atividade de cópia refere-se ao mesmo conjunto de dados de blob criado na etapa anterior como entrada e saída. Quando o conjunto de dados é usado como um conjunto de dados de entrada, o caminho de entrada é especificado. E quando o conjunto de dados é usado como um conjunto de dados de saída, o caminho de saída é especificado. 

```csharp
// Create a pipeline with a copy activity
Console.WriteLine("Creating pipeline " + pipelineName + "...");
PipelineResource pipeline = new PipelineResource
{
    Parameters = new Dictionary<string, ParameterSpecification>
    {
        { "inputPath", new ParameterSpecification { Type = ParameterType.String } },
        { "outputPath", new ParameterSpecification { Type = ParameterType.String } }
    },
    Activities = new List<Activity>
    {
        new CopyActivity
        {
            Name = "CopyFromBlobToBlob",
            Inputs = new List<DatasetReference>
            {
                new DatasetReference()
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.inputPath" }
                    }
                }
            },
            Outputs = new List<DatasetReference>
            {
                new DatasetReference
                {
                    ReferenceName = blobDatasetName,
                    Parameters = new Dictionary<string, object>
                    {
                        { "path", "@pipeline().parameters.outputPath" }
                    }
                }
            },
            Source = new BlobSource { },
            Sink = new BlobSink { }
        }
    }
};
client.Pipelines.CreateOrUpdate(resourceGroup, dataFactoryName, pipelineName, pipeline);
Console.WriteLine(SafeJsonConvert.SerializeObject(pipeline, client.SerializationSettings));
```

## <a name="create-a-pipeline-run"></a>Criar uma execução de pipeline

Adicione o código a seguir, que **dispara uma execução de pipeline**, ao método **Main**.

Esse código também define os valores dos parâmetros **inputPath** e **outputPath** especificados no pipeline com os valores reais dos caminhos de blob de origem e de coletor.

```csharp
// Create a pipeline run
Console.WriteLine("Creating pipeline run...");
Dictionary<string, object> parameters = new Dictionary<string, object>
{
    { "inputPath", inputBlobPath },
    { "outputPath", outputBlobPath }
};
CreateRunResponse runResponse = client.Pipelines.CreateRunWithHttpMessagesAsync(resourceGroup, dataFactoryName, pipelineName, parameters).Result.Body;
Console.WriteLine("Pipeline run ID: " + runResponse.RunId);
```

## <a name="monitor-a-pipeline-run"></a>Monitorar uma execução de pipeline

1. Adicione o código a seguir ao método **Main** para verificar continuamente o status até que ele termine de copiar os dados.

    ```csharp
    // Monitor the pipeline run
    Console.WriteLine("Checking pipeline run status...");
    PipelineRun pipelineRun;
    while (true)
    {
        pipelineRun = client.PipelineRuns.Get(resourceGroup, dataFactoryName, runResponse.RunId);
        Console.WriteLine("Status: " + pipelineRun.Status);
        if (pipelineRun.Status == "InProgress")
            System.Threading.Thread.Sleep(15000);
        else
            break;
    }
    ```

2. Adicione o código a seguir ao método **Main** para recuperar os detalhes de execução da atividade de cópia, por exemplo, o tamanho dos dados lidos/gravados.

    ```csharp
    // Check the copy activity run details
    Console.WriteLine("Checking copy activity run details...");
   
    List<ActivityRun> activityRuns = client.ActivityRuns.ListByPipelineRun(
    resourceGroup, dataFactoryName, runResponse.RunId, DateTime.UtcNow.AddMinutes(-10), DateTime.UtcNow.AddMinutes(10)).ToList(); 
    if (pipelineRun.Status == "Succeeded")
        Console.WriteLine(activityRuns.First().Output);
    else
        Console.WriteLine(activityRuns.First().Error);
    Console.WriteLine("\nPress any key to exit...");
    Console.ReadKey();
    ```

## <a name="run-the-code"></a>Executar o código

Compile e inicie o aplicativo, então verifique a execução do pipeline.

O console imprime o progresso de criação do data factory, do serviço vinculado, dos conjuntos de dados, do pipeline e da execução de pipeline. Em seguida, ele verifica o status da execução de pipeline. Aguarde até ver os detalhes de execução da atividade de cópia com o tamanho dos dados lidos/gravados. Em seguida, use ferramentas como o [Gerenciador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) para verificar se os blobs são copiados de "inputBlobPath" para "outputBlobPath" conforme você especificou nas variáveis.

### <a name="sample-output"></a>Resultado de exemplo: 
```json
Creating data factory SPv2Factory0907...
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "East US"
}
Creating linked service AzureStorageLinkedService...
{
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": {
        "value": "DefaultEndpointsProtocol=https;AccountName=<storageAccountName>;AccountKey=<storageAccountKey>",
        "type": "SecureString"
      }
    }
  }
}
Creating dataset BlobDataset...
{
  "properties": {
    "type": "AzureBlob",
    "typeProperties": {
      "folderPath": {
        "value": "@{dataset().path}",
        "type": "Expression"
      }
    },
    "linkedServiceName": {
      "referenceName": "AzureStorageLinkedService",
      "type": "LinkedServiceReference"
    },
    "parameters": {
      "path": {
        "type": "String"
      }
    }
  }
}
Creating pipeline Adfv2QuickStartPipeline...
{
  "properties": {
    "activities": [
      {
        "type": "Copy",
        "typeProperties": {
          "source": {
            "type": "BlobSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
        "inputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.inputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "outputs": [
          {
            "referenceName": "BlobDataset",
            "parameters": {
              "path": "@pipeline().parameters.outputPath"
            },
            "type": "DatasetReference"
          }
        ],
        "name": "CopyFromBlobToBlob"
      }
    ],
    "parameters": {
      "inputPath": {
        "type": "String"
      },
      "outputPath": {
        "type": "String"
      }
    }
  }
}
Creating pipeline run...
Pipeline run ID: 308d222d-3858-48b1-9e66-acd921feaa09
Checking pipeline run status...
Status: InProgress
Status: InProgress
Checking copy activity run details...
{
    "dataRead": 331452208,
    "dataWritten": 331452208,
    "copyDuration": 23,
    "throughput": 14073.209,
    "errors": [],
    "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (West US)",
    "usedCloudDataMovementUnits": 2,
    "billedDuration": 23
}

Press any key to exit...
```

## <a name="verify-the-output"></a>Verificar a saída
O pipeline cria automaticamente a pasta de saída no contêiner de blob adftutorial. Em seguida, ele copia o arquivo emp.txt da pasta de entrada para a pasta de saída. 

1. No Portal do Azure, na página de contêiner **adftutorial**, clique em **Atualizar** para ver a pasta de saída. 
    
    ![Atualizar](media/quickstart-create-data-factory-dot-net/output-refresh.png)
2. Clique em **saída** na lista de pastas. 
2. Confirme que **emp.txt** tenha sido copiado para a pasta de saída. 

    ![Atualizar](media/quickstart-create-data-factory-dot-net/output-file.png)

## <a name="clean-up-resources"></a>Limpar recursos
Para excluir programaticamente o data factory, adicione as linhas de código a seguir ao programa: 

```csharp
            Console.WriteLine("Deleting the data factory");
            client.Factories.Delete(resourceGroup, dataFactoryName);
```

## <a name="next-steps"></a>Próximas etapas
O pipeline nessa amostra copia dados de uma localização para outra em um Armazenamento de Blobs do Azure. Percorra os [tutoriais](tutorial-copy-data-dot-net.md) para saber mais sobre o uso do Data Factory em mais cenários. 
