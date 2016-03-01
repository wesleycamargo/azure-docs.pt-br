<properties 
   pageTitle="Experimente a Pesquisa do Azure gratuitamente com o Serviço de Aplicativo do Azure | Microsoft Azure"
   description="Experimente a Pesquisa do Azure gratuitamente, por até uma hora, usando o modelo de Serviço de Aplicativo do Azure."
   services="search" 
   documentationCenter="" 
   authors="HeidiSteen" 
   manager="mblythe" 
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="search" 
   ms.date="02/21/2016"
   ms.author="heidist"/>

# Experimente a Pesquisa do Azure gratuitamente com o Serviço de Aplicativo do Azure

[Testar Serviço de Aplicativo do Azure](https://tryappservice.azure.com/) é uma sessão gratuita de uma hora baseada em navegador para testar os Serviços do Azure, incluindo a **Pesquisa do Azure**, sem necessidade de assinatura. O site fornece diversos modelos para sua escolha. Ao selecionar o modelo do ASP.NET que inclui a Pesquisa do Azure, você obtém acesso a um site da Web totalmente funcional, com o apoio de serviços que você selecionou.

Como parte da oferta **Testar Serviço de Aplicativo do Azure**, o serviço de Pesquisa do Azure já foi criado e está pronto para receber consultas de pesquisa. Você não pode carregar nem usar seus próprios dados ou índice, mas pode executar consultas e fazer várias alterações de código que reformulam a experiência do usuário.

Os dados de pesquisa da [USGS (United States Geological Survey)](http://geonames.usgs.gov/domestic/download_data.htm) consistem em cerca de três milhões de linhas de pontos de referência, locais históricos, construções e outros pontos de referência nos EUA.

## Introdução

Se você ainda não iniciou a sessão de 1 hora, siga estas etapas para começar.

1. Clique duas vezes em [https://tryappservice.azure.com](https://tryappservice.azure.com/) para abrir este site em uma nova janela do navegador.
2. Role para baixo para selecionar **Aplicativo Web**. 
2. Clique em **Próximo**.
3. Escolha o modelo **ASP.NET + Site da Pesquisa do Azure**.

    ![][1]

4. Clique em **Criar**.
5. Escolha um método de logon e forneça o nome de usuário e senha.

    ![][2]

6. Aguarde enquanto o site é configurado. Quando estiver pronto, você verá uma página semelhante a esta. Cada página mostra um relógio em execução para que você sempre saiba quanto tempo resta.

    ![][3]

7. Escolha **Editar com o Visual Studio Team Services** para exibir a solução e navegar no site.
9. No Visual Studio Team Services, expanda as opções de sessão na parte superior da página e clique em **Procurar Site da Web**.

    ![][4]

10. Você deve ver a página de introdução para o site da Web da Pesquisa do Azure. Clique no botão **Introdução** para abrir o site.

    ![][5]

11. Um site da Web ASP.NET é aberto no navegador, fornecendo uma caixa de pesquisa. Digite um termo conhecido de pesquisa, como *Yellowstone*, ou uma montanha famosa, como *Mount Rainier*. Começar com um ponto de referência familiar facilita a avaliação dos resultados.

    ![][6]


## O que fazer primeiro

Como o índice de pesquisa é totalmente operacional, uma boa primeira etapa é testar algumas consultas. A Pesquisa do Azure dá suporte a todos os operadores de pesquisa padrão (+, -, |), aspas para correspondências literais, curinga (*) e operadores de precedência. Você pode examinar a referência de sintaxe de consulta para a lista completa dos operadores.

- Comece com uma pesquisa curinga adicionando um asterisco (`*`). Isso informa quantos documentos são encontrados no índice: 2,262,578.
- Em seguida, digite "Yellowstone" e adicione "+centro", "+edifício" e "-ND" para restringir progressivamente os resultados da pesquisa apenas para os centros de visitantes de Yellowstone, exceto aqueles na Dakota do Norte: `Yellowstone +center +building -ND`.  
- Tente uma frase que combina os operadores de precedência e a correspondência de cadeia de caracteres: `statue+(park+MT)`. Você verá resultados semelhantes à captura de tela abaixo. Observe que as categorias de faceta aparecem na classe de recurso, oferecendo filtragem autodirigida por meio de navegação mista, um recurso comumente encontrado na maioria dos aplicativos de pesquisa.

    ![][7]

Pronto para prosseguir? Vamos alterar algumas linhas do código para ver o impacto nas operações de pesquisa de texto completo.

## Alterar searchMode.All

A Pesquisa do Azure tem uma propriedade **searchMode** configurável que você pode usar para controlar o comportamento do operador de pesquisa. Os valores válidos para essa propriedade são `Any` (padrão) ou `All`. Consulte a [Sintaxe de Consulta Simples](https://msdn.microsoft.com/library/dn798920.aspx) para obter instruções sobre como definir essas opções.

- **searchMode.Any** estipula que qualquer correspondência em um termo de pesquisa é suficiente para incluir um item nos resultados da pesquisa. Se a frase de pesquisa for `Yellowstone visitor center`, então qualquer documento que contenha esses termos será incluído nos resultados da pesquisa. Esse modo é mais adequado para *recall*.
- **searchModel.All**, usado neste exemplo, requer que todos os termos especificados estejam presentes no documento. Esse modo é mais restritivo que o **searchMode.Any**, mas se você preferir *precisão* a recall, provavelmente é a escolha certa para o seu aplicativo. 

> [AZURE.NOTE] **searchMode.Any** funciona melhor quando a construção de consulta consiste principalmente de frases, com uso mínimo de operadores. Uma regra geral é que as pessoas procurando aplicativos de consumidor, como sites de comércio eletrônico, tendem a usar apenas termos, enquanto as pessoas que pesquisam conteúdo ou dados têm mais probabilidade de incluir operadores na frase de pesquisa. Se você acredita que as pesquisas provavelmente incluem operadores, principalmente o operador `NOT (-)`, comece com **searchModel.All**. Por outro lado, sua outra escolha, **searchMode.Any**, aplicará `OR` ao operador `NOT` com outros termos de pesquisa, o que pode expandir consideravelmente os resultados, em vez de reduzi-los. O exemplo a seguir pode ajudá-lo a entender a diferença.

Nesta tarefa, você alterará o **searchMode** e comparará os resultados de pesquisa com base no modo.

1. Abra a janela do navegador que contém o aplicativo de exemplo e escolha **Conectar ao Visual Studio Team Services**.

    ![][8]

2. Abra **Search.cshtml**, localize `searchMode.All` na linha 39 e altere para `searchMode.Any`.

    ![][9]

3. Na barra de salto à direita, clique em **Executar**.

    ![][10]
 
Na janela do aplicativo recompilado, insira um termo de pesquisa que você usou antes, como `Yellowstone +center +building -ND`, e compare os resultados de antes e depois das alterações no **searchMode**.

É uma grande diferença. Em vez de sete resultados da pesquisa, você tem mais de dois milhões.

   ![][11]
 
O comportamento que você observou é devido à inclusão do operador `NOT` (neste caso, "-ND"), que aplica *OU* em vez de *E* quando **searchMode** é definido como `Any`.

Dada essa configuração, os resultados da pesquisa incluem ocorrências para os termos de pesquisa `Yellowstone`, `center` e `building`, mas também todos os documentos que são `NOT North Dakota`. Como só há 13.081 documentos contendo a frase `North Dakota`, quase todo o conjunto de dados é retornado.

De fato, esse é talvez um cenário improvável, mas ilustra os efeitos do **searchMode** em frases de pesquisa que incluem o operador `NOT`, portanto, é útil entender por que o comportamento ocorre e como alterá-lo se isso não for o que você deseja.

Para continuar este tutorial, reverta **searchMode** ao seu valor inicial (definido como `All` na linha 39), execute o programa e use o aplicativo recompilado para as tarefas restantes.
 
## Adicionar um filtro global para o estado de Washington

Normalmente, se você quisesse pesquisar em um subconjunto dos dados disponíveis, você definiria o filtro na fonte de dados ao importar dados. Para fins de aprendizagem, trabalhando com dados somente leitura, vamos definir o filtro em nosso aplicativo para retornar apenas os documentos que incluem o estado de Washington.

1. Abra Search.cshtml, localize o bloco de código **SearchParameters** (começando na linha 36) e adicione uma linha de comentário e um filtro.

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


Os filtros são especificados usando a sintaxe de OData e são frequentemente usados com navegação mistas ou incluídos na cadeia de caracteres de consulta para restringir a consulta. Consulte [Sintaxe de Filtro OData](https://msdn.microsoft.com/library/azure/dn798921.aspx) para saber mais.

2. Clique em **Executar**.

3. Abra o aplicativo.

4. Digite o caractere curinga (*) para retornar uma contagem. Observe que os resultados agora estão limitados a 42.411 itens, que são todos os documentos para todos os recursos geográficos no estado de Washington.

   ![][12]

## Adicionar realce de ocorrências

Agora que você fez uma série de alterações de uma linha de código, você talvez queira modificações mais abrangentes e que requerem alterações de código em vários lugares. A seguinte versão do **Search.cshtml** pode ser colada diretamente no arquivo Search.cshtml na sessão atual.

O código a seguir adiciona o realce de ocorrências. Observe as novas propriedades declaradas no [SearchParameters](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchparameters_properties.aspx). Também é uma nova função que itera na coleção de resultados para obter os documentos que precisam de realce, além de HTML que processa o resultado.

Quando você executa este exemplo de código, pesquise entradas de termo que têm uma correspondência nos campos especificados e que são realçadas em negrito.

   ![][14]

Você pode salvar uma cópia do arquivo **Search.cshtml** original para ver como as duas versões se comparam.

> [AZURE.NOTE] Comentários serão reduzidos para o tamanho do arquivo.
 
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

## Por que escolher a Pesquisa do Azure?

Embora outros serviços do Azure, como a pesquisa de texto completo da oferta de banco de dados SQL, um serviço como a Pesquisa do Azure fornece a você controle, paginação e contagens de ajuste, realce de ocorrências, sugestões de consulta de preenchimento automático, suporte de linguagem natural, navegação mistas, filtragem e muito mais. Como vários dos nossos [exemplos](https://github.com/Azure-Samples?utf8=%E2%9C%93&query=search) demonstram, é possível desenvolver um aplicativo completo baseado em pesquisa usando apenas a Pesquisa do Azure e o ASP.NET.

## Próximas etapas

Usando o serviço de somente leitura fornecido no site [Experimentar o Serviço de Aplicativo do Azure](https://tryappservice.azure.com/), você viu a sintaxe de consulta e pesquisa de texto completo em ação, aprendeu sobre searchMode e filtros e adicionou realce de ocorrências ao aplicativo de pesquisa. Como próxima etapa, considere ir para criação e atualização de índices. Isso adiciona a capacidade de:

- [Definir perfis de pontuação](https://msdn.microsoft.com/library/dn798928.aspx) usados para ajustar as pontuações de pesquisa para que os itens de alto valor sejam exibidos primeiro.
- [Definir sugestores](https://msdn.microsoft.com/library/mt131377.aspx) que adicionem sugestões de consulta de preenchimento automático em resposta à entrada do usuário.
- [Definir indexadores](https://msdn.microsoft.com/library/dn946891.aspx) que atualizem o índice automaticamente sempre que a fonte de dados for o Banco de Dados SQL do Azure ou o Banco de Dados de Documentos do Azure.

Para executar todas essas tarefas, você terá uma assinatura do Azure para que possa criar e preencher índices em um serviço. Para saber mais sobre como se inscrever para uma avaliação gratuita, visite [https://azure.microsoft.com/pt-br/pricing/free-trial/](https://azure.microsoft.com/pricing/free-trial/).

Para saber mais sobre a Pesquisa do Azure, visite nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) em [http://azure.microsoft.com](https://azure.microsoft.com/) ou confira a os [exemplos e vídeos](search-video-demo-tutorial-list.md) que exploram a funcionalidade de intervalo completo de Pesquisa de Azure.

## Fatos sobre o código e a Pesquisa do Azure

A Pesquisa do Azure é um serviço de Plataforma como Serviço [PaaS](https://en.wikipedia.org/wiki/Platform_as_a_service) totalmente gerenciada que facilita para os desenvolvedores a integração entre excelentes experiências de pesquisa na Web e aplicativos móveis. Os dados usados em operações de pesquisa são armazenados com o serviço de pesquisa no Azure, e a proximidade dos dados com as operações garante baixa latência e comportamentos de pesquisa consistente. Detalhando isso um pouco mais:

- Os dados pesquisáveis são armazenados em um índice gerenciado pela Pesquisa do Azure.
- O esquema que define o índice é definido pelo usuário e especificará os campos de pesquisa, os campos não pesquisáveis que pode ser úteis em uma expressão de filtro e construções, como perfis de pontuação para resultados de ajuste.
- Um índice de pesquisa contém um ou mais documentos (que são semelhantes a uma linha em uma tabela) que são pesquisáveis e recuperáveis.
- A maioria dos índices são carregados de um único conjunto de dados preparado antecipadamente por você para incluir apenas os campos que são úteis no contexto de operações de pesquisa. 
- Os dados podem ser carregados automaticamente por um indexador (com suporte somente para o Banco de Dados SQL ou o Banco de Dados de Documentos do Azure) ou enviados para um índice de pesquisa por meio de APIs da Pesquisa do Azure. Quando você usa a API, você pode enviar dados de qualquer fonte de dados, contanto que ela esteja no formato JSON.

Na opção [Serviço de Aplicativo do Azure](https://tryappservice.azure.com/), o modelo "ASP.NET + Site da Pesquisa do Azure" fornece o código-fonte para o aplicativo Web, modificável no Visual Studio Team Services (disponível como parte da sessão de uma hora). Nenhuma ferramenta de desenvolvimento separada é necessária para exibir ou alterar o código.

O código é escrito em C#, usando a [Biblioteca de cliente .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/dn951165.aspx) para executar consultas no índice, fornecer faceted navigation e exibir contagens e resultados da pesquisa em uma página da Web.

Outro código, não incluso no modelo, foi usado para criar e carregar o índice de pesquisa USGS. Como o serviço é somente leitura, todas as operações que exigem acesso de gravação precisaram ser concluídas com antecedência. Você pode ver uma [cópia do esquema](#schema) usado para criar o esquema ao final deste artigo.

<a name="Schema"></a>
## Sobre o esquema

A captura de tela a seguir mostra o esquema usado para criar o índice usado neste modelo.
 
   ![][13]

### Arquivo Schema.json

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

<!---HONumber=AcomDC_0224_2016-->