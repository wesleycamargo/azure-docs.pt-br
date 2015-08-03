<properties
	pageTitle="Introdução ao seu primeiro aplicativo de Pesquisa do Azure em .NET | Microsoft Azure"
	description="Tutorial sobre como criar uma solução usando a biblioteca de cliente .NET no SDK .NET da Pesquisa do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="hero-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

#Introdução ao seu primeiro aplicativo de Pesquisa do Azure em .NET#

Aprenda como criar um aplicativo personalizado de pesquisa .Net no Visual Studio 2013 ou posterior, que use a Pesquisa do Azure para sua experiência de pesquisa. O tutorial usa o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx) para criar classes para os objetos e operações usados neste exercício, assim como a API REST do serviço de Pesquisa do Azure.

Para executar esse exemplo, você deve ter um serviço de Pesquisa do Azure, no qual você pode se inscrever pelo [Portal do Azure](https://portal.azure.com).

> [AZURE.TIP]Baixe o código-fonte deste tutorial em [Exemplos do .NET da Pesquisa do Azure](http://go.microsoft.com/fwlink/p/?LinkId=530196).

##Sobre os dados##

Este exemplo de aplicativo usa dados do [Serviço Geológico dos Estados Unidos (USGS)](http://geonames.usgs.gov/domestic/download_data.htm), filtrados no estado de Rhode Island, para reduzir o tamanho do conjunto de dados. Vamos usar esses dados para criar um aplicativo de pesquisa que retorna prédios de referência, por exemplo, hospitais e escolas, e características geológicas, como rios, lagos e picos.

Neste aplicativo, o programa **DataIndexer** cria e carrega o índice usando um constructo [Indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx), recuperando o conjunto de dados filtrado do USGS de um Banco de Dados SQL do Azure público. As informações de credenciais e de conexão para a fonte de dados online são fornecidas no código do programa. Nenhuma configuração adicional é necessária.

> [AZURE.NOTE]Aplicamos um filtro a esse conjunto de dados para permanecer abaixo do limite de 10.000 documentos da camada de preços gratuita. Se você usar a camada padrão, esse limite não se aplicará. Para obter detalhes sobre a capacidade de cada camada de preços, confira [Limites e restrições](https://msdn.microsoft.com/library/azure/dn798934.aspx).

##Criar o serviço##

1. Entre no [Portal do Azure](https://portal.azure.com).

2. Na barra de navegação rápida, clique em **Novo** | **Dados + armazenamento** | **Pesquisa**.

     ![][1]

3. Configure o nome do serviço, camada de preços, grupo de recursos, assinatura e local. Essas configurações são necessárias e não podem ser alteradas após o início do fornecimento do serviço.

     ![][2]

	- O **nome do serviço** deve ser exclusivo, com letras minúsculas, menos de 15 caracteres e sem espaços. Esse nome se torna parte do ponto de extremidade de seu serviço de Pesquisa do Azure. Confira [Regras de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para saber mais sobre as convenções de nomenclatura.

	- A **Camada de preços** determina a capacidade e a cobrança. Ambas as camadas fornecem os mesmos recursos, mas em diferentes níveis de recursos.

		- Um serviço na camada **Gratuito** é executado em clusters compartilhados com outros assinantes. Essa camada oferece capacidade suficiente para testar tutoriais e escrever código de prova de conceito, mas não deve ser utilizada para aplicativos de produção. A implantação de um serviço gratuito geralmente demora apenas alguns minutos.
		- Um serviço na camada **Padrão** é executado em recursos dedicados e é altamente escalonável. Inicialmente, um serviço padrão é configurado com uma réplica e uma partição, mas você pode ajustar a capacidade após o serviço ser criado. A implantação de um serviço padrão demora mais tempo, normalmente cerca de quinze minutos.

	- **Grupos de recursos** são contêineres para serviços e contêineres usados para um fim comum. Por exemplo, se for compilar um aplicativo de pesquisa personalizado baseado na Pesquisa do Azure, Sites do Azure e armazenamento em Blob do Azure, você pode criar um grupo de recursos que mantém esses serviços juntos nas páginas de gerenciamento do portal.

	- **Assinatura** permite que você escolha entre várias assinaturas, se você tiver mais de uma.

	- **Local** é a região do data center. Atualmente, todos os recursos devem ser executados no mesmo data center. Não há suporte para a distribuição de recursos em vários data centers.

4. Clique em **Criar** para provisionar o serviço.

Observe as notificações na barra de navegação rápida. Um aviso é exibido quando o serviço fica pronto para uso.

<a id="sub-2"></a>
##Localizar o nome do serviço e chaves de Api do serviço de Pesquisa do Azure

Depois que o serviço for criado, você poderá retornar ao portal para obter a URL ou `api-key`. As conexões com o serviço de Pesquisa exigem que você tenha a URL e a `api-key` para autenticar a chamada.

1. Na barra de navegação rápida, clique em **Home** e clique no serviço de Pesquisa para abrir o painel do serviço.

2. No painel de serviço, você verá blocos com as informações essenciais e o ícone de chave para acessar as chaves de administrador.

  	![][3]

3. Copie a URL do serviço e uma chave de administrador. Você precisará deles posteriormente, quando você adicioná-los aos arquivos app.config e web.config em seus projetos do Visual Studio.

##Iniciar um novo projeto e solução##

Essa solução inclui dois projetos:

- **DataIndexer**, um aplicativo de console Visual C#, usado para carregar dados
- **SimpleSearchMVCApp**, um aplicativo Web MVC ASP.NET Visual C#, usado para consultar e retornar os resultados da pesquisa.

Nesta etapa, você criará ambos estes projetos.

1. Comece em **Visual Studio** | **Novo Projeto** | **Visual C#** | **Aplicativo de Console**.
2. Nomeie o projeto **DataIndexer** e, em seguida, nomeie a solução como **AzureSearchDotNetDemo**.
3. No Gerenciador de Soluções, em solução, clique com o botão direito em **Adicionar** | **Novo projeto** | **Visual C#** | **Aplicativo Web ASP .NET**.
4. Nomeie o projeto **SimpleSearchMVCApp**.
5. No novo projeto ASP.NET, escolha o modelo MVC e limpe as opções para evitar a criação de artefatos de programa que você não usará neste tutorial.

   Desmarque as caixas de seleção para hospedagem do Azure e testes de unidade e defina Autenticação como nenhuma.

   ![][10]

Quando terminar de criar os projetos, sua solução deve ser semelhante ao exemplo abaixo.

   ![][4]

##Instalar a biblioteca de cliente .NET e atualizar outros pacotes

1. No Gerenciador de Soluções, clique com o botão direito do mouse na solução e, então, clique em **Gerenciar Pacotes NuGet**.
2. Especifique **Atualizações** | **Apenas estáveis** | **Atualizar todas**.

   ![][11]

3. Aceite as instalações de pacotes adicionais para que todas as dependências também sejam instaladas.

4. Em seguida, instale a biblioteca de cliente .NET de Pesquisa do Azure. Certifique-se de especificar a pesquisa corretamente ou você não encontrará facilmente o pacote. Clique em com o botão direito e selecione **Gerenciar Pacotes do NuGet** novamente.

5. Especifique **Online** | **nuget.org** | **Incluir Pré-lançamento**, procure *azure.search* e, em seguida, instale a biblioteca.

   ![][12]

Uma lista parcial dos assemblies usado neste exemplo é mostrada abaixo.

   ![][5]

##Adicionar uma referência de assembly para System.Configuration

**DataIndexer** usa **System.Configuration** para ler as definições de configuração em app.config.

1. Clique com o botão direito em **DataIndexer** | **Adicionar** | **Referência** | **Framework** | **System.Configuration**. Marque a caixa de seleção.
2. Clique em **OK**.

##Atualizar arquivos de configuração

Cada projeto inclui os arquivos de configuração que especificam o nome do serviço e a chave de api.

1. Em **DataIndexer**, substitua o App.config pelo exemplo a seguir, atualizando o [NOME DO SERVIÇO] e a [CHAVE DE SERVIÇO] com valores válidos para o seu serviço.

   O nome do serviço não é a URL completa. Por exemplo, se o ponto de extremidade de serviço de Pesquisa é *https://mysearchsrv.search.microsoft.net*, o nome de serviço que você inseriria em App.config é *mysearchsrv*.

	    <?xml version="1.0" encoding="utf-8"?>
	    <configuration>
	      <startup>
	         <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
	      </startup>
	      <appSettings>
	        <add key="SearchServiceName" value="[SERVICE NAME]" />
	        <add key="SearchServiceApiKey" value="[SERVICE KEY]" />
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
		        <bindingRedirect oldVersion="0.0.0.0-6.0.0.0" newVersion="6.0.0.0" />
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


##Modificar DataIndexer

Este programa é um aplicativo de console que se conecta a seu serviço de Pesquisa conforme especificado em app.config, cria o índice e, em seguida, carrega-o com o conjunto de dados de USGS armazenados em um Banco de Dados SQL do Azure.

Atualmente, a versão de pré-lançamento da biblioteca de cliente não dá suporte a indexadores, então utilizaremos a API REST para criar e usar um indexador para esta parte do tutorial.

Antes de executar este programa, você fará duas modificações:

- Adicione **AzureSearchhelper.cs**, usado ao chamar a API REST para lidar com conexões e serializar e desserializar solicitações e respostas JSON.

- Substitua **Program.cs**, usado para criar o índice, indexador, carregar os dados e gravar mensagens.

###Criar AzureSearchHelper.cs

O código que chama a API REST deve incluir uma classe que lida com conexões e a serialização e desserialização de solicitações e respostas JSON. Nos exemplos fornecidos com a Pesquisa do Azure, essa classe geralmente é chamada de **AzureSearchHelper.cs**. Você pode criar essa classe e adicioná-la a **DataIndexer**, usando o código a seguir.

1. No Gerenciador de Soluções, clique com botão direito em **DataIndexer** | **Adicionar** | **Novo Item** | **Código** | **Classe**.
2. Nomeie a classe **AzureSearchHelper**.
3. Substitua o código padrão pelo código a seguir.

		//Copyright 2015 Microsoft

		//Licensed under the Apache License, Version 2.0 (the "License");
		//you may not use this file except in compliance with the License.
		//You may obtain a copy of the License at

		//       http://www.apache.org/licenses/LICENSE-2.0

		//Unless required by applicable law or agreed to in writing, software
		//distributed under the License is distributed on an "AS IS" BASIS,
		//WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
		//See the License for the specific language governing permissions and
		//limitations under the License.

		using System;
		using System.Net.Http;
		using System.Text;
		using Newtonsoft.Json;
		using Newtonsoft.Json.Converters;
		using Newtonsoft.Json.Serialization;

		namespace DataIndexer
		{
		    public class AzureSearchHelper
		    {
		        public const string ApiVersionString = "api-version=2015-02-28";

		        private static readonly JsonSerializerSettings _jsonSettings;

		        static AzureSearchHelper()
		        {
		            _jsonSettings = new JsonSerializerSettings
		            {
		                Formatting = Formatting.Indented, // for readability, change to None for compactness
		                ContractResolver = new CamelCasePropertyNamesContractResolver(),
		                DateTimeZoneHandling = DateTimeZoneHandling.Utc
		            };

		            _jsonSettings.Converters.Add(new StringEnumConverter());
		        }

		        public static string SerializeJson(object value)
		        {
		            return JsonConvert.SerializeObject(value, _jsonSettings);
		        }

		        public static T DeserializeJson<T>(string json)
		        {
		            return JsonConvert.DeserializeObject<T>(json, _jsonSettings);
		        }

		        public static HttpResponseMessage SendSearchRequest(HttpClient client, HttpMethod method, Uri uri, string json = null)
		        {
		            UriBuilder builder = new UriBuilder(uri);
		            string separator = string.IsNullOrWhiteSpace(builder.Query) ? string.Empty : "&";
		            builder.Query = builder.Query.TrimStart('?') + separator + ApiVersionString;

		            var request = new HttpRequestMessage(method, builder.Uri);

		            if (json != null)
		            {
		                request.Content = new StringContent(json, Encoding.UTF8, "application/json");
		            }

		            return client.SendAsync(request).Result;
		        }

		        public static void EnsureSuccessfulSearchResponse(HttpResponseMessage response)
		        {
		            if (!response.IsSuccessStatusCode)
		            {
		                string error = response.Content == null ? null : response.Content.ReadAsStringAsync().Result;
		                throw new Exception("Search request failed: " + error);
		            }
		        }
		    }
		}



###Atualizar Program.cs

1. No Gerenciador de Soluções, abra **DataIndexer** | **Program.cs**
2. Substitua os conteúdos do Program.cs pelo código a seguir.

		using Microsoft.Azure;
		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using Microsoft.Spatial;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Text;
		using System.Threading;
		using System.Threading.Tasks;
		using System.Timers;

		namespace DataIndexer
		{
		    class Program
		    {
		        private static SearchServiceClient _searchClient;
		        private static SearchIndexClient _indexClient;

		        // This Sample shows how to delete, create, upload documents and query an index
		        static void Main(string[] args)
		        {
		            string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
		            string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

		            // Create an HTTP reference to the catalog index
		            _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
		            _indexClient = _searchClient.Indexes.GetClient("geonames");

		            Console.WriteLine("{0}", "Deleting index...\n");
		            if (DeleteIndex())
		            {
		                Console.WriteLine("{0}", "Creating index...\n");
		                CreateIndex();
		                Console.WriteLine("{0}", "Sync documents from Azure SQL...\n");
		                SyncDataFromAzureSQL();
		            }
		            Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
		            Console.ReadKey();
		        }

		        private static bool DeleteIndex()
		        {
		            // Delete the index if it exists
		            try
		            {
		                _searchClient.Indexes.Delete("geonames");
		            }
		            catch (Exception ex)
		            {
		                Console.WriteLine("Error deleting index: {0}\r\n", ex.Message.ToString());
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
		                    Name = "geonames",
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
		                Console.WriteLine("Error creating index: {0}\r\n", ex.Message.ToString());
		            }

		        }

		        private static void SyncDataFromAzureSQL()
		        {
		            // This will use the Azure Search Indexer to synchronize data from Azure SQL to Azure Search
		            Uri _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
		            HttpClient _httpClient = new HttpClient();
		            _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);

		            Console.WriteLine("{0}", "Creating Data Source...\n");
		            Uri uri = new Uri(_serviceUri, "datasources/usgs-datasource");
		            string json = "{ 'name' : 'usgs-datasource','description' : 'USGS Dataset','type' : 'azuresql','credentials' : { 'connectionString' : 'Server=tcp:azs-playground.database.windows.net,1433;Database=usgs;User ID=reader;Password=EdrERBt3j6mZDP;Trusted_Connection=False;Encrypt=True;Connection Timeout=30;' },'container' : { 'name' : 'GeoNamesRI' }} ";
		            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating data source: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Creating Indexer...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer");
		            json = "{ 'name' : 'usgs-indexer','description' : 'USGS data indexer','dataSourceName' : 'usgs-datasource','targetIndexName' : 'geonames','parameters' : { 'maxFailedItems' : 10, 'maxFailedItemsPerBatch' : 5, 'base64EncodeKeys': false }}";
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Put, uri, json);
		            if (response.StatusCode != HttpStatusCode.Created && response.StatusCode != HttpStatusCode.NoContent)
		            {
		                Console.WriteLine("Error creating indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            Console.WriteLine("{0}", "Syncing data...\n");
		            uri = new Uri(_serviceUri, "indexers/usgs-indexer/run");
		            response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Post, uri);
		            if (response.StatusCode != HttpStatusCode.Accepted)
		            {
		                Console.WriteLine("Error running indexer: {0}", response.Content.ReadAsStringAsync().Result);
		                return;
		            }

		            bool running = true;
		            Console.WriteLine("{0}", "Synchronization running...\n");
		            while (running)
		            {
		                uri = new Uri(_serviceUri, "indexers/usgs-indexer/status");
		                response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
		                if (response.StatusCode != HttpStatusCode.OK)
		                {
		                    Console.WriteLine("Error polling for indexer status: {0}", response.Content.ReadAsStringAsync().Result);
		                    return;
		                }

		                var result = AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
		                if (result.lastResult != null)
		                {
		                    switch ((string)result.lastResult.status)
		                    {
		                        case "inProgress":
		                            Console.WriteLine("{0}", "Synchronization running...\n");
		                            Thread.Sleep(1000);
		                            break;

		                        case "success":
		                            running = false;
		                            Console.WriteLine("Synchronized {0} rows...\n", result.lastResult.itemsProcessed.Value);
		                            break;

		                        default:
		                            running = false;
		                            Console.WriteLine("Synchronization failed: {0}\n", result.lastResult.errorMessage);
		                            break;
		                    }
		                }
		            }
		        }
		    }
		}



##Compilar e executar DataIndexer

1. Clique com o botão direito no projeto **DataIndexer**.
2. Compile o projeto.
3. Pressione **F5** para executá-lo.

Você deverá ver uma janela de console com essas mensagens.

![][6]

No portal, você verá um novo índice **geonames**. Pode demorar vários minutos para que o portal atualize, portanto, talvez seja melhor esperar um pouco antes de verificar os resultados.

![][7]

##Alterar SimpleSearchMVCApp

**SimpleSearchMVC** é um aplicativo Web executado localmente no IIS Express. Ele fornece uma caixa de pesquisa e apresenta os resultados da pesquisa em uma tabela.

Antes de executar este programa, você fará três modificações:

- Substitua **HomeController.cs**, usado para aceitar a entrada do usuário. Neste exemplo, o termo de pesquisa é armazenado em uma variável chamada *q*.

- Substitua **index.cshtml**, as entradas de uma página da Web que fornece o termo de pesquisa e exibe os resultados da pesquisa.

- Adicione **FeatureSearch.cs**, uma classe que cria o cliente de pesquisa e executa a pesquisa.

###Atualizar HomeController.cs

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
	                Data = _featuresSearch.Search(q)
	            };
	        }


	    }
	}


###Atualizar Index.cshtml

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


###Adicionar FeaturesSearch.cs

Adicione uma classe que fornece a funcionalidade de pesquisa para seu aplicativo.

1. No Gerenciador de Soluções, clique com botão direito em **SimpleSearchMVCApp** | **Adicionar** | **Novo Item** | **Código** | **Classe**.
2. Nomeie a classe como **FeaturesSearch**.
3. Substitua o código padrão pelo código a seguir.

		using Microsoft.Azure.Search;
		using Microsoft.Azure.Search.Models;
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.Linq;
		using System.Web;

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

		        public DocumentSearchResponse Search(string searchText)
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

###Definir SimpleSearchMVCApp como o projeto de inicialização

Clique com o botão direito do mouse no projeto **SimpleSearchMVCApp** para defini-lo como o projeto de inicialização.

##Compilar e executar SimpleSearchMVCApp

Quando você compila e executa esse programa, você deve ver uma página da Web no seu navegador padrão, fornecendo uma caixa de pesquisa para acessar dados USGS armazenados no índice em seu serviço de Pesquisa do Azure.

![][8]

###Pesquisar em dados USGS

O conjunto de dados do USGS inclui registros relevantes para o estado de Rhode Island. Se você clicar em **Pesquisa** em uma caixa de pesquisa vazia, você obterá as 50 entradas principais, que é o padrão.

Inserir um termo de pesquisa dará ao mecanismo de pesquisa critérios para limitar tal pesquisa. Tente digitar um nome regional. *Roger Williams* foi o primeiro governador de Rhode Island. Vários parques, edifícios e escolas receberam seu nome em homenagem.

![][9]

Você também pode tentar essas consultas, adicionando ou removendo operadores ou frases para redefinir o escopo de sua pesquisa:

- Pawtucket OR Pembroke
- goose +cape -neck


##Próximas etapas##

Este é o primeiro tutorial da Pesquisa do Azure com base em .NET e no conjunto de dados do USGS. Ao longo do tempo, ampliaremos este tutorial para demonstrar outros recursos de pesquisa que talvez você queira usar em suas soluções personalizadas.

Se você já tiver alguma experiência com a Pesquisa do Azure, use este exemplo como um trampolim para experimentar sugestões (consultas de preenchimento automático ou de digitação antecipada), filtros e navegação facetada. Você também pode melhorar a página de resultados da pesquisa adicionando contagens e documentos em lote para que os usuários possam percorrer os resultados.

Ainda não conhece a Pesquisa do Azure? Recomendamos os outros tutoriais para que você compreenda o que pode criar. Visite nossa [página de documentação](http://azure.microsoft.com/documentation/services/search/) para encontrar mais recursos. Você também pode exibir os links em nossa [Lista de vídeos e Tutorial](https://msdn.microsoft.com/library/azure/dn798933.aspx) para acessar mais informações.

<!--Image references-->
[1]: ./media/search-get-started-dotnet/create-search-portal-1.PNG
[2]: ./media/search-get-started-dotnet/create-search-portal-2.PNG
[3]: ./media/search-get-started-dotnet/create-search-portal-3.PNG

[4]: ./media/search-get-started-dotnet/AzSearch-DotNet-VSSolutionExplorer.png
[5]: ./media/search-get-started-dotnet/AzSearch-DotNet-Assembly.png

[6]: ./media/search-get-started-dotnet/consolemessages.png
[7]: ./media/search-get-started-dotnet/portalindexstatus.png
[8]: ./media/search-get-started-dotnet/usgssearchbox.png
[9]: ./media/search-get-started-dotnet/rogerwilliamsschool.png

[10]: ./media/search-get-started-dotnet/AzSearch-DotNet-MVCOptions.PNG
[11]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-1.PNG
[12]: ./media/search-get-started-dotnet/AzSearch-DotNet-NuGet-2.PNG
 

<!---HONumber=July15_HO4-->