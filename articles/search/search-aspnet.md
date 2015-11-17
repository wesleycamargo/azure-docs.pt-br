<properties
	pageTitle="Conectar a Pesquisa do Azure aos aplicativos Web do MVC ASP.NET | Microsoft Azure | Serviço de pequisa hospedado na nuvem"
	description="Associe um aplicativo Web MVC ASP.NET à Pesquisa do Azure, um serviço de pesquisa hospedado na nuvem. Saiba como conectar, consultar e renderizar os resultados usando a biblioteca do .NET ou a API REST."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor="v-lincan"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

#Como integrar a Pesquisa do Azure aos Aplicativos Web MVC do ASP.NET

O MVC ASP.NET é a estrutura predominante de aplicativos Web em soluções personalizadas que são integradas à Pesquisa do Azure. Neste artigo, você aprenderá como conectar seu aplicativo Web do ASP.NET à Pesquisa do Azure, entender os padrões de design para operações comuns e analisar algumas práticas de codificação que podem ajudá-lo a ter uma experiência de desenvolvimento sem problemas.

##Exemplos e demonstrações que usam o ASP.NET e a Pesquisa do Azure

Já existem vários exemplos de código que mostram como a Pesquisa é integrada ao ASP.NET. Você pode ir diretamente ao código ou a um aplicativo de demonstração visitando um destes links:

- [Site de demonstração de trabalhos da Cidade de Nova Iorque (NYC)](http://aka.ms/azjobsdemo)
- [Experimente o Serviço de Aplicativo + Pesquisa do Azure](search-tryappservice.md)
- [Lista completa de vídeos, tutoriais, demonstrações e exemplos de código](earch-video-demo-tutorial-list.md)

##Conectar-se ao serviço

Para estabelecer uma conexão com as solicitações de serviço e de problemas, seu aplicativo Web precisa apenas de três coisas:

- Uma URL para o serviço de Pesquisa do Azure provisionado, formatada como https://<service-name>.search.windows.net
- Uma chave de API (cadeia de caracteres) que autentica a conexão com a Pesquisa do Azure
- Um HTTPClient ou SearchServiceClient para formular a solicitação de conexão

####URLs e chaves de API

Você pode encontrar a URL e a chave de API no [portal](search-create-service-portal.md) ou recuperá-las de forma programática usando a [API REST de Gerenciamento](https://msdn.microsoft.com/library/dn832684.aspx).

Normalmente, a URL e a chave são colocadas no arquivo web.config do seu programa de interação do usuário:

      <appSettings>
    	<add key="SearchServiceName" value="[SEARCH SERVICE NAME]" />
    	<add key="SearchServiceApiKey" value="[API KEY]" />
    	. . .
      </appSettings>

O nome do serviço de Pesquisa pode ser o nome curto que você especificou durante o provisionamento, contanto que acrescente o domínio (search.windows.net) na conexão, ou você pode especificar o nome totalmente qualificado (<service-name>.search.windows.net) no arquivo web.config, sem o prefixo HTTPS.

A chave de API é um token de autenticação gerado durante o provisionamento de serviço (somente chaves de administrador) ou gerado manualmente se você estiver criando chaves de consulta no portal. O tipo de chave determina quais operações de pesquisa estão disponíveis para o seu aplicativo:

- chaves de administrador (permissões de leitura/gravação, duas por serviço)
- chaves de consulta (somente leitura, até 50 por serviço)

As chaves de API são cadeias de caracteres com 32 caracteres. Visualmente, não há nenhuma distinção entre as chaves de administrador e de consulta. Caso as perca, veja que tipo de chave especificou no código; você precisa verificar o portal ou usar a API REST de Gerenciamento para retornar o tipo de chave. Para saber mais sobre chaves, visite [API REST do serviço de Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

> [AZURE.TIP]Uma chave de consulta fornece uma experiência somente leitura para o cliente. Consulte [TryAppService + Pesquisa do Azure](search-tryappservice.md) para testar as operações de Pesquisa do Azure que estão disponíveis em um serviço somente leitura. Vale lembrar que no TryAppService, o código do aplicativo Web pode ser totalmente modificado – você pode alterar qualquer parte do código C# no projeto do ASP.NET para modificar o layout da página da Web, pesquisar a construção de consulta ou pesquisar os resultados — apenas as operações de índice de serviço de Pesquisa do Azure e de carregamento de documento são somente leitura, de acordo com a inclusão de uma chave de API de consulta na conexão de serviço.

####Conexão do cliente

Os dois trechos de código a seguir configuram uma conexão com o serviço de Pesquisa usando a URL e a chave de API. Lembre-se de que o nome do serviço e as chaves de API são especificadas no arquivo web.config. Para as chamadas à REST, as chaves de administrador devem ser transmitidas no cabeçalho da solicitação, enquanto que as chaves de consulta podem ser transmitidas no cabeçalho ou diretamente na URL.

**[HttpClient](https://msdn.microsoft.com/library/system.net.http.httpclient.aspx) com chamadas à API REST**

    public class CatalogSearch
    {
        private static readonly Uri _serviceUri;
        private static HttpClient _httpClient;
        public static string errorMessage;

        static CatalogSearch()
        {
            try
            {
                _serviceUri = new Uri("https://" + ConfigurationManager.AppSettings["SearchServiceName"] + ".search.windows.net");
                _httpClient = new HttpClient();
                _httpClient.DefaultRequestHeaders.Add("api-key", ConfigurationManager.AppSettings["SearchServiceApiKey"]);
            }
            catch (Exception e)
            {
                errorMessage = e.Message;
            }
        }

**[SearchServiceClient](https://msdn.microsoft.com/library/azure/microsoft.azure.search.searchserviceclient.aspx) com .NET**

        static UsgsSearch()
        {
            try
            {
                string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
                string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];

                // Create an HTTP reference to the catalog index. Alternatively, include the index name in the query
                _searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
        }

##Padrões de design

Um aplicativo Web que é integrado à Pesquisa do Azure precisará formular consultas e renderizar os resultados. Esta seção fornece orientações sobre como estruturar o código para tarefas executadas em um programa que contém o código de interação do usuário. A definição de esquema, geração de índice e inclusão de dados são excluídos intencionalmente. Para obter orientações sobre como codificar essas operações, consulte as explicações passo a passo e os exemplos listados em [Vídeos, exemplos e tutoriais na Pesquisa do Azure](search-video-demo-tutorial-list.md).

###Formulação de consulta

Uma pesquisa de texto completo no índice é executada nos campos marcados como **isSearchable** no esquema que define o índice. Dada uma entrada de termo de pesquisa (representada abaixo pela cadeia de caracteres “q”), o mecanismo de pesquisa procura uma correspondência dentro de todos os campos pesquisáveis e retorna os resultados dos campos marcados como **isRetrievable**.

> [AZURE.NOTE]Embora a maioria dos campos provavelmente seja pesquisável, um índice pode conter campos usados somente em expressões de filtro; nesse caso, você os marcaria como não pesquisáveis para excluí-los da pesquisa de texto completo e não recuperáveis para excluí-los dos resultados da pesquisa.

Uma consulta de pesquisa envolve o termo de entrada fornecido pelo usuário em uma solicitação de Pesquisa que especifica o índice de destino, além dos parâmetros usados para filtrar ou refinar a solicitação. Os operadores incorporados na cadeia de caracteres de pesquisa, como +, - ou |, são manipulados automaticamente, o que significa que não existem requisitos de codificação para a análise de um termo de pesquisa. Qualquer análise é feita pelo mecanismo de pesquisa, como uma operação interna. Você pode supor que a cadeia de caracteres transmitida será analisada pelo mecanismo.

Uma consulta de pesquisa é fornecida em dois tipos: **Pesquisa** ou **Sugestões**. Você deve definir métodos separados para cada tipo de consulta. **Pesquisa** é a pesquisa de texto completo nos campos do índice. **Sugestões** é o recurso de consulta de preenchimento automático na Pesquisa do Azure que cria uma lista de possíveis termos de pesquisa com base nos três primeiros caracteres da entrada do usuário. Na maioria dos casos, você deve restringir **Sugestões** apenas aos campos que contenham valores relativamente exclusivos ou distintivos (como um nome de produto ou de publicação), em vez de a campos que contenham dados não diferenciados.

O trecho de código a seguir captura uma entrada de termo de pesquisa em um programa que usa a API REST. O termo de entrada é representado por uma cadeia de caracteres “q”, e os parâmetros restantes são usados para transmitir os valores de filtro de uma estrutura de navegação facetada na mesma página de pesquisa. O termo de entrada e os parâmetros de filtro são usados no método Pesquisa.

        public ActionResult Search(string q = "", string color = null, string category = null, double? priceFrom = null, double? priceTo = null, string sort = null)
        {
            dynamic result = null;

            // If blank search, assume they want to search everything
            if (string.IsNullOrWhiteSpace(q))
                q = "*";

            result = _catalogSearch.Search(q, sort, color, category, priceFrom, priceTo);
            ViewBag.searchString = q;
            ViewBag.color = color;
            ViewBag.category = category;
            ViewBag.priceFrom = priceFrom;
            ViewBag.priceTo = priceTo;
            ViewBag.sort = sort;

            return View("Index", result);
        }
O método **Search** que aceita essa consulta é definido da maneira que se segue. Observe que ele define os parâmetros na cadeia de caracteres de consulta, além da estrutura de navegação facetada (apoiada por meio de filtros que fazem o trabalho pesado de restringir os resultados da pesquisa) e da ordem de classificação.

        public dynamic Search(string searchText, string sort, string color, string category, double? priceFrom, double? priceTo)
        {
            string search = "&search=" + Uri.EscapeDataString(searchText);
            string facets = "&facet=color&facet=categoryName&facet=listPrice,values:10|25|100|500|1000|2500";
            string paging = "&$top=10";
            string filter = BuildFilter(color, category, priceFrom, priceTo);
            string orderby = BuildSort(sort);

            Uri uri = new Uri(_serviceUri, "/indexes/catalog/docs?$count=true" + search + facets + paging + filter + orderby);
            HttpResponseMessage response = AzureSearchHelper.SendSearchRequest(_httpClient, HttpMethod.Get, uri);
            AzureSearchHelper.EnsureSuccessfulSearchResponse(response);

            return AzureSearchHelper.DeserializeJson<dynamic>(response.Content.ReadAsStringAsync().Result);
        }

Um método do .NET que constrói uma cadeia de caracteres de pesquisa poderia ser colocado em uma exibição do MVC ou em um controlador. Essa função transmite a cadeia de caracteres para o controlador residencial. Ela também define uma estrutura de dados para os resultados.

    function Search() {

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

Um método do .NET para invocar **Search** pode ter a seguinte aparência, contido no programa C# principal que fornece a operação de conexão e pesquisa:

        public DocumentSearchResponse Search(string searchText)
        {
            // Execute search based on query string
            try
            {
                SearchParameters sp = new SearchParameters() { SearchMode = SearchMode.All };
                return _indexClient.Documents.Search(searchText, sp);
            }
            catch (Exception e)
            {
                errorMessage = e.Message.ToString();
            }
            return null;
        }


###Manipulando os resultados da pesquisa

Os resultados da pesquisa são retornados como um conjunto de linhas composto por campos que são marcados no esquema de índice como isRetrievable. Uma das maneiras mais simples renderizar um conjunto de resultados é usar o objeto do sistema ViewBag no MVC. O trecho de código a seguir é retirado de Index.cshtml do [projeto AdventureWorksDemo do CodePlex](https://azuresearchadventureworksdemo.codeplex.com/).

	@model dynamic
	
	@{
	    ViewBag.Title = "Search";
	}
	
	<h2>Product search</h2>
	
	@if (@ViewBag.errorMessage != null) {
	    @ViewBag.errorMessage
	} else {
	    <div class="container">
	        <form action="/Home/Search" method="get">
	            <input type="search" name="q" id="q" value="@ViewBag.searchString" autocomplete="off" size="100" /> <button type="submit">Search</button>
	            <input type="hidden" name="color" id="color" value="@ViewBag.color" />
	            <input type="hidden" name="category" id="category" value="@ViewBag.category" />
	            <input type="hidden" name="priceFrom" id="priceFrom" value="@ViewBag.priceFrom" />
	            <input type="hidden" name="priceTo" id="priceTo" value="@ViewBag.priceTo" />
	            <input type="hidden" name="sort" id="sort" value="@ViewBag.sort" />
	        </form>
	    </div>

###Navegação facetada

No mesmo arquivo Index.cshmtl, você pode encontrar o HTML usado para criar uma estrutura de navegação facetada que fornece classificações para a filtragem autodirigida, restringindo progressivamente os resultados da pesquisa por cor, preço ou categoria.

	    if (@Model != null)
	    {
	        <div class="container">
	            <div class="row">
	                <div class="col-md-4">
	                    Colors:
	                    <ul>
	                        @foreach (var colorFacet in Model["@search.facets"].color)
	                        {
	                            <li><a href="#" onclick="document.getElementById('color').value='@colorFacet.value'; 
	document.forms[0].submit(); 
	return false;">@colorFacet.value</a> (@colorFacet.count)</li>
	                        }
	                    </ul>
	                    Categories:
	                    <ul>
	                        @foreach (var categoryFacet in Model["@search.facets"].categoryName)
	                        {
	                            <li><a href="#" onclick="document.getElementById('category').value='@categoryFacet.value'; document.forms[0].submit(); return false;">@categoryFacet.value</a> (@categoryFacet.count)</li>
	                        }
	                    </ul>
	                    Prices:
	                    <ul>
	                        @foreach (var priceFacet in Model["@search.facets"].listPrice)
	                        {
	                            if (priceFacet.count > 0)
	                            {
	                       <li><a href="#" onclick="document.getElementById('priceFrom').value=@(priceFacet.from ?? 0); document.getElementById('priceTo').value=@(priceFacet.to ?? 0); 
	document.forms[0].submit(); return false;
	">@(priceFacet.from ?? 0) - @(priceFacet.to ?? "more")</a> (@priceFacet.count)</li>
	                            }
	                        }
	                    </ul>
	                </div>
	                <div class="col-md-8">
	                    <p>
	                        Sort -
	                        <a href="#" onclick="document.getElementById('sort').value=null; document.forms[0].submit(); return false;">by relevance</a>
	                        <a href="#" onclick="document.getElementById('sort').value='listPrice'; document.forms[0].submit(); return false;">by list price</a>
	                        <a href="#" onclick="document.getElementById('sort').value='color'; document.forms[0].submit(); return false;">by color</a>
	                    </p>
	                    <p>Found @Model["@odata.count"] products in the catalog</p>
	
	                    <ul>
	                        @foreach (var product in Model.value)
	                        {
	                            <li>
	                                <h3><b>@product.name</b></h3>
	                                price: @product.listPrice, color: @product.color, weight: @product.weight, size: @product.size
	                            </li>
	                        }
	                    </ul>
	                </div>
	            </div>
	        </div>
	    }
	}


###Realce de ocorrência

Aplicar um estilo à instância do termo de pesquisa em um resultado de pesquisa é chamado de realce de ocorrência. Na Pesquisa do Azure, realces de ocorrência são especificados na consulta, por meio do parâmetro de pesquisa de realce, para o qual você fornece uma lista delimitada por vírgulas de campos para verificar se há termos correspondentes. Você pode escolhe o estilo real aplicado. Os três trechos de código a seguir são retirados do [tutorial TryAppService + Pesquisa do Azure](search-tryappservice.md).

Primeiro, especifique os realces de ocorrência como um parâmetro de pesquisa e liste os campos para verificar se há termos correspondentes. Especifique o estilo HTML que será usado no realce de ocorrência.

	// Set the Search parameters used when executing the search request
	     var sp = new SearchParameters
	{
	// Include a count of results in the query result
	     IncludeTotalResultCount = true,
	// Limit the results to 20 documents
	     Top = 20,
	// Enable hit-highlighting
	     HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
	     HighlightPreTag = "<b>",
	     HighlightPostTag = "</b>",
	};

Depois, itere pelos resultados da pesquisa para encontrar a cadeia de caracteres que precisa ser realçada. private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)

	  {
	     if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
	      {
	      string highlightedResult = string.Join("...", item.Highlights[fieldName]);
	      return new HtmlString(highlightedResult);
	      }
	      return new HtmlString(item.Document[fieldName].ToString());
	   }

Por fim, forneça o layout dos resultados da pesquisa, especificando o conjunto de resultados que foi avaliado no trecho anterior.

	<div class="col-xs-12 col-sm-6 col-md-10">
	  <p style="padding-top:20px">1 - @response.Results.Count of @response.Count results for "@searchText"</p>
	
	  <ul class="list-unstyled">
	    <!-- Cycle through the search results -->
	   @foreach (var item in response.Results)
	    {
	     <li>
	       <h3>@RenderHitHighlightedString(item, "FEATURE_NAME")</h3>
	       <p>@RenderHitHighlightedString(item, "DESCRIPTION")</p>
	       <p>@RenderHitHighlightedString(item, "FEATURE_CLASS"), elevation: @item.Document["ELEV_IN_M"] meters</p>
	       <p>@RenderHitHighlightedString(item, "COUNTY_NAME") County, @RenderHitHighlightedString(item, "STATE_ALPHA")</p>
	       <br />
	     </li>
	    }
	  </ul>
	</div>


##Práticas comuns de codificação

Ainda não conhece o MVC, a programação do .NET ou as APIs REST? Essas seções oferecem algumas práticas de codificação para ajudá-lo a se familiarizar.

###Modelo do MVC

A tabela a seguir resume como os componentes do modelo MVC são usados em aplicativos que incluem a Pesquisa do Azure. Se estiver usando o MVC 4 ou MVC 5, o código que é integrado à Pesquisa do Azure geralmente será adicionado a esses módulos

Arquivo|Descrição
----|-----------
Web.config|Forneça a URL de serviço e a chave de API. Adicione uma referência a System.Configuration no módulo do programa principal para ler os valores.
Program.cs|No programa principal, defina um HttpClient ou SearchServiceClient para estabelecer uma conexão com o serviço. Adicione o método Pesquisa a esse programa.
DataModel|Não usado. Supondo que as operações de criação de índice e de carregamento de dados estejam em diferentes programas, nenhum modelo de dados é necessário para a Pesquisa do Azure no aplicativo Web.
Modos de exibição|Um modo de exibição contém o HTML da página da Web do aplicativo, que vai desde a entrada da caixa de pesquisa até o HTML dinâmico para manipular os resultados da pesquisa.
Controladores|A construção de consulta e o tratamento de erro geralmente são encontrados em HomeContoller.cs. No mínimo, o controlador deve incluir um método de pesquisa que recupere os resultados da Pesquisa do Azure e encaminhe o conjunto de resultados para o modo de exibição. 

Se você estiver usando sugestões para consultas de preenchimento automático, outra opção é incluir um método que retorne as consultas sugeridas, dependendo se o índice contém um valor correspondente à entrada do termo de pesquisa fornecida pelo usuário.

###Quando usar a biblioteca de clientes do .NET vs. API REST

Para aplicativos do ASP.NET, a biblioteca de clientes do .NET é considerada uma melhor opção, pois configura a conexão HTTP e manipula a serialização e desserialização de JSON para você, o que simplifica o código.

Em alguns casos, sua escolha de API pode ser determinada pela paridade de recursos entre as duas abordagens. Em geral, a [biblioteca de clientes do .NET](https://msdn.microsoft.com/library/azure/dn951165.aspx) e a [API REST de Serviço](https://msdn.microsoft.com/library/azure/dn798935.aspx) são intercambiáveis, contanto que as operações solicitadas sejam implementadas em ambas. No entanto, às vezes novos recursos aparecem primeiro na API REST como parte de uma versão de visualização e são adicionadas à biblioteca do .NET somente alguns meses mais tarde. Por exemplo, os indexadores, que são usados para automatizar as operações de carregamento de dados dos tipos de fonte de dados específicos, apareceram na API REST de visualização primeiro antes de aparecerem na biblioteca de clientes alguns meses mais tarde. Todas as restrições na implementação de recursos são descritas na documentação do recurso.

###Incluir AzureSearchHelper.cs na serialização e desserialização de JSON na API REST

Ao contrário da biblioteca do .NET que realiza esta etapa para você, as APIs REST de Serviço devem serializar e desserializar os documentos JSON na troca de solicitação-resposta com o serviço. JSON é o formato de carga para a transmissão de dados ao carregar ou atualizar documentos no índice.

O código para a serialização de JSON pode ser encontrado em vários exemplos, em um arquivo chamado **AzureSearchHelper.cs**:

	using System;
	using System.Net.Http;
	using System.Text;
	using Newtonsoft.Json;
	using Newtonsoft.Json.Converters;
	using Newtonsoft.Json.Serialization;
	
	namespace CatalogCommon
	{
	    public class AzureSearchHelper
	    {
	        public const string ApiVersionString = "api-version=2014-07-31-Preview";
	
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

###Organizar seu código

Dividir as cargas de trabalho em projetos autônomos dentro da mesma solução do Visual Studio proporciona mais flexibilidade na forma como projetar, manter e executar cada programa. Recomendamos três:

- Código de criação de índice
- Código de ingestão de dados
- Código de interação do usuário

Na Pesquisa do Azure, as operações de indexação e de documentos – como adicionar ou atualizar documentos ou executar consultas – são totalmente independentes entre si. Isso significa que é possível desacoplar o gerenciamento de índice do seu código de interação do usuário do ASP.NET que formula as solicitações de pesquisa e renderiza os resultados.

Na maioria dos nossos exemplos de código, o índice é criado e carregado em um projeto (mencionado como DataIndexer, CatalogIndexer ou DataCatalog em vários exemplos), enquanto o código que manipula as solicitações de pesquisa e as respostas é colocado em um projeto do aplicativo MVC do ASP.NET. Nos exemplos de código, é prático agrupar a criação de índice e o upload de documento em um projeto, mas o código de produção provavelmente isolaria essas operações. Quando um índice é criado, raramente ele é alterado (e dependendo da alteração, talvez precise ser recompilado), enquanto os documentos provavelmente serão atualizados com frequência.

Separar as cargas de trabalho oferece outras vantagens na forma de diferentes níveis de permissões para a Pesquisa do Azure (direitos de administrador completos versus direitos somente consulta), uso de diferentes linguagens de programação, dependências mais específicas por programa, além da capacidade de revisar os programas de forma independente ou criar vários aplicativos front-end que todos operam no índice compilado e mantido por um aplicativo central de indexação.

##Próximas etapas

Para ampliar seu entendimento sobre a Pesquisa do Azure e a integração do ASP.NET, visite os seguintes links:

- [Como usar a Pesquisa do Azure de um aplicativo .NET](search-howto-dotnet-sdk.md) 
- [Estudo de caso do desenvolvedor de Pesquisa do Azure](search-dev-case-study-whattopedia.md)
- [Fluxo de trabalho típico para o desenvolvimento da Pesquisa do Azure](search-workflow.md) 

<!---HONumber=Nov15_HO3-->