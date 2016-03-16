<properties
	pageTitle="Criar um índice da Pesquisa do Azure usando o .NET| Microsoft Azure | Serviço de pesquisa de nuvem hospedado"
	description="Crie um índice no código usando o SDK ou a biblioteca .NET da Pesquisa do Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Criar um índice da Pesquisa do Azure usando o .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Este artigo mostra como criar um índice usando o [SDK do .NET da Pesquisa do Azure](https://msdn.microsoft.com/library/azure/dn951165.aspx). O conteúdo abaixo é um subconjunto de [Como usar a Pesquisa do Azure de um aplicativo .NET](search-howto-dotnet-sdk.md). Consulte o artigo pai para obter as etapas de ponta a ponta.

Os pré-requisitos para a criação de um índice incluem ter estabelecido anteriormente uma conexão com o serviço de pesquisa, o que normalmente é feito por meio de um `SearchServiceClient`. Uma prática recomendada para garantir uma reimplantação descomplicada é excluir um índice existente de mesmo nome, caso ele já exista.

Supondo que haja um índice chamado 'hotéis', você poderá construir um método para isso da seguinte maneira:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Esse método usa o `SearchServiceClient` fornecido para verificar se o índice existe e, nesse caso, excluí-lo.

Para criar um novo índice de "hotéis", construa um método semelhante ao seguinte:

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

<!---HONumber=AcomDC_0224_2016-->