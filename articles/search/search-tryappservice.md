<properties 
   pageTitle="Experimente o Serviço de Aplicativo do Azure com a Pesquisa do Azure" 
   description="Teste a Pesquisa do Azure gratuitamente, por até uma hora, usando o modelo TryAzureAppService." 
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="07/13/2015"
   ms.author="heidist"/>

# Experimente o Serviço de Aplicativo do Azure com a Pesquisa do Azure

[Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/) é uma maneira nova e totalmente gratuita para testar serviços do Azure, inclusive a Pesquisa do Azure, por até uma hora com nenhuma assinatura do Azure necessária.

O site fornece diversos modelos para sua escolha. Quando você seleciona o modelo do ASP.NET que inclui a Pesquisa do Azure, você obtém uma hora de acesso a um site da Web totalmente funcional, com o apoio de serviços que você selecionou. Você não poderá atualizar ou excluir os dados gerenciados pela Pesquisa do Azure – mas você pode executar consultas e fazer quaisquer alterações de código que redimensionem a experiência do usuário. Se a sessão expirar antes de você ter terminado de explorar, você pode sempre começar outra sessão ou passar para uma assinatura completa ou de avaliação se sua meta é criar ou carregar um índice diretamente.

No site [Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/), a Pesquisa do Azure é parte do modelo de aplicativo da Web – fornecendo uma experiência de pesquisa avançada e de texto completo juntamente com uma enorme quantidade de recursos centrados em pesquisa disponíveis apenas nesse serviço na plataforma do Azure.

Embora outros serviços do Azure, como a pesquisa de texto completo da oferta de banco de dados SQL, um serviço como a Pesquisa do Azure fornece a você controle, paginação e contagens de ajuste, realce de ocorrências, sugestões de consulta de preenchimento automático, suporte de linguagem natural, navegação mistas, filtragem e muito mais. Como vários dos nosso s [exemplos](https://github.com/AzureSearch) demonstram, é possível desenvolver um aplicativo completo de pesquisa baseada usando apenas a Pesquisa do Azure e ASP.NET.

Como parte da oferta [Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/), o serviço de Pesquisa do Azure que você usará é somente leitura – o que significa que você precisará usar o corpus de pesquisa fornecido na sessão. Você não pode carregar ou usar seu próprio índice ou dados. Os dados com os quais você trabalhará são provenientes da [United States Geological Survey (USGS)](), que consiste de linhas de cerca de 3 milhões de pontos de referência, locais históricos, edifícios e outros pontos de referência nos EUA.

Para ajudá-lo a tirar o máximo proveito de sua sessão de uma hora, as instruções a seguir guiarão você através de consultas e código.

Antes de continuar, talvez você queira reservar alguns minutos para examinar alguns pontos fundamentais sobre o código, serviço e dados pesquisáveis. Ter um pouco de conhecimento poderá ser útil se você ainda não estiver familiarizado com a Pesquisa do Azure.

##Fatos sobre o código e a Pesquisa do Azure

A Pesquisa do Azure é uma oferta [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) de serviços e dados, que consiste de um serviço de pesquisa totalmente gerenciado, além de dados pesquisáveis que você carrega ao usar uma instância irrestrita da Pesquisa do Azure (ou seja, quando você não estiver usando a opção Experimentar Serviço de Aplicativo do Azure).

Os dados usados em operações de pesquisa são armazenados com o serviço de pesquisa no Azure, e a proximidade dos dados com as operações garante baixa latência e comportamentos de pesquisa consistente. Atualmente, não há nenhum suporte para armazenamento remoto ou offline de dados pesquisáveis. Detalhando isso um pouco mais:

- Os dados da pesquisa são armazenados em um índice gerenciado pela Pesquisa do Azure, preenchida por documentos, um documento por item pesquisável. 
- A maioria dos índices são carregados de um único conjunto de dados preparado antecipadamente por você para incluir apenas os campos que são úteis no contexto de operações de pesquisa. 
- O esquema que define o índice é definido pelo usuário e especificará os campos de pesquisa, os campos não pesquisáveis que pode ser úteis em uma expressão de filtro e construções, como perfis de pontuação para resultados de ajuste.
- Os dados podem ser carregados automaticamente por um indexador (com suporte somente para o Banco de Dados SQL ou o Banco de Dados de Documentos do Azure) ou enviados para um índice de pesquisa por meio de APIs da Pesquisa do Azure. Quando você usa a API, você pode enviar dados de qualquer fonte de dados, contanto que ela esteja no formato JSON.

Na opção [Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/), o modelo ASP.NET + Pesquisa do Azure fornece código-fonte para o aplicativo Web, que pode ser modificado no Visual Studio Online (disponível como parte da sessão de uma hora). Nenhuma ferramenta de desenvolvimento separada é necessária para exibir ou alterar o código.

O código é escrito em C#, usando a [biblioteca de cliente .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/dn951165.aspx) para executar consultas no índice, fornecer navegação mista e exibir contagens e resultados da pesquisa em uma página da Web.

Outro código, não incluso no modelo, foi usado para criar e carregar o índice de pesquisa USGS. Como o serviço é somente leitura, todas as operações que exigem acesso de gravação precisaram ser concluídas com antecedência. Você pode ver uma [cópia do esquema](#schema) usado para criar o esquema ao final deste artigo.

##Introdução

Se você ainda não iniciou a sessão de 1 hora, siga estas etapas para começar.

1. Vá para [https://tryappservice.azure.com](https://tryappservice.azure.com/) e role para baixo para selecionar **aplicativo Web**. 
2. Clique em **Próximo**.
3. Escolha o modelo **ASP.NET + Pesquisa do Azure**.

    ![][1]

4. Clique em **Criar**.
5. Escolha um método de logon e forneça o nome de usuário e senha.

    ![][2]

6. Aguarde enquanto o site é configurado. Quando estiver pronto, você verá uma página semelhante a esta. Cada página mostra um relógio em execução para que você sempre saiba quanto tempo resta.

    ![][3]

7. Escolha **Editar com o Visual Studio Online** para exibir a solução e navegar no site.
9. No Visual Studio Online, expanda as opções de sessão na parte superior da página e, em seguida, clique em **Procurar site da Web**.

    ![][4]

10. Você deve ver a página de introdução para o site da Web da Pesquisa do Azure. Clique no botão **Introdução** para abrir o site.

    ![][5]

11. Um site da Web ASP.NET é aberto no navegador, fornecendo uma caixa de pesquisa. Insira um termo familiar de pesquisa, como *Yellowstone* ou uma montanha conhecida como o *Monte Rainier*. Começar com um ponto de referência familiar facilita a avaliação dos resultados.

    ![][6]


##O que fazer primeiro
Como o índice de pesquisa é totalmente operacional, uma boa primeira etapa é testar algumas consultas. A Pesquisa do Azure dá suporte a todos os operadores de pesquisa padrão (+, -, |), aspas para correspondências literais, curinga (*) e operadores de precedência. Você pode examinar a referência de sintaxe de consulta para a lista completa dos operadores.

- Comece com uma pesquisa de curinga adicionando um asterisco (`*`). Isso informa quantos documentos são encontrados no índice: 2,262,578.
- Em seguida, digite "Yellowstone", e depois adicione "+centro", "+ criando" e "-ND" restringir progressivamente os resultados da pesquisa apenas para os centros de visitantes de Yellowstone, exceto aqueles na Dakota do Norte: `Yellowstone +center +building -ND`.  
- Tente uma frase que combina os operadores de precedência e a correspondência de cadeia de caracteres: `statue+(park+MT)`. Você verá resultados semelhantes à captura de tela abaixo. Observe que as categorias de faceta aparecem na classe de recurso, oferecendo filtragem autodirigida por meio de navegação mista, um recurso comumente encontrado na maioria dos aplicativos de pesquisa.

    ![][7]

Pronto para prosseguir? Vamos alterar algumas linhas do código para ver o impacto nas operações de pesquisa de texto completo.

##Alterar searchMode.All

A Pesquisa do Azure tem uma propriedade **searchMode** que você pode usar para controlar o comportamento do operador de pesquisa. Os valores válidos para essa propriedade são `Any`(padrão) ou `All`. Consulte a [sintaxe de consulta simples](https://msdn.microsoft.com/library/dn798920.aspx) para obter instruções sobre como definir essas opções.

- **searchMode.Any** estipula que qualquer correspondência em um termo de pesquisa é suficiente para incluir um item nos resultados da pesquisa. Se a frase for `Yellowstone visitor center`, então qualquer documento que contenha esses termos está incluído nos resultados da pesquisa. Esse modo é mais adequado para *recall*.
- **searchModel.All**, usado neste exemplo, requer que todos os termos especificados estejam presentes no documento. Esse modo é mais rígido do que **searchMode.Any**, mas se você preferir *precisão* em vez de recall, provavelmente é a escolha certa para seu aplicativo. 

> [AZURE.NOTE]**searchMode.Any** funciona melhor quando a construção de consulta consiste principalmente de frases, com uso mínimo de operadores. Uma regra geral é que as pessoas procurando aplicativos de consumidor, como sites de comércio eletrônico, tendem a usar apenas termos, enquanto as pessoas que pesquisam conteúdo ou dados têm mais probabilidade de incluir operadores na frase de pesquisa. Se você acha que pesquisas provavelmente incluem operadores, principalmente o operador `NOT (-)`, comece com **searchModel.All**. Por outro lado, sua outra escolha, **searchMode.Any** será `OR` o operador `NOT` com outros termos de pesquisa, o que pode expandir consideravelmente em vez diminuir os resultados. O exemplo a seguir pode ajudá-lo a entender a diferença.

Nesta tarefa, você alterará o **searchMode** e comparará os resultados de pesquisa com base no modo.

1. Abra a janela do navegador que contém o aplicativo de exemplo, escolha **Conectar ao Visual Studio Online**.

    ![][8]

2. Abra **Search.cshtml**, localize `searchMode.All` na linha 39 e altere para `searchMode.Any`.

    ![][9]

3. Na barra de salto à direita, clique em **Executar**.

    ![][10]
 
Na janela do aplicativo recriado, insira um termo de pesquisa que você usou antes, como `Yellowstone +center +building -ND` e compare os resultados de antes e depois das alterações no **searchMode**.

É uma grande diferença. Em vez de sete resultados da pesquisa, você tem mais de dois milhões.

   ![][11]
 
O comportamento que você observou é devido à inclusão do operador `NOT` (nesse caso, "-ND"), que é *OR'd* em vez de *AND'd* quando **searchMode** é definido como `Any`.

Dada essa configuração, os resultados da pesquisa incluem ocorrências para os termos de pesquisa `Yellowstone`, `center` e `building`, mas também todos os documentos que são `NOT North Dakota`. Como só há 13.081 documentos contendo a frase `North Dakota`, quase todos os conjunto de dados são retornados.

De fato, esse é talvez um cenário improvável, mas ilustra os efeitos de **searchMode** em frases de busca que incluem o operador `NOT`, portanto, é útil entender por que o comportamento ocorre e como alterá-lo se este não for o que você deseja.

Para continuar este tutorial, reverta **searchMode** para seu valor original (definido como `All` na linha 39), execute o programa e use o aplicativo recriado para as tarefas restantes.
 
##Adicionar um filtro global para o estado de Washington

Normalmente, se você quisesse pesquisar em um subconjunto dos dados disponíveis, você definiria o filtro na fonte de dados ao importar dados. Para fins de aprendizagem, trabalhando com dados somente leitura, vamos definir o filtro em nosso aplicativo para retornar apenas os documentos que incluem o estado de Washington.

1. Abra Search.cshtml, localize o bloco de código **SearchParameters** (começando na linha 36) e adicione uma linha de comentário mais um filtro.

        var sp = new SearchParameters
        {
            //Add a filter for _Washington State
            Filter = "STATE_ALPHA eq 'WA'",
            // Specify whether Any or All of the search terms 
            SearchMode = SearchMode.All,
            // Include a count of results in the query result
            IncludeTotalResultCount = true,
            // Return an aggregated count of feature classes based on the specified query
            Facets = new[] { "FEATURE_CLASS" },
            // Limit the results to 20 documents
            Top = 20
        };


Os filtros são especificados usando a sintaxe de OData e são frequentemente usados com navegação mistas ou incluídos na cadeia de caracteres de consulta para restringir a consulta. Consulte [sintaxe de filtro OData](https://msdn.microsoft.com/library/azure/dn798921.aspx) para obter mais informações.

2. Clique em **Executar**.

3. Abra o aplicativo.

4. Digite o caractere curinga (*) para retornar uma contagem. Observe que os resultados agora estão limitados a 42.411 itens, que são todos os documentos para todos os recursos geográficos no estado de Washington.

   ![][12]

##Adicionar realce de ocorrência

Agora que você fez uma série de alterações de uma linha de código, você talvez queira modificações mais abrangentes e que requerem alterações de código em vários lugares. A versão seguinte do **Search.cshtml** pode ser colada diretamente sobre o arquivo Search.cshtml na sessão atual.

O código a seguir adiciona o realce de ocorrências. Observe as novas propriedades declaradas no [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx). Também é uma nova função que itera na coleção de resultados para obter os documentos que precisam de realce, além de HTML que processa o resultado.

Quando você executa este exemplo de código, pesquise entradas de termo que têm uma correspondência nos campos especificados e que são realçadas em negrito.

   ![][14]

Talvez você queira salvar uma cópia do arquivo **Search.cshtml** original para ver como as duas versões se comparam.

> [AZURE.NOTE]Comentários serão reduzidos para o tamanho do arquivo.
 
    @using System.Collections.Specialized
    @using System.Configuration
    @using Microsoft.Azure.Search
    @using Microsoft.Azure.Search.Models
    
    @{
    Layout = "~/_SiteLayout.cshtml";
    }
    
    @{
    // This modified search.cshtml file adds hit-highlighting
    
    string searchText = Request.Unvalidated["q"];
    string filterExpression = Request.Unvalidated["filter"];
    
    DocumentSearchResponse response = null;
    if (!string.IsNullOrEmpty(searchText))
    {
    searchText = searchText.Trim();
    
    // Retrieve search service name and an api key from configuration
    string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
    string apiKey = ConfigurationManager.AppSettings["SearchServiceApiKey"];
    
    var searchClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));
    var indexClient = searchClient.Indexes.GetClient("geonames");
    
    // Set the Search parameters used when executing the search request
    var sp = new SearchParameters
    {
    // Specify whether Any or All of the search terms must be matched in order to count the document as a match.
    SearchMode = SearchMode.All,
    // Include a count of results in the query result
    IncludeTotalResultCount = true,
    // Return an aggregated count of feature classes based on the specified query
    Facets = new[] { "FEATURE_CLASS" },
    // Limit the results to 20 documents
    Top = 20,
    // Enable hit-highlighting
    HighlightFields = new[] { "FEATURE_NAME", "DESCRIPTION", "FEATURE_CLASS", "COUNTY_NAME", "STATE_ALPHA" },
    HighlightPreTag = "<b>",
    HighlightPostTag = "</b>",
    };
    
    if (!string.IsNullOrEmpty(filterExpression))
    {
    // Add a search filter that will limit the search terms based on a specified expression.
    // This is often used with facets so that when a user selects a facet, the query is re-executed,
    // but limited based on the chosen facet value to further refine results
    sp.Filter = filterExpression;
    }
    
    // Execute the search query based on the specified search text and search parameters
    response = indexClient.Documents.Search(searchText, sp);
    }
    }
    
    @functions
    {
    private string GetFacetQueryString(string facetKey, string facetValue)
    {
    NameValueCollection queryString = HttpUtility.ParseQueryString(Request.Url.Query);
    queryString["filter"] = string.Format("{0} eq '{1}'", HttpUtility.UrlEncode(facetKey), HttpUtility.UrlEncode(facetValue));
    return queryString.ToString();
    }
    
    private HtmlString RenderHitHighlightedString(SearchResult item, string fieldName)
    {
    if (item.Highlights != null && item.Highlights.ContainsKey(fieldName))
    {
    string highlightedResult = string.Join("...", item.Highlights[fieldName]);
    return new HtmlString(highlightedResult);
    }
    return new HtmlString(item.Document[fieldName].ToString());
    }
    }
    
    <!-- Form to allow user to enter search terms -->
    <form class="form-inline" role="search">
    <div class="form-group">
    <input type="search" name="q" id="q" autocomplete="off" size="80" placeholder="Type something to search, i.e. 'park'." class="form-control" value="@searchText" />
    <button type="submit" id="search" class="btn btn-primary">Search</button>
    </div>
    </form>
    @if (response != null)
    {
    if (response.Count == 0)
    {
    <p style="padding-top:20px">No results found for "@searchText"</p>
    <h3>Suggestions:</h3>
    <ul>
    <li>Ensure words are spelled correctly.</li>
    <li>Try rephrasing keywords or using synonyms.</li>
    <li>Try less specific keywords.</li>
    </ul>
    }
    else
    {
    <div class="col-xs-3 col-md-2">
    <h3>Feature Class</h3>
    <ul class="list-unstyled">
    @if (!string.IsNullOrEmpty(filterExpression))
    {
    <li><a href="?q=@HttpUtility.UrlEncode(searchText)">All</a></li>
    }
    <!-- Cycle through the facet results and show the values and counts -->
    @foreach (var facet in response.Facets["FEATURE_CLASS"])
    {
    <li><a href="?@GetFacetQueryString("FEATURE_CLASS", (string)facet.Value)">@facet.Value (@facet.Count)</a></li>
    }
    </ul>
    </div>
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
    }
    }


##Próximas etapas

Usando o serviço de somente leitura fornecido no site [Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/), você viu a sintaxe de consulta e pesquisa de texto completo em ação, aprendeu sobre searchMode e filtros e adicionou realce de ocorrências ao aplicativo de pesquisa. Como próxima etapa, considere ir para criação e atualização de índices. Isso adiciona a capacidade de:

- [Definir perfis de pontuação](https://msdn.microsoft.com/library/dn798928.aspx) usados para ajustar as pontuações de pesquisa para que os itens de alto valor sejam exibidos primeiramente.
- [Definir sugestões](https://msdn.microsoft.com/library/mt131377.aspx) que adicionam sugestões de preenchimento automático ou antes do tipo de consulta em resposta à entrada do usuário.
- [Definir indexadores](https://msdn.microsoft.com/library/dn946891.aspx) que atualizam o índice automaticamente sempre que a fonte de dados for o Banco de Dados SQL do Azure ou o Banco de Dados de Documentos do Azure.

Para executar todas essas tarefas, você terá uma assinatura do Azure para que possa criar e preencher índices em um serviço. Para obter mais informações sobre como se inscrever para uma avaliação gratuita, visite [https://azure.microsoft.com/pricing/free-trial](https://azure.microsoft.com/pricing/free-trial/).

Para saber mais sobre a Pesquisa do Azure, visite nossa [página de documentação](http://azure.microsoft.com/documentation/services/search/) em [http://azure.microsoft.com](http://azure.microsoft.com) ou confira a os [exemplos e vídeos](https://msdn.microsoft.com/library/dn818681.aspx) que exploram a funcionalidade de intervalo completo de Pesquisa de Azure.

<a name="Schema"></a>
##Sobre o esquema

A captura de tela a seguir mostra o esquema usado para criar o índice usado neste modelo.
 
   ![][13]

###Arquivo Schema.json

    {
      "@odata.context": "https://tryappservice.search.windows.net/$metadata#indexes/$entity",
      "name": "geonames",
      "fields": [
    {
      "name": "FEATURE_ID",
      "type": "Edm.String",
      "searchable": false,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": true,
      "analyzer": null
    },
    {
      "name": "FEATURE_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "FEATURE_CLASS",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_ALPHA",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "STATE_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "COUNTY_NUMERIC",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_M",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "ELEV_IN_FT",
      "type": "Edm.Int32",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "MAP_NAME",
      "type": "Edm.String",
      "searchable": true,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DESCRIPTION",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "HISTORY",
      "type": "Edm.String",
      "searchable": true,
      "filterable": false,
      "retrievable": true,
      "sortable": false,
      "facetable": false,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_CREATED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    },
    {
      "name": "DATE_EDITED",
      "type": "Edm.DateTimeOffset",
      "searchable": false,
      "filterable": true,
      "retrievable": true,
      "sortable": true,
      "facetable": true,
      "key": false,
      "analyzer": null
    }
      ],
      "scoringProfiles": [
    
      ],
      "defaultScoringProfile": null,
      "corsOptions": {
    "allowedOrigins": [
      "*"
    ],
    "maxAgeInSeconds": 300
      },
      "suggesters": [
    {
      "name": "AUTO_COMPLETE",
      "searchMode": "analyzingInfixMatching",
      "sourceFields": [
    "FEATURE_NAME",
    "FEATURE_CLASS",
    "COUNTY_NAME",
    "DESCRIPTION"
      ]
    }
      ]
    }


<!--Image references-->
[1]: ./media/search-tryappservice/AzSearch-TryAppService-TemplateTile.png
[2]: ./media/search-tryappservice/AzSearch-TryAppService-LoginAccount.png
[3]: ./media/search-tryappservice/AzSearch-TryAppService-AppCreated.png
[4]: ./media/search-tryappservice/AzSearch-TryAppService-BrowseSite.png
[5]: ./media/search-tryappservice/AzSearch-TryAppService-GetStarted.png
[6]: ./media/search-tryappservice/AzSearch-TryAppService-QueryWA.png
[7]: ./media/search-tryappservice/AzSearch-TryAppService-QueryPrecedence.png
[8]: ./media/search-tryappservice/AzSearch-TryAppService-VSOConnect.png
[9]: ./media/search-tryappservice/AzSearch-TryAppService-cSharpSample.png
[10]: ./media/search-tryappservice/AzSearch-TryAppService-RunBtn.png
[11]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeany.png
[12]: ./media/search-tryappservice/AzSearch-TryAppService-searchmodeWAState.png
[13]: ./media/search-tryappservice/AzSearch-TryAppService-Schema.png
[14]: ./media/search-tryappservice/AzSearch-TryAppService-HitHighlight.png

<!---HONumber=July15_HO3-->