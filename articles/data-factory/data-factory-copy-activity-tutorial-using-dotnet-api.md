---
title: "Tutorial: criar um pipeline com Atividade de Cópia usando a API .NET | Microsoft Docs"
description: "Neste tutorial, você cria um pipeline do Azure Data Factory com uma Atividade de Cópia usando a REST .NET."
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 58fc4007-b46d-4c8e-a279-cb9e479b3e2b
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/27/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: d175d3d4d7d7a58f071dab0f32e3fdd3cb3146ce
ms.openlocfilehash: 8c26d8ef39827cff87b7fc7f17fab78c627a5035


---
# <a name="tutorial-create-a-pipeline-with-copy-activity-using-net-api"></a>Tutorial: criar um pipeline com Atividade de Cópia usando a API .NET
> [!div class="op_single_selector"]
> * [Visão geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Assistente de Cópia](data-factory-copy-data-wizard-tutorial.md)
> * [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
> * [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
> * [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
> * [Modelo do Azure Resource Manager](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
> * [API REST](data-factory-copy-activity-tutorial-using-rest-api.md)
> * [API do .NET](data-factory-copy-activity-tutorial-using-dotnet-api.md)

Este tutorial mostra como criar e monitorar um data factory do Azure usando a API .NET. O pipeline no data factory usa uma Atividade de Cópia para copiar dados do Armazenamento de Blobs do Azure para o Banco de Dados SQL.

A atividade de cópia realiza a movimentação de dados no Azure Data Factory. A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

> [!NOTE]
> Este artigo não abrange toda a API .NET de Data Factory. Veja [Referência de API .NET de Data Factory](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre o SDK do .NET de Data Factory.

## <a name="prerequisites"></a>Pré-requisitos
* Examine [Visão geral e pré-requisitos do tutorial](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter uma visão geral do tutorial e concluir as etapas de **pré-requisitos** .
* Visual Studio 2012 ou 2013 ou 2015.
* Baixar e instalar o [SDK .NET do Azure](http://azure.microsoft.com/downloads/)
* PowerShell do Azure. Siga as instruções no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs) para instalar a última versão do Azure PowerShell no computador. Você pode usar o Azure PowerShell para criar um aplicativo do Azure Active Directory.

### <a name="create-an-application-in-azure-active-directory"></a>Criar um aplicativo no Azure Active Directory
Crie um aplicativo do Azure Active Directory, crie uma entidade de serviço para o aplicativo e atribua-a à função **Colaborador de Data Factory** .

1. Inicie o **PowerShell**.
2. Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.

    ```PowerShell
    Login-AzureRmAccount
    ```
3. Execute o comando a seguir para exibir todas as assinaturas dessa conta.

    ```PowerShell
    Get-AzureRmSubscription
    ```
4. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **&lt;NameOfAzureSubscription**&gt; pelo nome da sua assinatura do Azure.

    ```PowerShell
    Get-AzureRmSubscription -SubscriptionName <NameOfAzureSubscription> | Set-AzureRmContext
    ```

   > [!IMPORTANT]
   > Anote **SubscriptionId** e **TenantId** da saída desse comando.

5. Crie um grupo de recursos do Azure denominado **ADFTutorialResourceGroup** executando o comando a seguir no PowerShell.

    ```PowerShell
    New-AzureRmResourceGroup -Name ADFTutorialResourceGroup  -Location "West US"
    ```

    Se o grupo de recursos já existe, especifique se deseja atualizá-lo (S) ou mantê-lo como (N).

    Se você utilizar um grupo de recursos diferente, precisará usar o nome do seu grupo de recursos no lugar de ADFTutorialResourceGroup neste tutorial.
6. Criar um aplicativo do Azure Active Directory.

    ```PowerShell
    $azureAdApplication = New-AzureRmADApplication -DisplayName "ADFCopyTutotiralApp" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.adfcopytutorialapp.org/example" -Password "Pass@word1"
    ```

    Se você receber o erro a seguir, especifique uma URL diferente e execute o comando novamente.
    
    ```PowerShell
    Another object with the same value for property identifierUris already exists.
    ```
7. Crie a entidade de serviço do AD.

    ```PowerShell
    New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
    ```
8. Adicione a entidade de serviço à função de **Colaborador do Data Factory** .

    ```PowerShell
    New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
    ```
9. Obtenha a ID do aplicativo.

    ```PowerShell
    $azureAdApplication 
    ```
    Anote a ID do aplicativo (**applicationID** da saída).

Você deve ter quatro valores após estas etapas:

* ID do locatário
* ID da assinatura
* ID do aplicativo
* Senha (especificada no primeiro comando)

## <a name="walkthrough"></a>Passo a passo
1. Usando o Visual Studio 2012/2013/2015, crie um aplicativo de console C# .NET.
   1. Inicie o **Visual Studio** 2012/2013/2015.
   2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
   3. Expanda **Modelos** e selecione **Visual C#**. Neste passo a passo, você usa C#, mas você pode usar qualquer linguagem .NET.
   4. Selecione **Aplicativo de console** na lista de tipos de projetos à direita.
   5. Digite **DataFactoryAPITestApp** como o Nome.
   6. Selecione **C:\ADFGetStarted** como o Local.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gerenciador de Pacotes Nuget** e clique em **Console do Gerenciador de Pacotes**.
3. No **Console do Gerenciador de Pacotes**, realize as seguintes etapas:
   1. Execute o seguinte comando para instalar o pacote de Data Factory: `Install-Package Microsoft.Azure.Management.DataFactories`
   2. Execute o seguinte comando para instalar o pacote do Azure Active Directory (use a API do Active Directory no código): `Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213`
4. Adicione a seção **appSetttings** a seguir ao arquivo **App.config**. Essas configurações são usadas pelo método auxiliar: **GetAuthorizationHeader**.

    Substitua os valores de **&lt;ID do aplicativo&gt;**, **&lt;Senha&gt;**, **&lt;ID da assinatura&gt;** e **&lt;ID do locatário&gt;** pelos seus próprios valores.

    ```xml
    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
        <startup>
            <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2" />
        </startup>
        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <add key="ApplicationId" value="your application ID" />
            <add key="Password" value="Password you used while creating the AAD application" />
            <add key="SubscriptionId" value= "Subscription ID" />
            <add key="ActiveDirectoryTenantId" value="Tenant ID" />
        </appSettings>
    </configuration>
    ```

5. Adicione as seguintes declarações **using** ao arquivo de origem (Program.cs) no projeto.

    ```csharp
    using System.Threading;
    using System.Configuration;
    using System.Collections.ObjectModel;

    using Microsoft.Azure.Management.DataFactories;
    using Microsoft.Azure.Management.DataFactories.Models;
    using Microsoft.Azure.Management.DataFactories.Common.Models;

    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Microsoft.Azure;
    ```

6. Adicione o seguinte código que cria uma instância de classe **DataPipelineManagementClient** ao método **Main**. Você usa esse objeto para criar um data factory, um serviço vinculado, conjunto de dados de entrada e de saída e um pipeline. Você também usa esse objeto para monitorar as partes de um conjunto de dados em tempo de execução.

    ```csharp
    // create data factory management client
    string resourceGroupName = "ADFTutorialResourceGroup";
    string dataFactoryName = "APITutorialFactory";

    TokenCloudCredentials aadTokenCredentials =
        new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeader());

    Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);
    ```

   > [!IMPORTANT]
   > Substitua o valor de **resourceGroupName** pelo nome do seu grupo de recursos do Azure.
   >
   > O nome da atualização de data factory (**dataFactoryName**) deve ser exclusivo. O nome de data factory deve ser exclusivo. Consulte o tópico [Data Factory - regras de nomenclatura](data-factory-naming-rules.md) para ver as regras de nomenclatura para artefatos de Data Factory.

7. Adicione o seguinte código que cria um **data factory** no método **Main**.

    ```csharp
    // create a data factory
    Console.WriteLine("Creating a data factory");
    client.DataFactories.CreateOrUpdate(resourceGroupName,
        new DataFactoryCreateOrUpdateParameters()
        {
            DataFactory = new DataFactory()
            {
                Name = dataFactoryName,
                Location = "westus",
                Properties = new DataFactoryProperties() { }
            }
        }
    );
    ```

8. Adicione o seguinte código, que cria um **serviço vinculado do Armazenamento do Azure** para o método **Main**.

   > [!IMPORTANT]
   > Substitua **storageaccountname** e **accountkey** pelo nome e pela chave da sua conta de Armazenamento do Azure.

    ```csharp
    // create a linked service for input data store: Azure Storage
    Console.WriteLine("Creating Azure Storage linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureStorageLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<storageaccountname>;AccountKey=<accountkey>")
                )
            }
        }
    );
    ```

9. Adicione o seguinte código que cria um **serviço vinculado SQL do Azure** no método **Main**.

   > [!IMPORTANT]
   > Substitua **servername**, **databasename**, **username** e **password** pelos nomes de seu servidor SQL do Azure, banco de dados, usuário e senha.

    ```csharp
    // create a linked service for output data store: Azure SQL Database
    Console.WriteLine("Creating Azure SQL Database linked service");
    client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new LinkedServiceCreateOrUpdateParameters()
        {
            LinkedService = new LinkedService()
            {
                Name = "AzureSqlLinkedService",
                Properties = new LinkedServiceProperties
                (
                    new AzureSqlDatabaseLinkedService("Data Source=tcp:<servername>.database.windows.net,1433;Initial Catalog=<databasename>;User ID=<username>;Password=<password>;Integrated Security=False;Encrypt=True;Connect Timeout=30")
                )
            }
        }
    );
    ```

10. Adicione o seguinte código que cria **conjuntos de dados de entrada e de saída** para o método **Main**.

    ```csharp
    // create input and output datasets
    Console.WriteLine("Creating input and output datasets");
    string Dataset_Source = "DatasetBlobSource";
    string Dataset_Destination = "DatasetAzureSqlDestination";

    Console.WriteLine("Creating input dataset of type: Azure Blob");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,

    new DatasetCreateOrUpdateParameters()
    {
        Dataset = new Dataset()
        {
            Name = Dataset_Source,
            Properties = new DatasetProperties()
            {
                Structure = new List<DataElement>()
                {
                    new DataElement() { Name = "FirstName", Type = "String" },
                    new DataElement() { Name = "LastName", Type = "String" }
                },
                LinkedServiceName = "AzureStorageLinkedService",
                TypeProperties = new AzureBlobDataset()
                {
                    FolderPath = "adftutorial/",
                    FileName = "emp.txt"
                },
                External = true,
                Availability = new Availability()
                {
                    Frequency = SchedulePeriod.Hour,
                    Interval = 1,
                },

                Policy = new Policy()
                {
                    Validation = new ValidationPolicy()
                    {
                        MinimumRows = 1
                    }
                }
            }
        }
    });

    Console.WriteLine("Creating output dataset of type: Azure SQL");
    client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new DatasetCreateOrUpdateParameters()
        {
            Dataset = new Dataset()
            {
                Name = Dataset_Destination,
                Properties = new DatasetProperties()
                {
                    Structure = new List<DataElement>()
                    {
                        new DataElement() { Name = "FirstName", Type = "String" },
                        new DataElement() { Name = "LastName", Type = "String" }
                    },
                    LinkedServiceName = "AzureSqlLinkedService",
                    TypeProperties = new AzureSqlTableDataset()
                    {
                        TableName = "emp"
                    },

                    Availability = new Availability()
                    {
                        Frequency = SchedulePeriod.Hour,
                        Interval = 1,
                    },
                }
            }
        });
    ```

11. Adicione o código a seguir, que **cria e ativa um pipeline** no método **Main**. Essa pipeline tem uma **CopyActivity** que usa **BlobSource** como fonte e **BlobSink** como coletor.

    ```csharp
    // create a pipeline
    Console.WriteLine("Creating a pipeline");
    DateTime PipelineActivePeriodStartTime = new DateTime(2016, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
    DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
    string PipelineName = "ADFTutorialPipeline";

    client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
        new PipelineCreateOrUpdateParameters()
        {
            Pipeline = new Pipeline()
            {
                Name = PipelineName,
                Properties = new PipelineProperties()
                {
                    Description = "Demo Pipeline for data transfer between blobs",

            // Initial value for pipeline's active period. With this, you won't need to set slice status
            Start = PipelineActivePeriodStartTime,
                    End = PipelineActivePeriodEndTime,

                    Activities = new List<Activity>()
                    {
                        new Activity()
                        {
                            Name = "BlobToAzureSql",
                            Inputs = new List<ActivityInput>()
                            {
                                new ActivityInput() {
                                    Name = Dataset_Source
                                }
                            },
                            Outputs = new List<ActivityOutput>()
                            {
                                new ActivityOutput()
                                {
                                    Name = Dataset_Destination
                                }
                            },
                            TypeProperties = new CopyActivity()
                            {
                                Source = new BlobSource(),
                                Sink = new BlobSink()
                                {
                                    WriteBatchSize = 10000,
                                    WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                }
                            }
                        }
                    },
                }
            }
        });
    ```

12. Adicione o seguinte código ao método **Principal** para obter o status de uma fatia de dados do conjunto de dados de saída. Há apenas uma fatia esperada neste exemplo.

    ```csharp
    // Pulling status within a timeout threshold
    DateTime start = DateTime.Now;
    bool done = false;

    while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
    {
        Console.WriteLine("Pulling the slice status");
        // wait before the next status check
        Thread.Sleep(1000 * 12);

        var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Dataset_Destination,
            new DataSliceListParameters()
            {
                DataSliceRangeStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                DataSliceRangeEndTime = PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString()
            });

        foreach (DataSlice slice in datalistResponse.DataSlices)
        {
            if (slice.State == DataSliceState.Failed || slice.State == DataSliceState.Ready)
            {
                Console.WriteLine("Slice execution is done with status: {0}", slice.State);
                done = true;
                break;
            }
            else
            {
                Console.WriteLine("Slice status is: {0}", slice.State);
            }
        }
    }
    ```

13. Adicione o seguinte código para obter detalhes de execução de uma fatia de dados ao método **Principal** .

    ```csharp
    Console.WriteLine("Getting run details of a data slice");

    // give it a few minutes for the output slice to be ready
    Console.WriteLine("\nGive it a few minutes for the output slice to be ready and press any key.");
    Console.ReadKey();

    var datasliceRunListResponse = client.DataSliceRuns.List(
            resourceGroupName,
            dataFactoryName,
            Dataset_Destination,
            new DataSliceRunListParameters()
            {
                DataSliceStartTime = PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString()
            }
        );

    foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
    {
        Console.WriteLine("Status: \t\t{0}", run.Status);
        Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
        Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
        Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
        Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
        Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
        Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
    }

    Console.WriteLine("\nPress any key to exit.");
    Console.ReadKey();
    ```

14. Adicione o método auxiliar a seguir usado pelo método **Main** na classe **Program**.

    ```csharp
    public static string GetAuthorizationHeader()
    {
        AuthenticationResult result = null;
        var thread = new Thread(() =>
        {
            try
            {
                var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                ClientCredential credential = new ClientCredential(ConfigurationManager.AppSettings["ApplicationId"], ConfigurationManager.AppSettings["Password"]);
                result = context.AcquireToken(resource: ConfigurationManager.AppSettings["WindowsManagementUri"], clientCredential: credential);
            }
            catch (Exception threadEx)
            {
                Console.WriteLine(threadEx.Message);
            }
        });

        thread.SetApartmentState(ApartmentState.STA);
        thread.Name = "AcquireTokenThread";
        thread.Start();
        thread.Join();

        if (result != null)
        {
            return result.AccessToken;
        }

        throw new InvalidOperationException("Failed to acquire token");
    }
    ```

15. No Gerenciador de Soluções, expanda o projeto (**DataFactoryAPITestApp**), clique com o botão direito do mouse em **Referências** e clique em **Adicionar Referência**. Marque a caixa de seleção do assembly "**System.Configuration**" e clique em **OK**.
16. Compile o aplicativo de console. Clique no menu **Compilar** e clique em **Solução de Compilação**.
17. Confirme se há pelo menos um arquivo no contêiner **adftutorial** no seu armazenamento de BLOBs do Azure. Caso contrário, crie o arquivo **Emp.txt** no bloco de notas com o seguinte conteúdo e carregue-o no contêiner adftutorial.

    ```
    John, Doe
    Jane, Doe
    ```
18. Execute o exemplo, clicando em **Depurar** -> **Iniciar Depuração** no menu. Quando você vir **Obter detalhes de execução de uma fatia de dados**, aguarde alguns minutos e pressione **ENTER**.
19. Use o Portal do Azure para verificar se a data factory **APITutorialFactory** foi criada com os seguintes artefatos:
   * Serviço vinculado: **LinkedService_AzureStorage**
   * Conjunto de dados: **DatasetBlobSource** e **DatasetBlobDestination**.
   * Pipeline: **PipelineBlobSample**
20. Verifique se os dois registros de funcionários são criados na tabela "**emp**" do banco de dados SQL do Azure especificado.

## <a name="next-steps"></a>Próximas etapas
* Leia o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) , que fornece informações detalhadas sobre a atividade de cópia que você usou no tutorial.
* Veja [Referência de API .NET de Data Factory](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre o SDK do .NET de Data Factory. Este artigo não abrange toda a API .NET de Data Factory.




<!--HONumber=Dec16_HO3-->


