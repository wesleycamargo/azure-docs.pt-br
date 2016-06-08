<properties
   pageTitle="Atualizando para o SDK do .NET da Pesquisa do Azure versão 1.1 | Microsoft Azure | Serviço de pesquisa de nuvem hospedada"
   description="Atualizando para o SDK do .NET da Pesquisa do Azure versão 1.1"
   services="search"
   documentationCenter=""
   authors="brjohnstmsft"
   manager="pablocas"
   editor=""/>

<tags
   ms.service="search"
   ms.devlang="dotnet"
   ms.workload="search"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.date="05/18/2016"
   ms.author="brjohnst"/>

# Atualizando para o SDK do .NET da Pesquisa do Azure versão 1.1

Se você estiver usando a versão 1.0.2-preview ou mais antiga do [SDK .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx), este artigo ajudará você a atualizar seu aplicativo a fim de usar a primeira versão disponível para o público geral, 1.1.

Para obter uma explicação mais geral do SDK, incluindo exemplos, confira [Como usar a Pesquisa do Azure de um aplicativo .NET](search-howto-dotnet-sdk.md).

A versão 1.1 do SDK .NET da Pesquisa do Azure contém várias alterações significativas de versões anteriores ao 1.0.0-preview (isso inclui as versões 0.13.0-preview e anteriores). A maioria das alterações é leve e, portanto, a alteração do seu código não deve exigir muito. Confira [Etapas da atualização](#UpgradeSteps) para obter instruções sobre como alterar o seu código para usar a nova versão do SDK.

<a name="WhatsNew"></a>
## O que há de novo na versão 1.1

A versão 1.1 destina-se à mesma versão de API REST que as versões mais antigas do SDK do .NET da Pesquisa do Azure (28-02-2015); portanto, não há nenhum recurso novo do serviço nesta versão. No entanto, há novos recursos de serialização do lado do cliente.

O SDK usa JSON.NET para serializar e desserializar documentos. A nova versão do SDK dá suporte à serialização personalizada por meio de `JsonConverter` e `IContractResolver` (confira a [documentação JSON.NET](http://www.newtonsoft.com/json/help/html/Introduction.htm) para obter mais detalhes). Isso pode ser útil quando você quer adaptar uma classe de modelo existente de seu aplicativo para usar com a Pesquisa do Azure e outros cenários mais avançados. Por exemplo, com a serialização personalizada, você pode:

 - Incluir ou excluir determinadas propriedades da sua classe de modelo para serem armazenadas como campos de documento.
 - Mapear os nomes de propriedade no código aos nomes de campo no índice.
 - Criar atributos personalizados que podem ser usados para mapear propriedades para campos de documento e para criar a definição de índice correspondente.

Você pode encontrar exemplos de implementação de serialização personalizada nos testes de unidade para o SDK .NET da Pesquisa do Azure no GitHub. Um bom ponto de partida é [esta pasta](https://github.com/Azure/azure-sdk-for-net/tree/AutoRest/src/Search/Search.Tests/Tests/Models). Ela contém classes que são usadas pelos testes de serialização personalizada.

Além da serialização personalizada, o novo SDK também oferece suporte à serialização de objetos `SearchContinuationToken`. Isso poderá ser útil se você chamar a Pesquisa do Azure de um aplicativo Web e precisar trocar tokens de continuação com um navegador ou cliente móvel ao paginar por meio dos resultados da pesquisa.

<a name="UpgradeSteps"></a>
## Etapas da atualização

Primeiro, atualize a sua referência NuGet para `Microsoft.Azure.Search` usando o Console do Gerenciador de Pacotes NuGet ou clicando com o botão direito do mouse em suas referências de projeto e selecionando "Gerenciar pacotes NuGet..." no Visual Studio.

Depois que o NuGet tiver baixado os novos pacotes e suas dependências, recompile o projeto.

Se você estava usando a versão 1.0.0-preview, 1.0.1-preview ou 1.0.2-preview anteriormente, o build deve ter êxito e você estará pronto para começar!

Se estava usando a versão 0.13.0-preview ou mais antiga anteriormente, você deve ver erros de build como o seguinte:

    Program.cs(137,56,137,62): error CS0117: 'Microsoft.Azure.Search.Models.IndexBatch' does not contain a definition for 'Create'
    Program.cs(137,99,137,105): error CS0117: 'Microsoft.Azure.Search.Models.IndexAction' does not contain a definition for 'Create'
    Program.cs(146,41,146,54): error CS1061: 'Microsoft.Azure.Search.IndexBatchException' does not contain a definition for 'IndexResponse' and no extension method 'IndexResponse' accepting a first argument of type 'Microsoft.Azure.Search.IndexBatchException' could be found (are you missing a using directive or an assembly reference?)
    Program.cs(163,13,163,42): error CS0246: The type or namespace name 'DocumentSearchResponse' could not be found (are you missing a using directive or an assembly reference?)

A próxima etapa é corrigir os erros de compilação individualmente. A maioria exigirá a alteração de alguns nomes de classe e de método que foram renomeados no SDK. A [Lista de alterações significativas na versão 1.1](#ListOfChanges) contém uma lista dessas alterações de nome.

Se estiver usando classes personalizadas para modelar documentos e essas classes tiverem propriedades de tipos primitivos não anuláveis (por exemplo, `int` ou `bool` em C#), haverá uma correção de bug na versão 1.1 do SDK da qual você deve estar ciente. Confira [Correções de bug na versão 1.1](#BugFixes) para obter mais detalhes.

Finalmente, depois de solucionar possíveis erros de compilação, você pode fazer alterações no seu aplicativo para tirar proveito da nova funcionalidade, se desejar. O recurso de serialização personalizada no novo SDK é detalhado em [Novidades na versão 1.1](#WhatsNew).

<a name="ListOfChanges"></a>
## Lista de alterações significativas na versão 1.1

A lista a seguir é ordenada pela probabilidade de a alteração afetar o seu código de aplicativo.

### Alterações de IndexBatch e IndexAction

`IndexBatch.Create` foi renomeado para `IndexBatch.New` e não tem mais um argumento `params`. Você pode usar `IndexBatch.New` para lotes que misturam tipos diferentes de ações (mesclagens, exclusões, etc.). Além disso, há novos métodos estáticos para a criação de lotes em que todas as ações são iguais: `Delete`, `Merge`, `MergeOrUpload` e `Upload`.

`IndexAction` não tem construtores públicos e suas propriedades agora são imutáveis. Você deve usar os novos métodos estáticos para criar ações para finalidades diferentes: `Delete`, `Merge`, `MergeOrUpload` e `Upload`. `IndexAction.Create` foram removidos. Se você usou a sobrecarga que utiliza apenas um documento, não deixe de usar `Upload`.

#### Exemplo

Se o seu código tiver esta aparência:

    var batch = IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc)));
    indexClient.Documents.Index(batch);

Você poderá alterá-lo para corrigir os erros de compilação:

    var batch = IndexBatch.New(documents.Select(doc => IndexAction.Upload(doc)));
    indexClient.Documents.Index(batch);

Se desejar, você pode simplificar ainda mais:

    var batch = IndexBatch.Upload(documents);
    indexClient.Documents.Index(batch);

### Alterações de IndexBatchException

A propriedade `IndexBatchException.IndexResponse` foi renomeada para `IndexingResults` e seu tipo agora é `IList<IndexingResult>`.

#### Exemplo

Se o seu código tiver esta aparência:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

Você poderá alterá-lo para corrigir os erros de compilação:

    catch (IndexBatchException e)
    {
        Console.WriteLine(
            "Failed to index some of the documents: {0}",
            String.Join(", ", e.IndexingResults.Where(r => !r.Succeeded).Select(r => r.Key)));
    }

<a name="OperationMethodChanges"></a>
### Alterações de método de operação

Cada operação no SDK .NET da Pesquisa do Azure é exposta como um conjunto de sobrecargas de método para chamadores síncronos e assíncronos. As assinaturas e a fatoração dessas sobrecargas de método mudaram na versão 1.1.

Por exemplo, a operação "Obter Estatísticas de Índice" em versões mais antigas do SDK expunham estas assinaturas:

Em `IIndexOperations`:

    // Asynchronous operation with all parameters
    Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        string indexName,
        CancellationToken cancellationToken);

Em `IndexOperationsExtensions`:

    // Asynchronous operation with only required parameters
    public static Task<IndexGetStatisticsResponse> GetStatisticsAsync(
        this IIndexOperations operations,
        string indexName);

    // Synchronous operation with only required parameters
    public static IndexGetStatisticsResponse GetStatistics(
        this IIndexOperations operations,
        string indexName);

As assinaturas de método para a mesma operação na versão 1.1 têm esta aparência:

Em `IIndexesOperations`:

    // Asynchronous operation with lower-level HTTP features exposed
    Task<AzureOperationResponse<IndexGetStatisticsResult>> GetStatisticsWithHttpMessagesAsync(
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        Dictionary<string, List<string>> customHeaders = null,
        CancellationToken cancellationToken = default(CancellationToken));

Em `IndexesOperationsExtensions`:

    // Simplified asynchronous operation
    public static Task<IndexGetStatisticsResult> GetStatisticsAsync(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions),
        CancellationToken cancellationToken = default(CancellationToken));

    // Simplified synchronous operation
    public static IndexGetStatisticsResult GetStatistics(
        this IIndexesOperations operations,
        string indexName,
        SearchRequestOptions searchRequestOptions = default(SearchRequestOptions));

O SDK do .NET da Pesquisa do Azure, a partir da versão 1.1, organiza os métodos de operação de maneira diferente:

 - Os parâmetros opcionais agora são modelados como padrão em vez de sobrecargas de método adicionais. Isso reduz o número de sobrecargas de método, às vezes drasticamente.
 - Os métodos de extensão agora ocultam muitos dos detalhes não essenciais de HTTP do chamador. Por exemplo, as versões mais antigas do SDK retornavam um objeto de resposta com um código de status HTTP, que normalmente não é verificado porque os métodos de operação lançam `CloudException` para qualquer código de status que indique um erro. Os novos métodos de extensão retornam apenas objetos de modelo, evitando que você tenha de desencapsulá-los em seu código.
 - Por outro lado, as principais interfaces agora expõem métodos que oferecem mais controle no nível de HTTP, se necessário. Agora, você pode passar a inclusão de cabeçalhos HTTP em solicitações e o novo tipo de retorno `AzureOperationResponse<T>` lhe dá acesso direto a `HttpRequestMessage` e a `HttpResponseMessage` para a operação. `AzureOperationResponse` é definido no namespace `Microsoft.Rest.Azure` e substitui `Hyak.Common.OperationResponse`.

### Alterações de ScoringParameters

Uma nova classe chamada `ScoringParameter` foi adicionada no SDK mais recente para facilitar o fornecimento de parâmetros para perfis de pontuação em uma consulta de pesquisa. A propriedade `ScoringProfiles` da classe `SearchParameters` foi tipada anteriormente como `IList<string>`; agora ela é tipada como `IList<ScoringParameter>`.

#### Exemplo

Se o seu código tiver esta aparência:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters = new[] { "featuredParam-featured", "mapCenterParam-" + lon + "," + lat };

Você poderá alterá-lo para corrigir os erros de compilação:

    var sp = new SearchParameters();
    sp.ScoringProfile = "jobsScoringFeatured";      // Use a scoring profile
    sp.ScoringParameters =
        new[]
        {
            new ScoringParameter("featuredParam", new[] { "featured" }),
            new ScoringParameter("mapCenterParam", GeographyPoint.Create(lat, lon))
        };

### Alterações no modelo de classe

Devido às alterações de assinatura descritas em [Alterações de método da operação](#OperationMethodChanges), muitas classes no namespace `Microsoft.Azure.Search.Models` foram renomeadas ou removidas. Por exemplo:

 - `IndexDefinitionResponse` foi substituído por `AzureOperationResponse<Index>`
 - `DocumentSearchResponse` foi renomeado para `DocumentSearchResult`
 - `IndexResult` foi renomeado para `IndexingResult`
 - `Documents.Count()` agora retorna `long` com a contagem de documentos em vez de `DocumentCountResponse`
 - `IndexGetStatisticsResponse` foi renomeado para `IndexGetStatisticsResult`
 - `IndexListResponse` foi renomeado para `IndexListResult`

Para resumir, as classes derivadas de `OperationResponse` que existiam somente para encapsular um objeto de modelo foram removidas. As classes restantes tinham sufixo alterado de `Response` para `Result`.

#### Exemplo

Se o seu código tiver esta aparência:

    IndexerGetStatusResponse statusResponse = null;

    try
    {
        statusResponse = _searchClient.Indexers.GetStatus(indexer.Name);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Error polling for indexer status: {0}", ex.Message);
        return;
    }

    IndexerExecutionResult lastResult = statusResponse.ExecutionInfo.LastResult;

Você poderá alterá-lo para corrigir os erros de compilação:

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

#### Classes de resposta e IEnumerable

Uma alteração adicional que pode afetar o seu código é que as classes de resposta que contêm coleções não implementam `IEnumerable<T>`. Em vez disso, você pode acessar diretamente a propriedade de coleção. Por exemplo, se o seu código tiver esta aparência:

    DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response)
    {
        Console.WriteLine(result.Document);
    }

Você poderá alterá-lo para corrigir os erros de compilação:

    DocumentSearchResult<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
    foreach (SearchResult<Hotel> result in response.Results)
    {
        Console.WriteLine(result.Document);
    }

#### Observação importante para aplicativos Web

Se você tiver um aplicativo Web que serializa `DocumentSearchResponse` diretamente para enviar os resultados da pesquisa ao navegador, precisará alterar o código ou os resultados não serão serializados corretamente. Por exemplo, se o seu código tiver esta aparência:

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

Você pode alterá-lo obtendo a propriedade `.Results` da resposta da pesquisa para corrigir a renderização dos resultados de pesquisa:

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

Você terá que procurar esses casos em seu código por conta própria; **o compilador não emitirá um aviso** porque `JsonResult.Data` é do tipo `object`.

### Alterações de CloudException

A classe `CloudException` foi movida do namespace `Hyak.Common` para o namespace `Microsoft.Rest.Azure`. Além disso, sua propriedade `Error` foi renomeada para `Body`.

### Alterações de SearchServiceClient e SearchIndexClient

O tipo da propriedade `Credentials` mudou de `SearchCredentials` para sua classe base, `ServiceClientCredentials`. Se você precisa acessar o `SearchCredentials` de um `SearchIndexClient` ou `SearchServiceClient`, use a nova propriedade `SearchCredentials`.

Em versões mais antigas do SDK, `SearchServiceClient` e `SearchIndexClient` tinham construtores que usavam o parâmetro `HttpClient`. Eles foram substituídos por construtores que usam um `HttpClientHandler` e uma matriz de objetos `DelegatingHandler`. Isso facilita a instalação de manipuladores personalizados para pré-processar solicitações HTTP, se necessário.

Por fim, os construtores que usavam `Uri` e `SearchCredentials` foram alterados. Por exemplo, se você tiver código parecido com este:

    var client =
        new SearchServiceClient(
            new SearchCredentials("abc123"),
            new Uri("http://myservice.search.windows.net"));

Você poderá alterá-lo para corrigir os erros de compilação:

    var client =
        new SearchServiceClient(
            new Uri("http://myservice.search.windows.net"),
            new SearchCredentials("abc123"));

Observe também que o tipo do parâmetro de credenciais foi alterado para `ServiceClientCredentials`. Isso provavelmente não afetará o código, já que `SearchCredentials` é derivado de `ServiceClientCredentials`.

### Passando uma ID de solicitação

Em versões mais antigas do SDK, você podia definir uma ID de solicitação no `SearchServiceClient` ou no `SearchIndexClient` e ela seria incluída em cada solicitação para a API REST. Isso é útil para solucionar problemas com o serviço de pesquisa, se você precisar entrar em contato com o suporte. No entanto, é mais útil para definir uma ID de solicitação exclusiva para cada operação em vez de usar a mesma ID para todas as operações. Por esse motivo, os métodos `SetClientRequestId` de `SearchServiceClient` e `SearchIndexClient` foram removidos. Em vez disso, você pode passar uma ID de solicitação para cada método de operação por meio do parâmetro opcional `SearchRequestOptions`.

> [AZURE.NOTE] Em uma futura versão do SDK, vamos adicionar um novo mecanismo para definir uma ID de solicitação globalmente nos objetos clientes que é consistente com a abordagem usada pelos outros SDKs do Azure.

#### Exemplo

Se você tiver um código parecido com este:

    client.SetClientRequestId(Guid.NewGuid());
    ...
    long count = client.Documents.Count();

Você poderá alterá-lo para corrigir os erros de compilação:

    long count = client.Documents.Count(new SearchRequestOptions(requestId: Guid.NewGuid()));

### Alterações de nome de interface

Os nomes de interface do grupo de operação foram alterados para ficarem consistentes com os nomes de propriedade correspondentes:

 - O tipo de `ISearchServiceClient.Indexes` foi renomeado de `IIndexOperations` para `IIndexesOperations`.
 - O tipo de `ISearchServiceClient.Indexers` foi renomeado de `IIndexerOperations` para `IIndexersOperations`.
 - O tipo de `ISearchServiceClient.DataSources` foi renomeado de `IDataSourceOperations` para `IDataSourcesOperations`.
 - O tipo de `ISearchIndexClient.Documents` foi renomeado de `IDocumentOperations` para `IDocumentsOperations`.

Essa alteração provavelmente não afetará o seu código, a menos que você tenha criado simulações dessas interfaces para fins de teste.

<a name="BugFixes"></a>
## Correções de bug na versão 1.1

Havia um bug nas versões mais antigas do SDK .NET da Pesquisa do Azure relacionado à serialização de classes de modelo personalizadas. O bug ocorria ao criar uma classe de modelo personalizada com uma propriedade de um tipo de valor não anulável.

### Etapas para reproduzir

Crie uma classe de modelo personalizada com uma propriedade de tipo de valor não anulável. Por exemplo, adicione uma propriedade `UnitCount` pública do tipo `int` em vez de `int?`.

Se você indexar um documento com o valor padrão desse tipo (por exemplo, 0 para `int`), o campo será nulo na Pesquisa do Azure. Se você pesquisar desse documento em seguida, a chamada `Search` gerará `JsonSerializationException` reclamando que não é possível converter `null` para `int`.

Além disso, os filtros podem não funcionar conforme o esperado, já que null foi gravado no índice em vez do valor desejado.

### Corrigir detalhes

Corrigimos o problema na versão 1.1 do SDK. Agora, se você tiver uma classe de modelo como esta:

    public class Model
    {
        public string Key { get; set; }

        public int IntValue { get; set; }
    }

e definir `IntValue` como 0, o valor será agora corretamente serializado como 0 durante a transmissão e armazenado como 0 no índice. A viagem de ida e volta também funciona conforme o esperado.

Há um problema potencial de que se deve estar ciente nessa abordagem: se você usar um tipo de modelo com uma propriedade não anulável, precisará **garantir** que nenhum documento no índice contenha um valor nulo para o campo correspondente. O SDK e a API REST da Pesquisa do Azure não podem ajudá-lo a impor isso.

Isso não é apenas uma preocupação hipotética: imagine um cenário em que você adiciona um novo campo a um índice existente do tipo `Edm.Int32`. Depois de atualizar a definição de índice, todos os documentos terão um valor nulo para esse novo campo (já que todos os tipos são anuláveis na Pesquisa do Azure). Ao usar uma classe de modelo com uma propriedade `int` não anulável para esse campo, você obterá uma `JsonSerializationException` como esta ao tentar recuperar documentos:

    Error converting value {null} to type 'System.Int32'. Path 'IntValue'.

Por esse motivo, continuamos a recomendar que você use tipos anuláveis nas suas classes de modelo como uma prática recomendada.

Para obter mais detalhes sobre esse bug e a correção, confira [esse problema no GitHub](https://github.com/Azure/azure-sdk-for-net/issues/1063).

## Conclusão
Se precisar de mais detalhes sobre como usar o SDK .NET da Pesquisa do Azure, confira os nosso artigos atualizado recentemente, [Tutorial](search-howto-dotnet-sdk.md).

Apreciamos os seus comentários sobre o SDK. Se você tiver problemas, fique à vontade para nos pedir ajuda no [Fórum MSDN da Pesquisa do Azure](https://social.msdn.microsoft.com/Forums/azure/pt-BR/home?forum=azuresearch). Se você encontrar um bug, poderá apresentar um problema no [repositório GitHub sobre SDK .NET do Azure](https://github.com/Azure/azure-sdk-for-net/issues). Não deixe de colocar o prefixo "SDK da Pesquisa:" no título do problema.

Obrigado por usar a Pesquisa do Azure!

<!---HONumber=AcomDC_0525_2016-->