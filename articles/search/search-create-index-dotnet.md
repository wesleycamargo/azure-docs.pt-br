---
title: 'Início Rápido: Criar um índice em um aplicativo de console C# – Azure Search'
description: Saiba como criar um índice de texto completo pesquisável em C# usando o SDK do .NET do Azure Search.
author: heidisteen
manager: cgronlun
ms.author: heidist
tags: azure-portal
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 04/08/2019
ms.openlocfilehash: 83842893e0ffc6bb954832cd65b6312b59bbcaa3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/18/2019
ms.locfileid: "59269037"
---
# <a name="quickstart-1---create-an-azure-search-index-in-c"></a>Início Rápido: 1 - Criar um índice de pesquisa do Azure Search em C#
> [!div class="op_single_selector"]
> * [C#](search-create-index-dotnet.md)
> * [Portal](search-get-started-portal.md)
> * [PowerShell](search-howto-dotnet-sdk.md)
> * [Postman](search-fiddler.md)
>*

Este artigo descreve o processo para criar [um índice do Azure Search](search-what-is-an-index.md) usando C# e o [SDK do .NET](https://aka.ms/search-sdk). Esta é a primeira lição em um exercício de três partes para criar, carregar e consultar um índice. A criação de índice é realizada executando estas tarefas:

> [!div class="checklist"]
> * Criar um objeto [`SearchServiceClient`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.searchserviceclient?view=azure-dotnet) para conectar um serviço de pesquisa.
> * Crie um objeto [`Index`](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.index?view=azure-dotnet) a ser passado como um parâmetro para `Indexes.Create`.
> * Chamar `Indexes.Create` em `SearchServiceClient` para enviar `Index` a um serviço.

## <a name="prerequisites"></a>Pré-requisitos

Os serviços, as ferramentas e os dados a seguir são usados neste início rápido. 

[Crie um serviço Azure Search](search-create-service-portal.md) ou [localize um serviço existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) na assinatura atual. É possível usar um serviço gratuito para este início rápido.

[Visual Studio 2017](https://visualstudio.microsoft.com/downloads/), qualquer edição. O código de exemplo e as instruções foram testados na edição gratuita da Comunidade.

[DotNetHowTo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowTo) fornece a solução de amostra, um aplicativo de console .NET Core escrito em C#, localizado no repositório GitHub de amostras do Azure. Baixe e extraia a solução. Por padrão, as soluções são somente leitura. Clique com o botão direito do mouse na solução e desmarque o atributo somente leitura para que você possa modificar arquivos. Os dados são incluídos na solução.

## <a name="get-a-key-and-url"></a>Obter uma chave e uma URL

As chamadas ao serviço exigem um ponto de extremidade de URL e uma chave de acesso em cada solicitação. Um serviço de pesquisa é criado com ambos, portanto, se você adicionou o Azure Search à sua assinatura, siga estas etapas para obter as informações necessárias:

1. [Entre no portal do Azure](https://portal.azure.com/) e, na página **Visão Geral** do serviço de pesquisa, obtenha a URL. Um ponto de extremidade de exemplo pode parecer com `https://mydemo.search.windows.net`.

2. Em **Configurações** > **Chaves**, obtenha uma chave de administração para adquirir todos os direitos sobre o serviço. Há duas chaves de administração intercambiáveis, fornecidas para a continuidade dos negócios, caso seja necessário sobrepor uma. É possível usar a chave primária ou secundária em solicitações para adicionar, modificar e excluir objetos.

![Obter um ponto de extremidade HTTP e uma chave de acesso](media/search-fiddler/get-url-key.png "Obter um ponto de extremidade HTTP e uma chave de acesso")

Todas as solicitações requerem uma chave de api em cada pedido enviado ao serviço. Ter uma chave válida estabelece a relação de confiança, para cada solicitação, entre o aplicativo que envia a solicitação e o serviço que lida com ela.

## <a name="1---configure-and-build"></a>1 – Configurar e criar

1. Abra o arquivo **DotNetHowTo.sln** no Visual Studio.

1. Em appsettings.json, substitua o conteúdo padrão pelo exemplo abaixo e forneça o nome do serviço e chave de api de administração do serviço. 


   ```json
   {
       "SearchServiceName": "Put your search service name here (not the full URL)",
       "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
    }
   ```

  Para o nome do serviço, é necessário somente o próprio nome. Por exemplo, se a URL for https://mydemo.search.windows.net, adicione `mydemo` ao arquivo JSON.

1. Pressione F5 para criar a solução e executar o aplicativo de console. As etapas restantes neste exercício e as seguintes são uma exploração de como esse código funciona. 

Como alternativa, veja [Como usar o Azure Search em um aplicativo .NET](search-howto-dotnet-sdk.md) para obter uma cobertura mais detalhada dos comportamentos do SDK. 

<a name="CreateSearchServiceClient"></a>

## <a name="2---create-a-client"></a>2 - Criar um cliente

Para começar a usar o SDK do .NET do Azure Search, crie uma instância da classe `SearchServiceClient`. Essa classe tem vários construtores. Aquele que você deseja usa o nome do seu serviço de pesquisa e um objeto `SearchCredentials` como parâmetros. `SearchCredentials` encapsula sua api-key.

O código a seguir pode ser encontrado no arquivo Program.cs. Ele cria um novo `SearchServiceClient` usando valores para o nome do serviço de pesquisa e a chave de api armazenados no arquivo de configuração do aplicativo (appsettings.json).

```csharp
private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
{
    string searchServiceName = configuration["SearchServiceName"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];

    SearchServiceClient serviceClient = new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
    return serviceClient;
}
```

O `SearchServiceClient` tem uma propriedade `Indexes`. Essa propriedade fornece todos os métodos necessários para criar, listar, atualizar ou excluir índices do Azure Search.

> [!NOTE]
> A classe `SearchServiceClient` gerencia conexões para o seu serviço de pesquisa. Para evitar abrir um número excessivo de conexões, você deve tentar compartilhar uma única instância de `SearchServiceClient` em seu aplicativo, se possível. Esses métodos são thread-safe para habilitar esse compartilhamento.
> 
> 

<a name="DefineIndex"></a>

## <a name="3---construct-index"></a>3 - Construir index
Uma única chamada para o método `Indexes.Create` cria um índice. Esse método usa como parâmetro um objeto `Index` que define um índice do Azure Search. Crie um objeto `Index` e inicialize-o conforme a seguir:

1. Defina a propriedade `Name` do objeto `Index` como o nome do índice.

2. Defina a propriedade `Fields` do objeto `Index` para uma matriz de objetos `Field`. A maneira mais fácil de criar os objetos `Field` é chamando o método `FieldBuilder.BuildForType`, passando uma classe de modelo para o parâmetro de tipo. Uma classe de modelo tem propriedades que mapeiam para os campos do seu índice. Isso permite a vinculação de documentos do índice de pesquisa a instâncias da sua classe de modelo.

> [!NOTE]
> Se você não planeja usar uma classe de modelo, você ainda pode definir o índice criando objetos `Field` diretamente. Você pode fornecer o nome do campo ao construtor, junto com o tipo de dados (ou o analisador para os campos de cadeia de caracteres). Também é possível definir outras propriedades como `IsSearchable`, `IsFilterable`, para citar algumas.
>
>

É importante que você tenha em mente a experiência do usuário de pesquisa e as necessidades de negócios ao projetar o índice. É necessário atribuir a cada campo os [atributos](https://docs.microsoft.com/rest/api/searchservice/Create-Index) que controlam quais recursos de pesquisa (filtragem, facetas, classificação, e assim por diante) são aplicáveis a esses campos. Para qualquer propriedade que você não defina explicitamente, a classe `Field` desabilita por padrão o recurso de pesquisa correspondente, a menos que você o habilite especificamente.

Neste exemplo, o nome do índice é "hotéis" e os campos são definidos usando uma classe de modelo. Cada propriedade da classe de modelo tem atributos que determinam os comportamentos relacionados à pesquisa do campo de índice correspondente. A classe de modelo é definida da seguinte maneira:

```csharp
using System;
using Microsoft.Azure.Search;
using Microsoft.Azure.Search.Models;
using Microsoft.Spatial;
using Newtonsoft.Json;

// The SerializePropertyNamesAsCamelCase attribute is defined in the Azure Search .NET SDK.
// It ensures that Pascal-case property names in the model class are mapped to camel-case
// field names in the index.
[SerializePropertyNamesAsCamelCase]
public partial class Hotel
{
    [System.ComponentModel.DataAnnotations.Key]
    [IsFilterable]
    public string HotelId { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public double? BaseRate { get; set; }

    [IsSearchable]
    public string Description { get; set; }

    [IsSearchable]
    [Analyzer(AnalyzerName.AsString.FrLucene)]
    [JsonProperty("description_fr")]
    public string DescriptionFr { get; set; }

    [IsSearchable, IsFilterable, IsSortable]
    public string HotelName { get; set; }

    [IsSearchable, IsFilterable, IsSortable, IsFacetable]
    public string Category { get; set; }

    [IsSearchable, IsFilterable, IsFacetable]
    public string[] Tags { get; set; }

    [IsFilterable, IsFacetable]
    public bool? ParkingIncluded { get; set; }

    [IsFilterable, IsFacetable]
    public bool? SmokingAllowed { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public DateTimeOffset? LastRenovationDate { get; set; }

    [IsFilterable, IsSortable, IsFacetable]
    public int? Rating { get; set; }

    [IsFilterable, IsSortable]
    public GeographyPoint Location { get; set; }
}
```

Escolhemos cuidadosamente os atributos para cada propriedade com base em como achamos que serão usados em um aplicativo. Por exemplo, é provável que ao pesquisarem por "hotels", as pessoas estejam interessadas em correspondências de palavra-chave no campo `description`, por isso habilitamos a pesquisa de texto completo para esse campo, adicionando o atributo `IsSearchable` à propriedade `Description`.

Observe que exatamente um campo no índice do tipo `string` deve ser designado como o campo *chave*, adicionando o atributo `Key` (confira `HotelId` no exemplo acima).

A definição de índice acima usa um analisador de idioma para o campo `description_fr` porque ele se destina a armazenar texto em francês. Para obter mais informações, consulte [Adicionar analisadores de idioma a um índice do Azure Search](index-add-language-analyzers.md).

> [!NOTE]
> Por padrão, o nome de cada propriedade na classe de modelo corresponde ao nome do campo no índice. Se você desejar mapear todos os nomes de propriedade para nomes de campo em minúsculas concatenadas, marque a classe com o `SerializePropertyNamesAsCamelCase` atributo. Se você deseja mapear para um nome diferente, você pode usar o atributo `JsonProperty` como a propriedade `DescriptionFr` acima. O atributo `JsonProperty` tem precedência sobre o atributo `SerializePropertyNamesAsCamelCase`.
> 
> 

Agora que definimos uma classe de modelo, podemos criar facilmente uma definição de índice:

```csharp
var definition = new Index()
{
    Name = "hotels",
    Fields = FieldBuilder.BuildForType<Hotel>()
};
```

## <a name="4---call-indexescreate"></a>4 - Chamar Indexes.Create
Agora que você tem um objeto `Index` inicializado, crie o índice chamando `Indexes.Create` no objeto `SearchServiceClient` :

```csharp
serviceClient.Indexes.Create(definition);
```

Para uma solicitação bem-sucedida, o método retornará manualmente. Se houver um problema com a solicitação como um parâmetro inválido, o método gerará `CloudException`.

Ao concluir um índice e quiser excluí-lo, chame o método `Indexes.Delete` no `SearchServiceClient`. Por exemplo: 

```csharp
serviceClient.Indexes.Delete("hotels");
```

> [!NOTE]
> O exemplo código deste artigo usa os métodos síncronos do SDK do .NET do Azure Search para manter a simplicidade. Recomendamos que você use os métodos assíncronos em seus próprios aplicativos para mantê-los escalonáveis e responsivos. Por exemplo, nos exemplos acima você pode usar `CreateAsync` e `DeleteAsync` em vez de `Create` e `Delete`.
> 
> 

## <a name="next-steps"></a>Próximas etapas
Neste início rápido, você criou um índice do Azure Search vazio com base em um esquema que define os comportamentos e tipos de dados de campo. O índice é um índice de funções básicas que consiste em um nome e uma coleção de campos atribuídos. Um índice mais realista incluiria outros elementos, como [perfis de pontuação](index-add-scoring-profiles.md), [sugestores](index-add-suggesters.md) para obter suporte de digitação antecipada [sinônimos](search-synonyms.md) e, possivelmente, [analisadores personalizados](index-add-custom-analyzers.md). É recomendável que você visite novamente esses recursos depois de entender o fluxo de trabalho básico.

O próximo início rápido desta série aborda como carregar o índice com conteúdo pesquisável.

> [!div class="nextstepaction"]
> [Carregar dados em um índice do Azure Search usando C#](search-import-data-dotnet.md)