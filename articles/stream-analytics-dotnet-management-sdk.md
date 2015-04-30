<properties 
	pageTitle="Use o gerenciamento do SDK .NET do Azure Stream Analytics | Azure" 
	description="Saiba como usar o SDK .NET do Stream Analytics Management" 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/05/2015" 
	ms.author="jgao"/>


# Use o SDK .NET do Azure Stream Analytics Management

[Esta é uma documentação de pré-lançamento e está sujeita a alterações em versões futuras.] 

O Azure Stream Analytics é um serviço completamente gerenciado que oferece baixa latência, alta disponibilidade e processamento escalonável de eventos complexos por streaming de dados na nuvem. Na versão de teste, o Stream Analytics permite que os clientes configurem trabalhos de streaming para analisar fluxos de dados e realizem análise quase em tempo real.  

Este artigo demonstra como usar o SDK .NET do Azure Stream Analytics Management.


## Pré-requisitos
Antes de começar este artigo, você deve ter o seguinte:

- Instale o Visual Studio 2012 ou 2013.
- Baixe e instale o [SDK .NET do Azure](http://azure.microsoft.com/downloads/). 
- Crie um grupo de recursos do Azure em sua assinatura. O seguinte é um exemplo de script do PowerShell do Azure. Para obter informações do PowerShell do Azure, consulte [Instalar e configurar o PowerShell do Azure](install-configure-powershell.md);  


		# Configure a sessão do PowerShell do Azure para acessar o Gerenciador de Recursos do Azure
		Switch-AzureMode AzureResourceManager

		# Faça logon na sua conta do Azure
		Add-AzureAccount

		# Selecione a assinatura do Azure que deseja usar para criar o grupo de recursos
		Select-AzureSubscription -SubscriptionName <subscription name>

		# Criar um grupo de recursos do Azure	
		New-AzureResourceGroup -Name <YOUR RESORUCE GROUP NAME> -Location <LOCATION>


-	Configure uma origem de entrada e o destino de saída para usar. Consulte [Inrodução ao uso do Azure Stream Analytics](stream-analytics-get-started.md) para configurar uma entrada e/ou saída de exemplo a ser usada por este artigo.


## Configurar um projeto

1. Crie um aplicativo de console do Visual Studio C# .NET.
2. No Console do Gerenciador de pacotes, execute os seguintes comandos para instalar os pacotes do NuGet. O primeiro é o SDK .NET do Azure Stream Analytics Management. O segundo é o cliente do Active Directory do Azure que será usado para autenticação.

		Install-Package Microsoft.Azure.Management.StreamAnalytics -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Adicione a seguinte seção **appSettings** ao arquivo App.config:

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="<YOUR AZURE SUBSCRIPTION>" />
		  <add key="ActiveDirectoryTenantId" value="<YOU TENANT ID>" />
		</appSettings>


	Substitua os valores por **SubscriptionId** e **ActiveDirectoryTenantId** com suas IDs de assinatura e locatário do Azure. Você pode obter esses valores executando o seguinte cmdlet do PowerShell do Azure:

		Get-AzureAccount

5. Adicione as seguintes declarações **using** para o arquivo de origem (Program.cs) no projeto:

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	Adicione um método auxiliar de autenticação:

		public static string GetAuthorizationHeader()
		{
		    AuthenticationResult result = null;
		    var thread = new Thread(() =>
		    {
		        try
		        {
		            var context = new AuthenticationContext(
						ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
						ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);
		
		            result = context.AcquireToken(
		                resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
		                clientId: ConfigurationManager.AppSettings["AsaClientId"],
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


## Crie um cliente de gerenciamento do Stream Analytics

Um objeto **StreamAnalyticsManagementClient** permite que você gerencie o trabalho e os componentes de trabalho, como entrada, saída e transformação. 

Adicione o seguinte código ao início do método **Main**: 

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
	
	// Obtenha o token de autenticação
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());
	
	// Criar o cliente do Stream Analytics Management
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

O valor da variável **resourceGroupName** deve ser igual ao nome do grupo de recursos que você criou ou escolheu nas etapas de pré-requisito.

As seções restantes deste artigo pressupõem que esse código esteja no início do método **Principal**.

## Criar um trabalho de análise de fluxo

O código a seguir cria um trabalho do Stream Analytics sob o grupo de recursos que você definiu. Você adicionará entrada, saída e transformação ao trabalho mais tarde.

	// Criar um trabalho do Stream Analytics
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};
	
	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Criar uma fonte de entrada do Stream Analytics

O código a seguir cria uma fonte de entrada do Stream Analytics com o tipo de fonte de entrada de blob e serialização de CSV. Para criar uma fonte de entrada de hub de eventos, use **EventHubStreamInputDataSource** em vez de **BlobStreamInputDataSource**. Da mesma forma, você pode personalizar o tipo de serialização da fonte de entrada.

	// Criar uma fonte de entrada do Stream Analytics
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};
	
	InputCreateOrUpdateResponse inputCreateResponse = 
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Fontes de entrada são vinculados a um trabalho específico. Para usar a mesma fonte de entrada para trabalhos diferentes, chame o método novamente e especifique um nome de trabalho diferente.


## Testar uma fonte de entrada do Stream Analytics

O método **TestConnection** testa se o trabalho do Stream Analytics é capaz de se conectar à fonte de entrada, bem como outros aspectos específicos para o tipo de fonte de entrada. Por exemplo, em que na fonte de entrada de blob criada em uma etapa anterior, o método verificará se o nome da conta de armazenamento e o par de chaves podem ser usados para conectar-se à conta de armazenamento, bem como verificará se o contêiner especificado existe.

	// Testar conexão de fonte de entrada
	DataSourceTestConnectionResponse inputTestResponse = 
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Criar um destino de saída do Stream Analytics

Criar um destino de saída é muito semelhante a criar uma fonte de entrada do Stream Analytics. Como as fontes de entrada, os destinos de saída são vinculados a trabalho específico. Para usar o mesmo destino de saída para diferentes trabalhos, chame o método novamente e especifique um nome de trabalho diferente.

O código a seguir cria um destino de saída (Banco de Dados SQL do Azure). Você pode personalizar o tipo de dados de destino de saída e/ou o tipo de serialização.

	// Criar um destino de saída do Stream Analytics
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};
	
	OutputCreateOrUpdateResponse outputCreateResponse = 
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Testar um destino de saída do Stream Analytics

Um destino de saída do Stream Analytics também tem um método **TestConnection** para conexões de teste.

	// Testar a conexão de destino de saída
	DataSourceTestConnectionResponse outputTestResponse = 
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Criar uma transformação do Stream Analytics

O código a seguir cria uma transformação do Stream Analytics com a consulta "selecionar * da entrada" e especifica para alocar uma unidade de streaming para o trabalho do Stream Analytics. Para obter mais informações sobre o ajuste de unidades de streaming, consulte [Dimensionar trabalhos do Azure Stream Analytics](stream-analytics-escale-jobs.md).


	// Criar uma transformação do Stream Analytics
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};
	
	var transformationCreateResp = 
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Como entrada e saída, uma transformação também está vinculada ao trabalho de Stream Analytics específico sob o qual foi criado.

## Iniciar um trabalho do Stream Analytics
Depois de criar um trabalho do Stream Analytics e suas entradas, saídas e transformação, você pode iniciar o trabalho chamando o método **Iniciar**. 

O código de exemplo a seguir inicia um trabalho do Stream Analytics com uma hora de início de saída personalizada definida para 12 de dezembro de 2012, 12h12min12 UTC:

	// Iniciar um trabalho do Stream Analytics
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};
	
	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Interromper um trabalho do Stream Analytics
Você pode interromper um trabalho do Stream Analytics em execução chamando o método **Parar**.

	// Interromper um trabalho do Stream Analytics
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Excluir um trabalho do Stream Analytics
O método **Excluir** excluirá o trabalho, bem como os sub-recursos subjacentes, incluindo entradas, saídas e transformação do trabalho.

	// Excluir um trabalho do Stream Analytics
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## Próximas etapas

- [Introdução ao Azure Stream Analytics][stream.analytics.introduction]
- [Introdução ao uso do Azure Stream Analytics][stream.analytics.get.started]
- [Dimensionar trabalhos do Azure Stream Analytics][stream.analytics.scale.jobs]
- [Limitações e problemas conhecidos do Azure Stream Analytics][stream.analytics.limitations]
- [Referência de linguagem de consulta do Azure Stream Analytics][stream.analytics.query.language.reference]
- [Referência da API REST de Gerenciamento do Azure Stream Analytics][stream.analytics.rest.api.reference] 



<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.limitations]: stream-analytics-limitations.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=52-->