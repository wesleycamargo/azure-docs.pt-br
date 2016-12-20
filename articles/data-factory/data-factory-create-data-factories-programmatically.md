---
title: Criar, monitorar e gerenciar data factories do Azure usando o SDK do Data Factory | Microsoft Docs
description: Aprenda como criar, monitorar e gerenciar as data factories do Azure programaticamente usando o SDK da Data Factory.
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: b0a357be-3040-4789-831e-0d0a32a0bda5
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/14/2016
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 6ec8ac288a4daf6fddd6d135655e62fad7ae17c2
ms.openlocfilehash: 5a9e5a5fab294fe23f2017b16e38e9a138b71955


---
# <a name="create-monitor-and-manage-azure-data-factories-using-data-factory-net-sdk"></a>Criar, monitorar e gerenciar data factories do Azure usando o SDK do .NET da Data Factory
## <a name="overview"></a>Visão geral
Você pode criar, monitorar e gerenciar as Data Factory do Azure programaticamente usando o SDK do .NET da Data Factory. Este artigo contém uma explicação passo a passo que você pode seguir para criar um aplicativo de console .NET de exemplo que cria e monitora uma Data Factory. Consulte [Referência de biblioteca de classes de Data Factory](https://msdn.microsoft.com/library/mt415893.aspx) para obter detalhes sobre o SDK do .NET da Data Factory.

## <a name="prerequisites"></a>Pré-requisitos
* Visual Studio 2012 ou 2013 ou 2015.
* Baixe e instale o [Azure .NET SDK](http://azure.microsoft.com/downloads/).
* Adicione um aplicativo cliente nativo ao Azure Active Directory. Confira [Integrar aplicativos ao Azure Active Directory](../active-directory/active-directory-integrating-applications.md) para as etapas necessárias para adicionar o aplicativo. Anote a **ID DO CLIENTE** e o **URI de REDIRECIONAMENTO** na página **CONFIGURAR**.
* Obtenha sua **ID de assinatura** e sua **ID de locatário** do Azure. Consulte [Obter IDs da assinatura e locatário do Azure](#get-azure-subscription-and-tenant-ids) para mais instruções.
* Baixe e instale os pacotes NuGet para Data Factory do Azure. As instruções estão no passo a passo.  

## <a name="walkthrough"></a>Passo a passo
1. Usando o Visual Studio 2012 ou 2013, crie um aplicativo de console C# .NET.
   1. Inicie o **Visual Studio 2012/2013/2015**.
   2. Clique em **Arquivo**, aponte para **Novo** e clique em **Projeto**.
   3. Expanda **Modelos** e selecione **Visual C#**. Neste passo a passo, você usa C#, mas você pode usar qualquer linguagem .NET.
   4. Selecione **Aplicativo de console** na lista de tipos de projetos à direita.
   5. Digite **DataFactoryAPITestApp** como o **Nome**.
   6. Selecione **C:\ADFGetStarted** como o **Local**.
   7. Clique em **OK** para criar o projeto.
2. Clique em **Ferramentas**, aponte para **Gerenciador de Pacotes NuGet** e clique em **Console do Gerenciador de Pacotes**.
3. No **Console do Gerenciador de Pacotes**, digite os comandos a seguir, um por vez.

         Install-Package Microsoft.Azure.Management.DataFactories
         Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.19.208020213
4. Adicione a seção **appSetttings** a seguir ao arquivo **App.config**. Esses valores são usados pelo método **GetAuthorizationHeader** .

   > [!IMPORTANT]
   > Substitua os valores de **AdfClientId**, **RedirectUri**, **SubscriptionId** e **ActiveDirectoryTenantId** pelos seus próprios valores.  
   >
   >

        <appSettings>
            <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
            <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
            <add key="WindowsManagementUri" value="https://management.core.windows.net/" />

            <!-- Replace the following values with your own -->
            <add key="AdfClientId" value="Your AAD application ID" />
            <add key="RedirectUri" value="Your AAD application's redirect URI" />
            <add key="SubscriptionId" value="your subscription ID" />
            <add key="ActiveDirectoryTenantId" value="your tenant ID" />
        </appSettings>
5. Adicione as seguintes declarações **using** ao arquivo de origem (Program.cs) no projeto.

        using System.Threading;
        using System.Configuration;
        using System.Collections.ObjectModel;

        using Microsoft.Azure.Management.DataFactories;
        using Microsoft.Azure.Management.DataFactories.Models;
        using Microsoft.Azure.Management.DataFactories.Common.Models;

        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure;
6. Adicione o seguinte código que cria uma instância de classe **DataPipelineManagementClient** ao método **Main**. Você usa esse objeto para criar um data factory, um serviço vinculado, conjunto de dados de entrada e de saída e um pipeline. Você também usa esse objeto para monitorar as partes de um conjunto de dados em tempo de execução.    

        // create data factory management client
        string resourceGroupName = "resourcegroupname";
        string dataFactoryName = "APITutorialFactorySP";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataFactoryManagementClient client = new DataFactoryManagementClient(aadTokenCredentials, resourceManagerUri);

   > [!NOTE]
   > Substitua **resourcegroupname** pelo nome do seu grupo de recursos do Azure. Você pode criar um grupo de recursos usando o cmdlet [New-AzureResourceGroup](https://msdn.microsoft.com/library/Dn654594.aspx) .
   >
   >
7. Adicione o seguinte código que cria um **data factory** no método **Main**.

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
8. Adicione o seguinte código que cria um **serviço vinculado** no método **Principal**.

   > [!NOTE]
   > Use o **nome de conta** e a **chave de conta** da sua conta de armazenamento do Azure para **ConnectionString**.
   >
   >

        // create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new LinkedServiceProperties
                    (
                        new AzureStorageLinkedService("DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>")
                    )
                }
            }
        );
9. Adicione o seguinte código que cria **conjuntos de dados de entrada e de saída** para o método **Main**.

    O **FolderPath** para o blob de entrada é definido como **adftutorial/** em que **adftutoria**l é o nome do contêiner no seu armazenamento de blobs. Se esse contêiner não existir em seu armazenamento de blob do Azure, crie um contêiner com o nome **adftutorial** e carregue um arquivo de texto no contêiner.

    O FolderPath para o blob de saída é definido como: **adftutorial/apifactoryoutput /{Slice}**, em que a **Slice** é dinamicamente calculada com base no valor de **SliceStart** (data e hora de início de cada fatia).  

        // create input and output datasets
        Console.WriteLine("Creating input and output datasets");
        string Dataset_Source = "DatasetBlobSource";
        string Dataset_Destination = "DatasetBlobDestination";

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Source,
                    Properties = new DatasetProperties()
                    {
                        LinkedServiceName = "LinkedService-AzureStorage",
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

        client.Datasets.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new DatasetCreateOrUpdateParameters()
            {
                Dataset = new Dataset()
                {
                    Name = Dataset_Destination,
                    Properties = new DatasetProperties()
                    {

                        LinkedServiceName = "LinkedService-AzureStorage",
                        TypeProperties = new AzureBlobDataset()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            }
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. Adicione o código a seguir, que **cria e ativa um pipeline** no método **Main**. Essa pipeline tem uma **CopyActivity** que usa **BlobSource** como fonte e **BlobSink** como coletor.

    A atividade de cópia realiza a movimentação de dados no Azure Data Factory. A atividade é habilitada por um serviço globalmente disponível que pode copiar dados entre vários repositórios de dados de forma segura, confiável e escalonável. Veja o artigo [Atividades de movimentação de dados](data-factory-data-movement-activities.md) para obter detalhes sobre a Atividade de Cópia.

            // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

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
                                Name = "BlobToBlob",
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
11. Adicione o método auxiliar a seguir usado pelo método **Main** na classe **Program**. Esse método exibe uma caixa de diálogo que permite fornecer o **nome de usuário** e a **senha** que você usa para fazer logon no portal do Azure.

        public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
                        redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                        promptBehavior: PromptBehavior.Always);
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
12. Adicione o seguinte código ao método **Principal** para obter o status de uma fatia de dados do conjunto de dados de saída. Há apenas uma fatia esperada neste exemplo.   

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
13. **(opcional)** Adicione o seguinte código para obter detalhes de execução de uma fatia de dados ao método **Principal**.

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
14. No Gerenciador de Soluções, expanda o projeto (**DataFactoryAPITestApp**), clique com o botão direito do mouse em **Referências** e clique em **Adicionar Referência**. Selecione caixa de seleção para o assembly `System.Configuration` e clique em **OK**.
15. Compile o aplicativo de console. Clique no menu **Compilar** e clique em **Solução de Compilação**.
16. Confirme se há pelo menos um arquivo no contêiner adftutorial no seu armazenamento de BLOBs do Azure. Caso contrário, crie o arquivo de Emp.txt no bloco de notas com o seguinte conteúdo e carregue-o no contêiner adftutorial.

        John, Doe
        Jane, Doe
17. Execute o exemplo, clicando em **Depurar** -> **Iniciar Depuração** no menu. Quando você vir **Obter detalhes de execução de uma fatia de dados**, aguarde alguns minutos e pressione **ENTER**.
18. Use o Portal do Azure para verificar se a data factory **APITutorialFactory** foi criada com os seguintes artefatos:
    * Serviço vinculado: **LinkedService_AzureStorage**
    * Conjunto de dados: **DatasetBlobSource** e **DatasetBlobDestination**.
    * Pipeline: **PipelineBlobSample**
19. Verifique se um arquivo de saída foi criado na pasta **apifactoryoutput** no contêiner **adftutorial**.

## <a name="log-in-without-popup-dialog-box"></a>Logon sem caixa de diálogo pop-up
O código de exemplo no passo a passo inicia uma caixa de diálogo para inserção das credenciais do Azure. Se você precisar entrar programaticamente sem usar uma caixa de diálogo, consulte [Autenticando uma entidade de serviço com o Azure Resource Manager](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate). 

> [!IMPORTANT]
> Adicione um aplicativo Web para o Azure Active Directory e anote a ID do cliente e o segredo do cliente do aplicativo.  
>
>

### <a name="example"></a>Exemplo
Crie o método GetAuthorizationHeaderNoPopup.  

    public static string GetAuthorizationHeaderNoPopup()
    {
        var authority = new Uri(new Uri("https://login.windows.net"), ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
        var context = new AuthenticationContext(authority.AbsoluteUri);
        var credential = new ClientCredential(ConfigurationManager.AppSettings["AdfClientId"], ConfigurationManager.AppSettings["AdfClientSecret"]);
        AuthenticationResult result = context.AcquireTokenAsync(ConfigurationManager.AppSettings["WindowsManagementUri"], credential).Result;
        if (result != null)
            return result.AccessToken;

        throw new InvalidOperationException("Failed to acquire token");
    }

Substitua a chamada **GetAuthorizationHeader** por uma chamada para **GetAuthorizationHeaderNoPopup** na função **Main**:  

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
            ConfigurationManager.AppSettings["SubscriptionId"],
            GetAuthorizationHeaderNoPopup());

Aqui está como você pode criar o aplicativo do Active Directory, a entidade de serviço e, em seguida, atribuí-lo à função de Colaborador do Data Factory:

1. Configurar o aplicativo do AD.

        $azureAdApplication = New-AzureRmADApplication -DisplayName "MyADAppForADF" -HomePage "https://www.contoso.org" -IdentifierUris "https://www.myadappforadf.org/example" -Password "Pass@word1"
2. Crie a entidade de serviço do AD.

        New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId
3. Adicione a entidade de serviço à função de Colaborador do Data Factory.

        New-AzureRmRoleAssignment -RoleDefinitionName "Data Factory Contributor" -ServicePrincipalName $azureAdApplication.ApplicationId.Guid
4. Obtenha a ID do aplicativo.

        $azureAdApplication

Anote a ID do aplicativo e a senha (segredo do cliente), e use-a no passo a passo.

## <a name="get-azure-subscription-and-tenant-ids"></a>Obter IDs da assinatura e locatário do Azure
Siga as instruções do artigo [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a última versão do Azure PowerShell no computador, caso ela ainda não esteja instalada.

1. Inicie o Azure PowerShell e execute o comando a seguir
2. Execute o comando a seguir e insira o nome de usuário e a senha que você usa para entrar no portal do Azure.

        Login-AzureRmAccount

    Se você tiver apenas uma assinatura do Azure associada a essa conta, não será necessário executar as duas próximas etapas.  
3. Execute o comando a seguir para exibir todas as assinaturas dessa conta.

        Get-AzureRmSubscription
4. Execute o comando a seguir para selecionar a assinatura com a qual deseja trabalhar. Substitua **NameOfAzureSubscription** pelo nome da sua assinatura do Azure.

        Get-AzureRmSubscription -SubscriptionName NameOfAzureSubscription | Set-AzureRmContext

Anote os valores de **SubscriptionId** e **TenantId**.



<!--HONumber=Nov16_HO3-->


