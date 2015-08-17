<properties
   pageTitle="Como usar a Pesquisa do Azure de um aplicativo .NET | Microsoft Azure"
   description="Como usar a Pesquisa do Azure de um aplicativo .NET"
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
   ms.date="07/08/2015"
   ms.author="brjohnst"/>

# Como usar a Pesquisa do Azure de um aplicativo .NET #

Este artigo é um passo a passo para ajudar você a usar o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). Você pode usar o SDK do .NET para implementar uma experiência de pesquisa avançada em seu aplicativo usando a Pesquisa do Azure.

## Novidades no SDK da Pesquisa do Azure ##

O SDK é composto por um uma biblioteca de cliente, `Microsoft.Azure.Search`. Ele permite que você gerencie seus índices, fontes de dados e indexadores, carregue e gerencie documentos e execute consultas, sem a necessidade de lidar com os detalhes de HTTP e JSON.

A biblioteca de cliente define classes como `Index`, `Field` e `Document`, e operações como `Indexes.Create` e `Documents.Search` nas classes `SearchServiceClient` e `SearchIndexClient`. Essas classes são organizadas nos namespaces a seguir:

- [Microsoft.Azure.Search](https://msdn.microsoft.com/library/azure/microsoft.azure.search.aspx)
- [Microsoft.Azure.Search.Models](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.aspx)

A versão atual do SDK do .Net da Pesquisa do Azure é uma versão de pré-lançamento. Se você quiser fornecer comentários para que possamos incorporar na primeira versão estável, visite nossa [página de comentários](http://feedback.azure.com/forums/263029-azure-search).

O SDK do .NET dá suporte à versão `2015-02-28` da API REST da Pesquisa do Azure, documentada no [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx). Novos recursos que *não* fazem parte dessa versão, como o suporte a processadores de idioma natural da Microsoft ou o parâmetro de pesquisa `moreLikeThis`, estão em [visualização](search-api-2015-02-28-preview.md) e ainda não estão disponíveis no SDK. Confira em [Controle de versão do serviço de pesquisa](https://msdn.microsoft.com/library/azure/dn864560.aspx) ou [Atualizações mais recentes da Pesquisa do Azure](search-latest-updates.md) para obter atualizações do status de um desses recursos.

Outros recursos que não têm suporte neste SDK incluem:

  - [Operações de gerenciamento](https://msdn.microsoft.com/library/azure/dn832684.aspx). As operações de gerenciamento incluem provisionamento de serviços da Pesquisa do Azure e gerenciamento de chaves de API. Futuramente, esses recursos terão suporte em um SDK separado de Gerenciamento .NET da Pesquisa do Azure.

## Requisitos para o SDK ##

1. Visual Studio 2013 ou uma versão mais recente.

2. Seu próprio serviço de Pesquisa do Azure. Para usar o SDK, você precisará do nome do serviço e de uma ou mais chaves de API. [Criar um serviço no portal](search-create-service-portal.md) ajudará você com estas etapas.

3. Baixe o [pacote NuGet](http://www.nuget.org/packages/Microsoft.Azure.Search) do SDK do .NET da Pesquisa do Azure usando "Gerenciar pacotes NuGet" no Visual Studio. Basta procurar o nome do pacote `Microsoft.Azure.Search` em NuGet.org. Selecione "Include Prerelease" para garantir que o SDK de pré-lançamento aparecerá nos resultados da pesquisa.

O SDK do .NET da Pesquisa do Azure oferece suporte a aplicativos voltados para o .NET Framework 4.0 ou posterior, bem como aplicativos da Windows Store destinados ao Windows 8.1 e ao Windows Phone 8.1. Não há suporte para o Silverlight.

## Principais cenários ##

Há várias coisas que você precisará fazer em seu aplicativo de pesquisa. Neste tutorial, abordaremos os principais cenários:

- Criando um índice
- Preenchimento do índice com documentos
- Procura por documentos usando filtros e pesquisa de texto completo

O código de exemplo a seguir ilustra cada um deles. Fique à vontade para usar os trechos de código em seu próprio aplicativo.

### Visão geral ###

O exemplo de aplicativo que vamos explorar cria um novo índice chamado "hotéis", preenche-o com alguns documentos e, em seguida, executa algumas consultas de pesquisa. Este é o programa principal, mostrando o fluxo geral:

    // This sample shows how to delete, create, upload documents and query an index
    static void Main(string[] args)
    {
        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

Vamos examinar isso passo a passo. Primeiro, é necessário criar um novo `SearchServiceClient`. Esse objeto permite o gerenciamento de índices. Para construir um, você precisa fornecer o nome do serviço da Pesquisa do Azure, bem como uma chave de API de administração.

        // Put your search service name here. This is the hostname portion of your service URL.
        // For example, if your service URL is https://myservice.search.windows.net, then your
        // service name is myservice.
        string searchServiceName = "myservice";

        string apiKey = "Put your API admin key here."

        SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

> [AZURE.NOTE]Se você fornecer uma chave incorreta (por exemplo, uma chave de consulta quando era necessário fornecer uma chave de administrador), o `SearchServiceClient` lançará uma `CloudException` com a mensagem de erro "Forbidden" na primeira vez que você chamar um método de operação, como `Indexes.Create`. Se isso ocorrer, verifique novamente a chave da API.

As próximas linhas chamam métodos para criação de um índice chamado "hotéis," excluindo-o primeiro caso ele já exista. Abordaremos esses métodos um pouco mais tarde.

        Console.WriteLine("{0}", "Deleting index...\n");
        DeleteHotelsIndexIfExists(serviceClient);

        Console.WriteLine("{0}", "Creating index...\n");
        CreateHotelsIndex(serviceClient);

Em seguida, o índice precisa ser preenchido. Para fazer isso, precisaremos de um `SearchIndexClient`. Há duas maneiras de obter um: construindo ou chamando `Indexes.GetClient` no `SearchServiceClient`. Usaremos o último por conveniência.

        SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

> [AZURE.NOTE]Em um aplicativo típico de pesquisa, o gerenciamento e o preenchimento do índice é tratado por um componente separado das consultas de pesquisa. `Indexes.GetClient` é conveniente para o preenchimento de um índice porque poupa o trabalho de fornecer outras `SearchCredentials`. Ele faz isso passando a chave de administrador que você usou para criar o `SearchServiceClient` ao novo `SearchIndexClient`. No entanto, na parte do aplicativo que executa consultas, é melhor criar o `SearchIndexClient` diretamente para que você possa passar uma chave de consulta em vez de uma chave de administrador. Isso está de acordo com o princípio de privilégios mínimos e ajuda a tornar seu aplicativo mais seguro. Você pode saber mais sobre chaves de administração e chaves de consulta [aqui](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Agora que temos um `SearchIndexClient`, podemos preencher o índice. Isso é feito por outro método que descreveremos mais tarde detalhadamente.

        Console.WriteLine("{0}", "Uploading documents...\n");
        UploadDocuments(indexClient);

Por fim, executamos algumas consultas de pesquisa e exibimos os resultados, novamente usando o `SearchIndexClient`:

        Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
        SearchDocuments(indexClient, searchText: "fancy wifi");

        Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
        SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

        Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
        Console.ReadKey();

Se você executar esse aplicativo com um nome de serviço válido e uma chave API, o resultado deve ser assim:

    Deleting index...

    Creating index...

    Uploading documents...

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []
    Complete.  Press any key to end application...

O código-fonte completo do aplicativo é fornecido ao final deste artigo.

Em seguida, analisaremos com mais detalhes cada um dos métodos chamados pelo `Main`.

### Criando um índice ###

Depois de criar um `SearchServiceClient`, a próxima coisa que o `Main` faz é excluir o índice "hotéis", caso ele já exista. Isso é feito pelo método a seguir:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Esse método usa o `SearchServiceClient` fornecido para verificar se o índice existe e, nesse caso, excluí-lo.

> [AZURE.NOTE]O exemplo código deste artigo usa os métodos síncronos do SDK do .NET da Pesquisa do Azure para manter a simplicidade. Recomendamos que você use os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, no método acima você pode usar `ExistsAsync` e `DeleteAsync` em vez de `Exists` e `Delete`.

Em seguida, `Main` cria um novo índice "hotéis" chamando este método:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Esse método cria um novo objeto `Index` com uma lista de objetos `Field` que definem o esquema do novo índice. Cada campo tem um nome, tipo de dados e vários atributos que definem seu comportamento de pesquisa. Além dos campos, você também pode adicionar perfis de pontuação, sugestões ou opções de CORS para o índice (eles foram omitidos do exemplo por questão de brevidade). Você pode saber mais sobre o objeto Index e suas partes na referência do SDK no [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx), bem como na [Referência da API REST da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx).

### Preenchendo o índice ###

A próxima etapa no `Main` é preencher o índice recém-criado. Isso é feito com o método a seguir:

    private static void UploadDocuments(SearchIndexClient indexClient)
    {
        var documents =
            new Hotel[]
            {
                new Hotel()
                { 
                    HotelId = "1058-441", 
                    HotelName = "Fancy Stay", 
                    BaseRate = 199.0, 
                    Category = "Luxury", 
                    Tags = new[] { "pool", "view", "concierge" }, 
                    ParkingIncluded = false, 
                    LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                    Rating = 5, 
                    Location = GeographyPoint.Create(47.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "666-437", 
                    HotelName = "Roach Motel",
                    BaseRate = 79.99,
                    Category = "Budget",
                    Tags = new[] { "motel", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                    Rating = 1,
                    Location = GeographyPoint.Create(49.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "970-501", 
                    HotelName = "Econo-Stay",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "pool", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(46.678581, -122.131577)
                },
                new Hotel()
                { 
                    HotelId = "956-532", 
                    HotelName = "Express Rooms",
                    BaseRate = 129.99,
                    Category = "Budget",
                    Tags = new[] { "wifi", "budget" },
                    ParkingIncluded = true,
                    LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                    Rating = 4,
                    Location = GeographyPoint.Create(48.678581, -122.131577)
                },
                new Hotel() 
                { 
                    HotelId = "566-518", 
                    HotelName = "Surprisingly Expensive Suites",
                    BaseRate = 279.99,
                    Category = "Luxury",
                    ParkingIncluded = false
                }
            };

        try
        {
            indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
        }
        catch (IndexBatchException e)
        {
            // Sometimes when your Search service is under load, indexing will fail for some of the documents in
            // the batch. Depending on your application, you can take compensating actions like delaying and
            // retrying. For this simple demo, we just log the failed document keys and continue.
            Console.WriteLine(
                "Failed to index some of the documents: {0}",
                String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
        }

        // Wait a while for indexing to complete.
        Thread.Sleep(2000);
    }

Este método tem quatro partes. O primeiro cria uma matriz de objetos `Hotel` que servirá como nossos dados de entrada para carregamento do índice. Esses dados são codificados para manter a simplicidade. Em seu próprio aplicativo, provavelmente seus dados virão de uma fonte de dados externa, como um banco de dados SQL.

A segunda parte cria um `IndexAction` para cada `Hotel` e, em seguida, os agrupa em um novo `IndexBatch`. O lote é então carregado para o índice da Pesquisa do Azure pelo método `Documents.Index`.

> [AZURE.NOTE]Neste exemplo, estamos carregando apenas documentos. Se você quisesse mesclar as alterações em um documento existente ou excluir um documento, você poderia criar um `IndexAction` com o `IndexActionType` correspondente. Não precisamos especificar `IndexActionType` neste exemplo, pois o padrão é `Upload`.

A terceira parte desse método é um bloco catch que trata um caso de erro importante para indexação. Se o serviço de Pesquisa do Azure não indexar alguns documentos no lote, uma `IndexBatchException` será lançada por `Documents.Index`. Isso pode acontecer se você estiver indexando documentos enquanto o serviço estiver sob carga pesada. **É altamente recomendável a manipulação explícita desse caso em seu código.** Você pode atrasar e repetir a indexação de documentos que falharam, ou você pode registrar em log e continuar, como faz o exemplo, ou pode alguma outra coisa, dependendo dos requisitos de consistência de dados do aplicativo.

Por fim, o método atrasa por dois segundos. A indexação ocorre de maneira assíncrona em seu serviço de Pesquisa do Azure, portanto, o exemplo de aplicativo precisa aguardar alguns instantes para garantir que os documentos estejam disponíveis para pesquisa. Normalmente, atrasos como esses só são necessários em demonstrações, testes e exemplos de aplicativos.

#### Como o SDK do .NET lida com documentos ####

Você pode estar se perguntando como o SDK do .NET da Pesquisa do Azure é capaz de carregar instâncias de uma classe definida pelo usuário, como `Hotel`, no índice. Para ajudar a responder a essa pergunta, vamos examinar a classe `Hotel`:

    [SerializePropertyNamesAsCamelCase]
    public class Hotel
    {
        public string HotelId { get; set; }

        public string HotelName { get; set; }

        public double? BaseRate { get; set; }

        public string Category { get; set; }

        public string[] Tags { get; set; }

        public bool? ParkingIncluded { get; set; }

        public DateTimeOffset? LastRenovationDate { get; set; }

        public int? Rating { get; set; }

        public GeographyPoint Location { get; set; }

        public override string ToString()
        {
            return String.Format(
                "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                HotelId,
                HotelName,
                Category,
                (Tags != null) ? String.Join(", ", Tags) : String.Empty);
        }
    }

A primeira coisa a observar é que cada propriedade pública de `Hotel` corresponde a um campo na definição do índice, mas com uma diferença fundamental: o nome de cada campo começa com uma letra minúscula ("minúsculas concatenadas"), enquanto o nome de cada propriedade pública de `Hotel` começa com uma letra maiúscula ("maiúsculas concatenadas"). Esse é um cenário comum em aplicativos .NET que executam associação de dados quando o esquema de destino está fora do controle do desenvolvedor do aplicativo. Em vez de violar as diretrizes de nomenclatura do .NET, usando minúscula para os nomes de propriedade, você pode informar ao SDK para mapear automaticamente os nomes de propriedade como minúscula com o atributo `[SerializePropertyNamesAsCamelCase]`.

Um segundo fator importante sobre a classe `Hotel` são os tipos de dados das propriedades públicas. Os tipos .NET dessas propriedades são mapeados para seus tipos de campo equivalentes na definição do índice. Por exemplo, a propriedade de cadeia de caracteres `Category` mapeia para o campo `category`, que é do tipo `Edm.String`. Há mapeamentos de tipo semelhantes entre `bool?` e `Edm.Boolean`, `DateTimeOffset?` e `Edm.DateTimeOffset` etc. As regras específicas para o mapeamento de tipos estão documentadas com o método `Documents.Get` no [MSDN](https://msdn.microsoft.com/library/azure/dn931291.aspx). Observe que os tipos de valor, por exemplo, `bool` e `int`, são anuláveis na classe `Hotel`, pois todos os tipos de campo primitivos na Pesquisa do Azure são anuláveis.

Essa capacidade de usar suas próprias classes como documentos funciona em ambas as direções; Você também pode recuperar os resultados da pesquisa e fazer com que o SDK os desserialize automaticamente para um tipo de sua escolha, como veremos na próxima seção.

> [AZURE.NOTE]O SDK do .NET da Pesquisa do Azure também oferece suporte a documentos do tipo dinâmico usando a classe `Document`, que é um mapeamento de chave/valor de nomes de campo para valores de campo. Isso é útil em cenários nos quais você não conhece o esquema de índice no momento do design, ou nos quais seria inconveniente associar a classes de modelo específico. Todos os métodos no SDK que lidam com documentos têm sobrecargas que funcionam com a classe `Document`, bem como sobrecargas fortemente tipadas que utilizam um parâmetro de tipo genérico. Somente as últimas são usadas no exemplo de código deste tutorial. Você pode saber mais sobre a classe `Document` [aqui](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.document.aspx).

### Procura de documentos no índice ###

A última etapa no exemplo de aplicativo é procurar por documentos no índice. O método a seguir faz isso:

    private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
    {
        // Execute search based on search text and optional filter
        var sp = new SearchParameters();

        if (!String.IsNullOrEmpty(filter))
        {
            sp.Filter = filter;
        }

        DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
        foreach (SearchResult<Hotel> result in response)
        {
            Console.WriteLine(result.Document);
        }
    }

Primeiro, esse método cria um novo objeto `SearchParameters`. Isso é usado para especificar opções adicionais para a consulta, como classificação, filtragem, paginação e organização. Neste exemplo, estamos apenas definindo apenas a propriedade `Filter`.

A próxima etapa é executar a consulta de pesquisa. Isso é feito usando o método `Documents.Search`. Nesse caso, passamos o texto de pesquisa a ser usado como uma cadeia de caracteres e também os parâmetros de pesquisa criados anteriormente. Também podemos especificar `Hotel` como o parâmetro de tipo para `Documents.Search`, que informa ao SDK para desserializar documentos nos resultados da pesquisa em objetos do tipo `Hotel`.

Por fim, esse método percorre todas as correspondências nos resultados da pesquisa, imprimindo cada documento no console.

Vamos examinar com mais detalhes como esse método é chamado:

    SearchDocuments(indexClient, searchText: "fancy wifi");

    SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

Na primeira chamada, procuramos todos os documentos com os termos de consulta "fancy" ou "wifi". Na segunda chamada, o texto da pesquisa é definido como "*", que significa "encontrar tudo". Você pode saber mais sobre a sintaxe de expressão da consulta de pesquisa [aqui](https://msdn.microsoft.com/library/azure/dn798920.aspx).

A segunda chamada usa uma expressão OData `$filter`, `category eq 'Luxury'`. Isso restringe a pesquisa para retornar apenas os documentos nos quais o campo `category` corresponde exatamente à cadeia de caracteres "Luxury". Você pode saber mais sobre a sintaxe do OData com suporte da Pesquisa do Azure [aqui](https://msdn.microsoft.com/library/azure/dn798921.aspx).

Agora que você sabe o que fazem essas duas chamadas, deve ser mais fácil ver por que sua saída tem esta aparência:

    Searching documents 'fancy wifi'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 956-532     Name: Express Rooms     Category: Budget        Tags: [wifi, budget]

    Filter documents with category 'Luxury'...

    ID: 1058-441    Name: Fancy Stay        Category: Luxury        Tags: [pool, view, concierge]
    ID: 566-518     Name: Surprisingly Expensive Suites     Category: Luxury        Tags: []

A primeira pesquisa retorna dois documentos. O primeiro tem "Fancy" no nome, enquanto o segundo tem "wifi" no campo `tags`. A segunda pesquisa retorna dois documentos, os únicos documentos no índice que têm o campo `category` definido como "Luxury".

Essa etapa conclui o tutorial, mas não pare aqui. **Próximas etapas** fornece os recursos adicionais para aprender mais sobre a Pesquisa do Azure.

## Próximas etapas ##

- Aprofunde seus conhecimentos por meio de [vídeos e outros exemplos e tutoriais](https://msdn.microsoft.com/library/azure/dn818681.aspx).
- Leia sobre os recursos nesta versão do SDK da Pesquisa do Azure: [Visão geral da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn798933.aspx)
- Revise as [convenções de nomenclatura](https://msdn.microsoft.com/library/azure/dn857353.aspx) para saber as regras de nomeação de vários objetos.
- Examine os [tipos de dados com suporte](https://msdn.microsoft.com/library/azure/dn798938.aspx) na Pesquisa do Azure.


## Código-fonte do aplicativo de exemplo ##

Aqui está o código-fonte completo do aplicativo de exemplo usado neste passo a passo. Observe que você precisará substituir o nome do serviço e os espaços reservados da chave de API em Program.cs por seus próprios valores se quiser compilar e executar o exemplo.

Program.cs:

    using System;
    using System.Configuration;
    using System.Linq;
    using System.Threading;
    using Microsoft.Azure.Search;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        class Program
        {
            // This sample shows how to delete, create, upload documents and query an index
            static void Main(string[] args)
            {
                // Put your search service name here. This is the hostname portion of your service URL.
                // For example, if your service URL is https://myservice.search.windows.net, then your
                // service name is myservice.
                string searchServiceName = "myservice";

                string apiKey = "Put your API admin key here."

                SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(apiKey));

                Console.WriteLine("{0}", "Deleting index...\n");
                DeleteHotelsIndexIfExists(serviceClient);

                Console.WriteLine("{0}", "Creating index...\n");
                CreateHotelsIndex(serviceClient);

                SearchIndexClient indexClient = serviceClient.Indexes.GetClient("hotels");

                Console.WriteLine("{0}", "Uploading documents...\n");
                UploadDocuments(indexClient);

                Console.WriteLine("{0}", "Searching documents 'fancy wifi'...\n");
                SearchDocuments(indexClient, searchText: "fancy wifi");

                Console.WriteLine("\n{0}", "Filter documents with category 'Luxury'...\n");
                SearchDocuments(indexClient, searchText: "*", filter: "category eq 'Luxury'");

                Console.WriteLine("{0}", "Complete.  Press any key to end application...\n");
                Console.ReadKey();
            }

            private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
            {
                if (serviceClient.Indexes.Exists("hotels"))
                {
                    serviceClient.Indexes.Delete("hotels");
                }
            }

            private static void CreateHotelsIndex(SearchServiceClient serviceClient)
            {
                var definition = new Index()
                {
                    Name = "hotels",
                    Fields = new[]
                    {
                        new Field("hotelId", DataType.String)                       { IsKey = true },
                        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
                    }
                };

                serviceClient.Indexes.Create(definition);
            }

            private static void UploadDocuments(SearchIndexClient indexClient)
            {
                var documents =
                    new Hotel[]
                    {
                        new Hotel()
                        { 
                            HotelId = "1058-441", 
                            HotelName = "Fancy Stay", 
                            BaseRate = 199.0, 
                            Category = "Luxury", 
                            Tags = new[] { "pool", "view", "concierge" }, 
                            ParkingIncluded = false, 
                            LastRenovationDate = new DateTimeOffset(2010, 6, 27, 0, 0, 0, TimeSpan.Zero), 
                            Rating = 5, 
                            Location = GeographyPoint.Create(47.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "666-437", 
                            HotelName = "Roach Motel",
                            BaseRate = 79.99,
                            Category = "Budget",
                            Tags = new[] { "motel", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1982, 4, 28, 0, 0, 0, TimeSpan.Zero),
                            Rating = 1,
                            Location = GeographyPoint.Create(49.678581, -122.131577)
                        },
                        new Hotel() 
                        { 
                            HotelId = "970-501", 
                            HotelName = "Econo-Stay",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "pool", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(46.678581, -122.131577)
                        },
                        new Hotel()
                        { 
                            HotelId = "956-532", 
                            HotelName = "Express Rooms",
                            BaseRate = 129.99,
                            Category = "Budget",
                            Tags = new[] { "wifi", "budget" },
                            ParkingIncluded = true,
                            LastRenovationDate = new DateTimeOffset(1995, 7, 1, 0, 0, 0, TimeSpan.Zero),
                            Rating = 4,
                            Location = GeographyPoint.Create(48.678581, -122.131577)
                        },
                        new Hotel() 
                    {
                            HotelId = "566-518", 
                            HotelName = "Surprisingly Expensive Suites",
                            BaseRate = 279.99,
                            Category = "Luxury",
                            ParkingIncluded = false
                        }
                    };

                try
                {
                    indexClient.Documents.Index(IndexBatch.Create(documents.Select(doc => IndexAction.Create(doc))));
                }
                catch (IndexBatchException e)
                {
                    // Sometimes when your Search service is under load, indexing will fail for some of the documents in
                    // the batch. Depending on your application, you can take compensating actions like delaying and
                    // retrying. For this simple demo, we just log the failed document keys and continue.
                    Console.WriteLine(
                        "Failed to index some of the documents: {0}",
                        String.Join(", ", e.IndexResponse.Results.Where(r => !r.Succeeded).Select(r => r.Key)));
                }

                // Wait a while for indexing to complete.
                Thread.Sleep(2000);
            }

            private static void SearchDocuments(SearchIndexClient indexClient, string searchText, string filter = null)
            {
                // Execute search based on search text and optional filter
                var sp = new SearchParameters();

                if (!String.IsNullOrEmpty(filter))
                {
                    sp.Filter = filter;
                }

                DocumentSearchResponse<Hotel> response = indexClient.Documents.Search<Hotel>(searchText, sp);
                foreach (SearchResult<Hotel> result in response)
                {
                    Console.WriteLine(result.Document);
                }
            }
        }
    }

Hotel.cs:

    using System;
    using Microsoft.Azure.Search.Models;
    using Microsoft.Spatial;

    namespace AzureSearch.NETSDKSample
    {
        [SerializePropertyNamesAsCamelCase]
        public class Hotel
        {
            public string HotelId { get; set; }

            public string HotelName { get; set; }

            public double? BaseRate { get; set; }

            public string Category { get; set; }

            public string[] Tags { get; set; }

            public bool? ParkingIncluded { get; set; }

            public DateTimeOffset? LastRenovationDate { get; set; }

            public int? Rating { get; set; }

            public GeographyPoint Location { get; set; }

            public override string ToString()
            {
                return String.Format(
                    "ID: {0}\tName: {1}\tCategory: {2}\tTags: [{3}]",
                    HotelId,
                    HotelName,
                    Category,
                    (Tags != null) ? String.Join(", ", Tags) : String.Empty);
            }
        }
    }
 

<!---HONumber=August15_HO6-->