<properties
	pageTitle="Introdução ao seu primeiro aplicativo de Pesquisa do Azure no .NET | Microsoft Azure | Serviço de pesquisa em nuvem hospedado"
	description="Tutorial sobre como criar uma solução usando a biblioteca de cliente .NET no SDK .NET da Pesquisa do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="02/09/2016"
	ms.author="heidist"/>

# Introdução ao seu primeiro aplicativo de Pesquisa do Azure em .NET

Aprenda como criar um aplicativo personalizado de pesquisa .Net no Visual Studio 2013 ou posterior, que use a Pesquisa do Azure para sua experiência de pesquisa. Este tutorial usa o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx), bem como a API REST do serviço de Pesquisa do Azure.

Para executar este exemplo, você precisará de um serviço de Pesquisa do Azure, no qual poderá se inscrever no [Portal do Azure](https://portal.azure.com). Para obter instruções passo a passo, veja [Criar um serviço de Pesquisa do Azure no portal](search-create-service-portal.md).

## Sobre os dados

Este exemplo de aplicativo usa dados do [USGS (Serviço Geológico dos Estados Unidos)](http://geonames.usgs.gov/domestic/download_data.htm), filtrado no estado de Rhode Island, para reduzir o tamanho do conjunto de dados. Vamos usar esses dados para criar um aplicativo de pesquisa que retorna prédios de referência, por exemplo, hospitais e escolas, e características geológicas, como rios, lagos e picos.

Neste aplicativo, o programa **DataIndexer** cria e carrega o índice usando um [Indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando o conjunto de dados filtrado do USGS de um Banco de Dados SQL do Azure público. As informações de credenciais e de conexão para a fonte de dados online são fornecidas no código-fonte. Nenhuma configuração adicional é necessária.

> [AZURE.NOTE] Aplicamos um filtro a esse conjunto de dados para permanecer abaixo do limite de 10.000 documentos da camada de preços gratuita. Se você usar um tipo de preço diferente, esse limite não se aplicará. Para obter detalhes sobre a capacidade de cada camada de preços, confira [Limites e restrições](search-limits-quotas-capacity.md).

<a id="sub-2"></a>
## Localizar o nome do serviço e chaves de Api do serviço de Pesquisa do Azure ##

Depois de criar o serviço, retorne ao portal para obter a URL ou `api-key`. Conexões com seu serviço de Pesquisa requerem que você tenha tanto uma URL quanto um `api-key` para autenticar a chamada.

1. Entre no [Portal do Azure](https://portal.azure.com).
2. Na barra de atalhos, clique em **Serviço de Pesquisa** para listar todos os serviços da Pesquisa do Azure provisionados para sua assinatura.
3. Selecione o serviço que você deseja usar.
4. No painel de serviço, você verá blocos com as informações essenciais e o ícone de chave para acessar as chaves de administrador.

   ![][3]

3. Copie a URL do serviço e uma chave de administrador. Você precisará deles posteriormente quando adicioná-los aos arquivos app.config e web.config em seus projetos do Visual Studio.

## Iniciar um novo projeto e solução

Essa solução inclui dois projetos:

- **DataIndexer**, um aplicativo de console Visual C# usado para carregar dados.
- **SimpleSearchMVCApp**, um aplicativo Web MVC ASP.NET Visual C#, usado para consultar e retornar os resultados da pesquisa.

Nesta etapa, você criará ambos estes projetos.

1. Inicie **Visual Studio** > **Novo Projeto** > **Visual C#** > **Aplicativo de Console**.
2. Nomeie o projeto **DataIndexer** e, em seguida, nomeie a solução como **AzureSearchDotNetDemo**.
3. No Gerenciador de Soluções, em solução, clique com o botão direito do mouse em **Adicionar** > **Novo projeto** > **Visual C#** > **Aplicativo Web ASP.NET**.
4. Nomeie o projeto **SimpleSearchMVCApp**.
5. No novo projeto ASP.NET, escolha o modelo MVC e limpe as opções para evitar a criação de artefatos de programa que você não usará neste tutorial.

   Desmarque as caixas de seleção para hospedagem do Azure e testes de unidade e defina Autenticação como nenhuma.

   ![][10]

Quando terminar de criar os projetos, sua solução deve ser semelhante ao exemplo abaixo.

   ![][4]

## Instalar a biblioteca de cliente .NET e atualizar outros pacotes

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet**.
2. Especifique **Atualizações** > **Apenas Estáveis** > **Atualizar Tudo**.

   ![][11]

3. Aceite as instalações de pacotes adicionais para que todas as dependências também sejam instaladas.

4. Em seguida, instale a biblioteca de cliente .NET de Pesquisa do Azure. Certifique-se de especificar a pesquisa corretamente ou você não encontrará facilmente o pacote. Clique em com o botão direito e selecione **Gerenciar Pacotes do NuGet** novamente.

5. Especifique **Online** > **nuget.org** > **Somente Estável** e, em seguida, procure *azure.search*. Clique em **Instalar** para instalar a biblioteca.

   ![][12]

## Adicionar uma referência de assembly para System.Configuration

**DataIndexer** usa **System.Configuration** para ler as definições de configuração em app.config.

1. Clique com o botão direito do mouse em **DataIndexer** > **Adicionar** > **Referência** > **Framework** > **System.Configuration**. Marque a caixa de seleção.
2. Clique em **OK**.

## Atualizar arquivos de configuração

Cada projeto inclui os arquivos de configuração que especificam o nome do serviço e a chave de api.

1. Em **DataIndexer**, substitua o App.config pelo exemplo a seguir, atualizando o [NOME DO SERVIÇO] e a [CHAVE DE SERVIÇO] com valores válidos para o seu serviço. Observe que o nome do serviço não é a URL completa. Por exemplo, se o ponto de extremidade de serviço da Pesquisa for **https://mysearchsrv.search.microsoft.net*, o nome do serviço que você inseriria em App.config será *mysearchsrv*.

		<?xml version="1.0" encoding="utf-8" ?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
		    <appSettings>
		      <add key="SearchServiceName" value="[SEARCH SERVICE]" />
		      <add key="SearchServiceApiKey" value="[SEARCH SERVICE API KEY]" />
		    </appSettings>
		</configuration>

2. Em **SimpleSearchMVCApp**, substitua o Web.config pelo exemplo a seguir, atualizando novamente o [NOME DO SERVIÇO] e a [CHAVE DE SERVIÇO] com valores válidos para o seu serviço.

		<?xml version="1.0" encoding="utf-8"?>
		<!--
			For more information on how to configure your ASP.NET application, please visit
			http://go.microsoft.com/fwlink/?LinkId=152368
			-->
		<configuration>
			<configSections>
				<!-- For more information on Entity Framework configuration, visit http://go.microsoft.com/fwlink/?LinkID=237468 -->
				<section name="entityFramework" type="System.Data.Entity.Internal.ConfigFile.EntityFrameworkSection, EntityFramework, Version=5.0.0.0, Culture=neutral, PublicKeyToken=b77a5c561934e089" requirePermission="false" />
			</configSections>
			<connectionStrings>
				<add name="DefaultConnection" providerName="System.Data.SqlClient" connectionString="Data Source=(LocalDb)\v11.0;Initial Catalog=aspnet-SimpleMVCApp-20150303114355;Integrated Security=SSPI;AttachDBFilename=|DataDirectory|\aspnet-SimpleMVCApp-20150303114355.mdf" />
			</connectionStrings>
			<appSettings>
				<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
				<add key="SearchServiceApiKey" value="[API KEY]" />

				<add key="webpages:Version" value="2.0.0.0" />
				<add key="webpages:Enabled" value="false" />
				<add key="PreserveLoginUrl" value="true" />
				<add key="ClientValidationEnabled" value="true" />
				<add key="UnobtrusiveJavaScriptEnabled" value="true" />
			</appSettings>
			<system.web>
				<httpRuntime targetFramework="4.5" />
				<compilation debug="true" targetFramework="4.5" />
				<authentication mode="Forms">
					<forms loginUrl="~/Account/Login" timeout="2880" />
				</authentication>
				<pages>
					<namespaces>
						<add namespace="System.Web.Helpers" />
						<add namespace="System.Web.Mvc" />
						<add namespace="System.Web.Mvc.Ajax" />
						<add namespace="System.Web.Mvc.Html" />
						<add namespace="System.Web.Optimization" />
						<add namespace="System.Web.Routing" />
						<add namespace="System.Web.WebPages" />
					</namespaces>
				</pages>
				<profile defaultProvider="DefaultProfileProvider">
					<providers>
						<add name="DefaultProfileProvider" type="System.Web.Providers.DefaultProfileProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</profile>
				<membership defaultProvider="DefaultMembershipProvider">
					<providers>
						<add name="DefaultMembershipProvider" type="System.Web.Providers.DefaultMembershipProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" enablePasswordRetrieval="false" enablePasswordReset="true" requiresQuestionAndAnswer="false" requiresUniqueEmail="false" maxInvalidPasswordAttempts="5" minRequiredPasswordLength="6" minRequiredNonalphanumericCharacters="0" passwordAttemptWindow="10" applicationName="/" />
					</providers>
				</membership>
				<roleManager defaultProvider="DefaultRoleProvider">
					<providers>
						<add name="DefaultRoleProvider" type="System.Web.Providers.DefaultRoleProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" applicationName="/" />
					</providers>
				</roleManager>
				<!--
								If you are deploying to a cloud environment that has multiple web server instances,
								you should change session state mode from "InProc" to "Custom". In addition,
								change the connection string named "DefaultConnection" to connect to an instance
								of SQL Server (including SQL Azure and SQL  Compact) instead of to SQL Server Express.
					-->
				<sessionState mode="InProc" customProvider="DefaultSessionProvider">
					<providers>
						<add name="DefaultSessionProvider" type="System.Web.Providers.DefaultSessionStateProvider, System.Web.Providers, Version=1.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35" connectionStringName="DefaultConnection" />
					</providers>
				</sessionState>
			</system.web>
			<system.webServer>
				<validation validateIntegratedModeConfiguration="false" />
				<handlers>
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" />
					<remove name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" />
					<remove name="ExtensionlessUrlHandler-Integrated-4.0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_32bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness32" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-ISAPI-4.0_64bit" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" modules="IsapiModule" scriptProcessor="%windir%\Microsoft.NET\Framework64\v4.0.30319\aspnet_isapi.dll" preCondition="classicMode,runtimeVersionv4.0,bitness64" responseBufferLimit="0" />
					<add name="ExtensionlessUrlHandler-Integrated-4.0" path="*." verb="GET,HEAD,POST,DEBUG,PUT,DELETE,PATCH,OPTIONS" type="System.Web.Handlers.TransferRequestHandler" preCondition="integratedMode,runtimeVersionv4.0" />
					<remove name="OPTIONSVerbHandler" />
					<remove name="TRACEVerbHandler" />
				</handlers>
			</system.webServer>
			<entityFramework>
				<defaultConnectionFactory type="System.Data.Entity.Infrastructure.LocalDbConnectionFactory, EntityFramework">
					<parameters>
						<parameter value="v12.0" />
					</parameters>
				</defaultConnectionFactory>
			</entityFramework>
			<runtime>
				<assemblyBinding xmlns="urn:schemas-microsoft-com:asm.v1">
					<dependentAssembly>
						<assemblyIdentity name="Newtonsoft.Json" publicKeyToken="30ad4fe6b2a6aeed" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-7.0.0.0" newVersion="7.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="WebGrease" publicKeyToken="31bf3856ad364e35" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-1.6.5135.21930" newVersion="1.6.5135.21930" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="Antlr3.Runtime" publicKeyToken="eb42632606e9261f" culture="neutral" />
						<bindingRedirect oldVersion="0.0.0.0-3.5.0.2" newVersion="3.5.0.2" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Helpers" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.Mvc" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-5.2.3.0" newVersion="5.2.3.0" />
					</dependentAssembly>
					<dependentAssembly>
						<assemblyIdentity name="System.Web.WebPages.Razor" publicKeyToken="31bf3856ad364e35" />
						<bindingRedirect oldVersion="1.0.0.0-3.0.0.0" newVersion="3.0.0.0" />
					</dependentAssembly>
				</assemblyBinding>
			</runtime>
		</configuration>


## Modificar DataIndexer

Esse programa é um aplicativo de console que se conecta a seu serviço de Pesquisa conforme especificado em app.config, cria o índice e, em seguida, carrega-o com o conjunto de dados de USGS armazenado em um Banco de Dados SQL do Azure. Vamos usar um indexador para esta parte do tutorial.

Antes de executar este programa, você precisará substituir **Program.cs**, usado para criar o índice, indexador, carregar os dados e gravar mensagens.

### Atualizar Program.cs

1. No Gerenciador de Soluções, abra **DataIndexer** > **Program.cs**.
2. Substitua os conteúdos do Program.cs pelo código a seguir.

	    using System;
	    using System.Configuration;
	    using System.Threading;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace DataIndexer
	    {
	        class Program
	        {
	            private const string GeoNamesIndex = "geonames";
	            private const string UsgsDataSource = "usgs-datasource";
	            private const string UsgsIndexer = "usgs-indexer";
	
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            // This Sample shows how to delete, create, upload documents and query an index
	            static void Main(string[] args)
	            {
	                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                // Create an HTTP reference to the catalog index
	                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                _indexClient = _searchClient.Indexes.GetClient(GeoNamesIndex);
	
	                Console.WriteLine("{0}", "Deleting index, data source, and indexer...\n");
	                if (DeleteIndexingResources())
	                {
	                    Console.WriteLine("{0}", "Creating index...\n");
	                    CreateIndex();
	                    Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
	                    SyncDataFromAzureSQL();
	                }
	                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
	                Console.ReadKey();
	            }
	
	            private static bool DeleteIndexingResources()
	            {
	                // Delete the index, data source, and indexer.
	                try
	                {
	                    _searchClient.Indexes.Delete(GeoNamesIndex);
	                    _searchClient.DataSources.Delete(UsgsDataSource);
	                    _searchClient.Indexers.Delete(UsgsIndexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error deleting indexing resources: {0}\r\n", ex.Message);
	                    Console.WriteLine("Did you remember to add your SearchServiceName and SearchServiceApiKey to the app.config?\r\n");
	                    return false;
	                }
	
	                return true;
	            }
	
	            private static void CreateIndex()
	            {
	                // Create the Azure Search index based on the included schema
	                try
	                {
	                    var definition = new Index()
	                    {
	                        Name = GeoNamesIndex,
	                        Fields = new[] 
	                        { 
	                            new Field("FEATURE_ID",     DataType.String)         { IsKey = true,  IsSearchable = false, IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_NAME",   DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("FEATURE_CLASS",  DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_ALPHA",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("STATE_NUMERIC",  DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("COUNTY_NAME",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("COUNTY_NUMERIC", DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_M",      DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("ELEV_IN_FT",     DataType.Int32)          { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("MAP_NAME",       DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = true,  IsSortable = true,  IsFacetable = false, IsRetrievable = true},
	                            new Field("DESCRIPTION",    DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("HISTORY",        DataType.String)         { IsKey = false, IsSearchable = true,  IsFilterable = false, IsSortable = false, IsFacetable = false, IsRetrievable = true},
	                            new Field("DATE_CREATED",   DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true},
	                            new Field("DATE_EDITED",    DataType.DateTimeOffset) { IsKey = false, IsSearchable = false, IsFilterable = true,  IsSortable = true,  IsFacetable = true,  IsRetrievable = true}
	                        }
	                    };
	
	                    _searchClient.Indexes.Create(definition);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating index: {0}\r\n", ex.Message);
	                }
	
	            }
	
	            private static void SyncDataFromAzureSQL()
	            {
	                // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
	                Console.WriteLine("{0}", "Creating Data Source...\n");
	                var dataSource =
	                    new DataSource()
	                    {
	                        Name = UsgsDataSource,
	                        Description = "USGS Dataset",
	                        Type = DataSourceType.AzureSql,
	                        Credentials = new DataSourceCredentials("Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;"),
	                        Container = new DataContainer("GeoNamesRI")
	                    };
	
	                try
	                {
	                    _searchClient.DataSources.Create(dataSource);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating data source: {0}", ex.Message);
	                    return;
	                }
	
	                Console.WriteLine("{0}", "Creating Indexer and syncing data...\n");
	
	                var indexer =
	                    new Indexer()
	                    {
	                        Name = UsgsIndexer,
	                        Description = "USGS data indexer",
	                        DataSourceName = dataSource.Name,
	                        TargetIndexName = GeoNamesIndex
	                    };
	
	                try
	                {
	                    _searchClient.Indexers.Create(indexer);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error creating and running indexer: {0}", ex.Message);
	                    return;
	                }
	
	                bool running = true;
	                Console.WriteLine("{0}", "Synchronization running...\n");
	                while (running)
	                {
	                    IndexerExecutionInfo status = null;
	
	                    try
	                    {
	                        status = _searchClient.Indexers.GetStatus(indexer.Name);
	                    }
	                    catch (Exception ex)
	                    {
	                        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
	                        return;
	                    }
	
	                    IndexerExecutionResult lastResult = status.LastResult;
	                    if (lastResult != null)
	                    {
	                        switch (lastResult.Status)
	                        {
	                            case IndexerExecutionStatus.InProgress:
	                                Console.WriteLine("{0}", "Synchronization running...\n");
	                                Thread.Sleep(1000);
	                                break;
	
	                            case IndexerExecutionStatus.Success:
	                                running = false;
	                                Console.WriteLine("Synchronized {0} rows...\n", lastResult.ItemCount);
	                                break;
	
	                            default:
	                                running = false;
	                                Console.WriteLine("Synchronization failed: {0}\n", lastResult.ErrorMessage);
	                                break;
	                        }
	                    }
	                }
	            }
	        }
	    }


## Compilar e executar DataIndexer

1. Clique com o botão direito no projeto **DataIndexer**.
2. Compile o projeto.
3. Pressione **F5** para executá-lo.

Você deverá ver uma janela de console com essas mensagens.

![][6]

No portal, você verá um novo índice **geonames**. Pode demorar vários minutos para que o portal atualize, portanto, talvez seja melhor esperar um pouco antes de verificar os resultados.

![][7]

## Alterar SimpleSearchMVCApp

**SimpleSearchMVC** é um aplicativo Web executado localmente no IIS Express. Ele fornece uma caixa de pesquisa e apresenta os resultados da pesquisa em uma tabela.

Antes de executar este programa, você fará três modificações:

- Substitua **HomeController.cs**, usado para aceitar a entrada do usuário. Neste exemplo, o termo de pesquisa é armazenado em uma variável chamada *q*.

- Substitua **index.cshtml**, as entradas de uma página da Web que fornece o termo de pesquisa e exibe os resultados da pesquisa.

- Adicione **FeatureSearch.cs**, uma classe que cria o cliente de pesquisa e executa a pesquisa.

### Atualizar HomeController.cs

Substitua o código padrão pelo código a seguir.

	    using Microsoft.Azure.Search.Models;
	    using System;
	    using System.Collections.Generic;
	    using System.Linq;
	    using System.Web;
	    using System.Web.Mvc;
	
	    namespace SimpleSearchMVCApp.Controllers
	    {
	        public class HomeController : Controller
	        {
	            //
	            // GET: /Home/
	            private FeaturesSearch _featuresSearch = new FeaturesSearch();
	
	            public ActionResult Index()
	            {
	                return View();
	            }
	
	            public ActionResult Search(string q = "")
	            {
	                // If blank search, assume they want to search everything
	                if (string.IsNullOrWhiteSpace(q))
	                    q = "*";
	
	                return new JsonResult
	                {
	                    JsonRequestBehavior = JsonRequestBehavior.AllowGet,
	                    Data = _featuresSearch.Search(q).Results
	                };
	            }
	        }
	    }


### Atualizar Index.cshtml

Substitua o código padrão pelo código a seguir.

	@{
	    ViewBag.Title = "Azure Search - Feature Search";
	}

	<script src="http://ajax.aspnetcdn.com/ajax/jQuery/jquery-1.10.2.min.js"></script>
	<script type="text/javascript">

	    $(function () {
	        // Execute search if user clicks enter
	        $("#q").keyup(function (event) {
	            if (event.keyCode == 13) {
	                Search();
	            }
	        });
	    });

	    function Search() {
	        // We will post to the MVC controller and parse the full results on the client side
	        // You may wish to do additional pre-processing on the data before sending it back to the client
	        var q = $("#q").val();

	        $.post('/home/search',
	        {
	            q: q
	        },
	        function (data) {
	            var searchResultsHTML = "<tr><td>FEATURE NAME</td><td>FEATURE CLASS</td>";
	            searchResultsHTML += "<td>STATE ALPHA</td><td>COUNTY_NAME</td>";
	            searchResultsHTML += "<td>Elevation (m)</td><td>Elevation (ft)</td><td>MAP NAME</td>";
	            searchResultsHTML += "<td>DESCRIPTION</td><td>HISTORY</td><td>DATE CREATED</td>";
	            searchResultsHTML += "<td>DATE EDITED</td></tr>";
	            for (var i = 0; i < data.length; i++) {
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.FEATURE_CLASS + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.STATE_ALPHA + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.COUNTY_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_M + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.ELEV_IN_FT + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.MAP_NAME + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.DESCRIPTION + "</td>";
	                searchResultsHTML += "<td>" + data[i].Document.HISTORY + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_CREATED) + "</td>";
	                searchResultsHTML += "<td>" + parseJsonDate(data[i].Document.DATE_EDITED) + "</td></tr>";
	            }

	            $("#searchResults").html(searchResultsHTML);

	        });

	        function parseJsonDate(jsonDateString) {
	            if (jsonDateString != null)
	                return new Date(parseInt(jsonDateString.replace('/Date(', '')));
	            else
	                return "";
	        }
	    };

	</script>
	<h2>USGS Search for Rhode Island</h2>

	<div class="container">
	    <input type="search" name="q" id="q" autocomplete="off" size="100" /> <button onclick="Search();">Search</button>
	</div>
	<br />
	<div class="container">
	    <div class="row">
	        <table id="searchResults" border="1"></table>
	    </div>
	</div>


### Adicionar FeaturesSearch.cs

Adicione uma classe que fornece a funcionalidade de pesquisa para seu aplicativo.

1. No Gerenciador de Soluções, clique com o botão direito do mouse em **SimpleSearchMVCApp** | **Adicionar** | **Novo Item** | **Código** | **Classe**.
2. Nomeie a classe como **FeaturesSearch**.
3. Substitua o código padrão pelo código a seguir.


	    using System;
	    using System.Configuration;
	    using Microsoft.Azure.Search;
	    using Microsoft.Azure.Search.Models;
	
	    namespace SimpleSearchMVCApp
	    {
	        public class FeaturesSearch
	        {
	            private static SearchServiceClient _searchClient;
	            private static SearchIndexClient _indexClient;
	
	            public static string errorMessage;
	
	            static FeaturesSearch()
	            {
	                try
	                {
	                    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
	                    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
	
	                    // Create an HTTP reference to the catalog index
	                    _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
	                    _indexClient = _searchClient.Indexes.GetClient("geonames");
	                }
	                catch (Exception e)
	                {
	                    errorMessage = e.Message.ToString();
	                }
	            }
	
	            public DocumentSearchResult Search(string searchText)
	            {
	                // Execute search based on query string
	                try
	                {
	                    SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
	                    return _indexClient.Documents.Search(searchText, sp);
	                }
	                catch (Exception ex)
	                {
	                    Console.WriteLine("Error querying index: {0}\r\n", ex.Message.ToString());
	                }
	                return null;
	            }
	
	        }
	    }


### Definir SimpleSearchMVCApp como o projeto de inicialização

Clique com o botão direito do mouse no projeto **SimpleSearchMVCApp** para defini-lo como o projeto de inicialização.

## Compilar e executar SimpleSearchMVCApp

Quando você compila e executa esse programa, você deve ver uma página da Web no seu navegador padrão, fornecendo uma caixa de pesquisa para acessar dados USGS armazenados no índice em seu serviço de Pesquisa do Azure.

![][8]

### Pesquisar em dados USGS

O conjunto de dados do USGS inclui registros relevantes para o estado de Rhode Island. Se você clicar em **Pesquisa** em uma caixa de pesquisa vazia, você obterá as 50 entradas principais, que é o padrão.

Inserir um termo de pesquisa dará ao mecanismo de pesquisa critérios para limitar tal pesquisa. Tente digitar um nome regional. *Roger Williams* foi o primeiro governador de Rhode Island. Vários parques, edifícios e escolas receberam seu nome em homenagem.

![][9]

Você também pode tentar essas consultas, adicionando ou removendo operadores ou frases para redefinir o escopo de sua pesquisa:

- Pawtucket OR Pembroke
- goose +cape -neck


## Próximas etapas

Este é o primeiro tutorial da Pesquisa do Azure com base em .NET e no conjunto de dados do USGS. Ao longo do tempo, ampliaremos este tutorial para demonstrar outros recursos de pesquisa que talvez você queira usar em suas soluções personalizadas.

Se você já tiver alguma experiência com a Pesquisa do Azure, use este exemplo como um trampolim para experimentar sugestões (consultas de preenchimento automático ou de digitação antecipada), filtros e navegação facetada. Você também pode melhorar a página de resultados da pesquisa adicionando contagens e documentos em lote para que os usuários possam percorrer os resultados.

Ainda não conhece a Pesquisa do Azure? Recomendamos os outros tutoriais para que você compreenda o que pode criar. Visite nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. Você também pode exibir os links em nossa [Lista de vídeos e Tutorial](search-video-demo-tutorial-list.md) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG

<!---HONumber=AcomDC_0211_2016-->