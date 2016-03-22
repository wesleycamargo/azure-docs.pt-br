<properties
    pageTitle="Criar um índice da Pesquisa do Azure usando o SDK .NET | Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
    description="Criar um índice no código usando o SDK .NET da Pesquisa do Azure."
    services="search"
    documentationCenter=""
    authors="brjohnstmsft"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="dotnet"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="brjohnst"/>

# Criar um índice de Pesquisa do Azure usando o SDK do .NET
> [AZURE.SELECTOR]
- [Visão geral](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


Este artigo o orientará ao longo do processo de criação de um [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) de Pesquisa do Azure usando o [SDK .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx).

Antes de seguir este guia e criar um índice, você já deverá ter [criado um serviço de Pesquisa do Azure](search-create-service-portal.md).

Observe que todos os códigos de exemplo neste artigo são escritos com C#. Você pode encontrar o código-fonte completo [no GitHub](http://aka.ms/search-dotnet-howto).

## I. Identificar a chave de API do administrador de seu serviço de Pesquisa do Azure
Agora que provisionou um serviço de Pesquisa do Azure, você está quase pronto para emitir solicitações em relação ao ponto de extremidade de serviço usando o SDK do .NET. Primeiro, é necessário obter uma das chaves de API do administrador gerada para o serviço de pesquisa que você provisionou. O SDK do .NET enviará está chave de API em cada solicitação ao seu serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

1. Para localizar as api-keys de seu serviço, você deve fazer logon no [Portal do Azure](https://portal.azure.com/)
2. Vá para a folha do serviço de Pesquisa do Azure
3. Clique no ícone de "Chaves"

O serviço terá *chaves de administração* e *chaves de consulta*.

  - Suas *chaves de administração* principal e secundária concedem direitos totais para todas as operações, incluindo a capacidade de gerenciar o serviço, criar e excluir índices, indexadores e fontes de dados. Há duas chaves para que você possa continuar a usar a chave secundária se decidir regenerar a chave primária e vice-versa.
  - As *chaves de consulta* concedem acesso somente leitura a índices e documentos e normalmente são distribuídas para aplicativos cliente que emitem solicitações de pesquisa.

Para criar um índice, você pode usar a chave de administração principal ou secundária.

<a name="CreateSearchServiceClient"></a>
## II. Criar uma instância da classe de SearchServiceClient
Para começar a usar o SDK .NET da Pesquisa do Azure, você precisará criar uma instância da classe `SearchServiceClient`. Essa classe tem vários construtores. Aquele que você deseja usa o nome do seu serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` encapsula a sua chave de API.

O código abaixo cria um novo `SearchServiceClient` usando valores para a chave de API e para o nome do serviço de pesquisa os quais são armazenados nos arquivos de configuração do aplicativo (`app.config` ou `web.config`):

```csharp
string searchServiceName = ConfigurationManager.AppSettings["SearchServiceName"];
string adminApiKey = ConfigurationManager.AppSettings["SearchServiceAdminApiKey"];

SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
```

O `SearchServiceClient` tem uma propriedade `Indexes`. Essa propriedade fornece todos os métodos necessários para criar, listar, atualizar ou excluir índices de Pesquisa do Azure.

> [AZURE.NOTE] A classe `SearchServiceClient` gerencia conexões para o seu serviço de pesquisa. Para evitar abrir um número excessivo de conexões, você deve tentar compartilhar uma única instância de `SearchServiceClient` em seu aplicativo, se possível. Esses métodos são thread-safe para habilitar esse compartilhamento.

<a name="DefineIndex"></a>
## III. Definir o seu índice de Pesquisa do Azure usando a classe `Index`
Uma única chamada para o método `Indexes.Create` criará o seu índice. Esse método aceita como um parâmetro um objeto `Index`, que define o índice da sua Pesquisa do Azure. Você precisa criar um objeto `Index` e inicializá-lo da seguinte maneira:

1. Defina a propriedade `Name` do objeto `Index` como o nome do índice.
2. Defina a propriedade `Fields` do objeto `Index` para uma matriz de objetos `Field`. Cada um dos objetos `Field` define o comportamento de um campo no seu índice. Você pode fornecer o nome do campo ao construtor, junto com o tipo de dados (ou o analisador para os campos de cadeia de caracteres). Também é possível definir outras propriedades como `IsSearchable`, `IsFilterable`, etc.

É importante ter em mente as suas necessidades de negócios e experiência de usuário de pesquisa ao projetar o índice, pois a cada `Field` devem ser atribuídas as [propriedades apropriadas](https://msdn.microsoft.com/library/azure/dn798941.aspx). Essas propriedades controlam quais recursos de pesquisa (filtragem, facetas, classificação, pesquisa de texto completo etc.) se aplicam a quais campos. Para qualquer propriedade que você não defina explicitamente, a classe `Field` desabilita por padrão o recurso de pesquisa correspondente, a menos que você o habilite especificamente.

Para nosso exemplo, chamamos o índice de "hotels" e definimos os campos da seguinte maneira:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = new[]
    {
        new Field("hotelId", DataType.String)                       { IsKey = true, IsFilterable = true },
        new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("description", DataType.String)                   { IsSearchable = true },
        new Field("description_fr", AnalyzerName.FrLucene),
        new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true, IsSortable = true },
        new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
        new Field("smokingAllowed", DataType.Boolean)               { IsFilterable = true, IsFacetable = true },
        new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
        new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
    }
};
```

Escolhemos cuidadosamente os valores de propriedade para cada `Field` com base em como achamos que serão usados em um aplicativo. Por exemplo, é provável que ao pesquisarem por "hotels", as pessoas estejam interessadas em correspondências de palavra-chave no campo `description`, por isso habilitamos a pesquisa de texto completo para esse campo definindo `IsSearchable` para `true`.

Observe que exatamente um campo no índice do tipo `DataType.String` deve ser designado como o campo _chave_, definindo `IsKey` para `true` (confira `hotelId` no exemplo acima).

A definição de índice acima usa um analisador de idioma personalizado para o campo `description_fr` porque ele se destina a armazenar texto em francês. Confira [o tópico de suporte a idiomas no MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx), além da [postagem do blog](https://azure.microsoft.com/blog/language-support-in-azure-search/) correspondente para obter mais informações sobre os analisadores de idioma.

> [AZURE.NOTE]  Observe que, passando `AnalyzerName.FrLucene` no construtor, `Field` será automaticamente do tipo `DataType.String` e terá `IsSearchable` definido como `true`.

## IV. Criar o índice
Agora que você tem um objeto `Index` inicializado, é possível criar o índice chamando `Indexes.Create` no seu objeto `SearchServiceClient`:

```csharp
serviceClient.Indexes.Create(definition);
```

Para uma solicitação bem-sucedida, o método retornará manualmente. Se houver um problema com a solicitação como um parâmetro inválido, o método gerará `CloudException`.

Quando o índice estiver pronto e você desejar excluí-lo, basta chamar o método `Indexes.Delete` em `SearchServiceClient`. Por exemplo, veja como podemos excluir o índice "hotels":

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [AZURE.NOTE] O exemplo código deste artigo usa os métodos síncronos do SDK do .NET da Pesquisa do Azure para manter a simplicidade. Recomendamos que você use os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, nos exemplos acima você pode usar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.

## Avançar
Após criar um índice de Pesquisa do Azure, você estará pronto para [carregar o conteúdo no índice](search-what-is-data-import.md) para que possa começar a pesquisar os dados.

<!---HONumber=AcomDC_0316_2016-->