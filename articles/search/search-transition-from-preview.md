<properties 
	pageTitle="Transição da versão da api de visualização=2014* para a versão da api=2015*" 
	description="Saiba mais sobre as alterações significativas e como migrar o código escrito na versão de visualização de 31-07-2014 ou na versão de visualização de 20-10-2014 para a Pesquisa do Azure, versão da api=28-02-2015." 
	services="search" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe" 
	editor=""/>

<tags 
	ms.service="search" 
	ms.devlang="rest-api" 
	ms.workload="search" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="11/04/2015" 
	ms.author="heidist"/>

#Transição da versão da api de visualização=2014* para a versão da api=2015*#

A Pesquisa do Azure é um serviço de pesquisa de nuvem hospedado do Microsoft Azure. A orientação a seguir serve para clientes que criam aplicativos personalizados nas versões de visualização da Pesquisa do Azure pesquisa e agora estão migrando para a versão disponível publicamente, 2015-02-28.

Como um cliente de visualização, você pode ter usado uma dessas versões mais antigas de visualização:

- [2014-07-31-Preview](search-api-2014-07-31-preview.md)
- [2014-10-20-Preview](search-api-2014-10-20-preview.md)

Agora que a Pesquisa do Azure está disponível publicamente, recomendamos fazer a transição para versões mais recentes: 2015-02-28 é a versão oficial da API da versão disponível publicamente da Pesquisa do Azure. Esta versão está documentada no [MSDN](https://msdn.microsoft.com/library/azure/dn798933.aspx).

Também estamos implantando a próxima versão de visualização, [2015-02-28-Preview](search-api-2015-02-28-preview.md), apresentando recursos que ainda estão em desenvolvimento. Você pode fornecer comentários sobre a API de visualização por meio dos [Fóruns de Pesquisa do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch) ou da nossa [página de comentários](http://feedback.azure.com/forums/263029-azure-search).

###Lista de verificação para migração###

- Revise as alterações significativas para determinar se sua solução foi afetada.
- Eleve a versão de API para `2015-02-28` para a versão bloqueada. Esta versão está sob SLA. Se você enfrentar problemas, poderá resolvê-los mais rapidamente.
- Criar, implantar, testar. Você deve ter 100% de paridade em termos de comportamentos de pesquisa, com exceção de alterações significativas.
- Distribua para produção
- Avalie os novos recursos para adoção futura. Eleve novamente o 2015-02-28-Preview se você quiser testar os processadores de idioma natural da Microsoft ou o `morelikethis`.

##Alterações significativas na versão da api= 2015*##

A versão inicial da API incluía um recurso de sugestões de preenchimento automático ou preenchimento automático. Embora fosse útil, a correspondência apenas de prefixos era limitada, pesquisando os primeiros caracteres do termo de pesquisa, sem suporte para a correspondência em outro lugar no campo. A implementação foi uma propriedade Booliana chamada `suggestions` que você definiria como `true` se quisesse habilitar a correspondência de prefixo em um determinado campo.

Essa implementação original foi substituída por um novo constructo `Suggesters` definida no recurso [índice](https://msdn.microsoft.com/library/azure/dn798941.aspx) que fornece infixos e correspondência difusa. Como os nomes sugerem, infixos e correspondência difusa proporcionam capacidade de correspondência muito mais ampla. A correspondência de infixos abrange prefixos, pois ainda faz a correspondência de caracteres iniciais, mas amplia a correspondência para incluir o restante da cadeia de caracteres.

Optamos por interromper a implementação anterior (a propriedade booliana), o que significa que ela estará totalmente indisponível em qualquer uma das versões 2015 sem compatibilidade com versões anteriores, a fim de evitar sua adoção acidental por clientes que estão criando soluções mais novas. Se você usar um `2015-02-28` ou `2015-02-28-Preview` será necessário usar o novo constructo `Suggesters` para habilitar consultas de preenchimento automático.

##Portabilidade do código existente##

A propriedade de sugestão é a única alteração significativa. Se você não usar essa propriedade poderá usar a `api-version` do `2014-07-31-Preview` ou do `2014-10-20-Preview` com `2015-02-28` e, em seguida, recriar e reimplantar. O aplicativo funcionará como antes.

Aplicativos personalizados que implementam sugestões devem fazer o seguinte:

1. Atualizar todos os pacotes NuGet.
1. Elevar a versão de api para `2015-02-28`. Se você estiver usando o exemplo de código a seguir, a versão de api estará na classe **AzureSearchHelper**.
1. Exclua o atributo `Suggestions={true | false}` do esquema JSON que define o índice.
1. Adicione um constructo na parte inferior do índice para `Suggesters` (como mostra a seção [após](#after)).
1. Verifique se que você pode publicar seu serviço (talvez seja necessário renomear o índice para evitar conflitos de nomenclatura).
1. Recompile a solução e implante em um ambiente de teste.
1. Execute todos os casos de teste para garantir que a solução se comporta como o esperado.
1. Distribua para produção

O exemplo de código de [da Adventure Works no codeplex](https://azuresearchadventureworksdemo.codeplex.com/) tem a implementação de `Suggestions` original. Talvez você queira usar esse exemplo para praticar a migração de código no código de exemplo.

Na seção a seguir, mostraremos uma implementação [antes](#before) e [depois](#after) das sugestões. Você pode substituir o método **CreateCatalogIndex()** pela versão na seção [após](#after) e, em seguida, compilar e implantar a solução para testar a nova funcionalidade.

<a name="before"></a>
###Antes###

Como você pode ver, `Suggestions` são uma propriedade Booliana definida em cada campo. Exclua todos os atributos `Suggestions`.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = true  },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = false },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false, Suggestions = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true,  Suggestions = true  },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true,  Suggestions = false }
                }
            };

<a name="after"></a>
###Afinal,###

Uma definição de esquema migrado omite a propriedade `Suggestions` e adiciona um constructo `Suggesters` na parte inferior.

        private static void CreateCatalogIndex()
        {
            var definition = new 
            {
                Name = "catalog",
                Fields = new[] 
                { 
                    new { Name = "productID",        Type = "Edm.String",         Key = true,  Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "name",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = true,  Facetable = false, Retrievable = true },
                    new { Name = "productNumber",    Type = "Edm.String",         Key = false, Searchable = true,  Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "color",            Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "standardCost",     Type = "Edm.Double",         Key = false, Searchable = false, Filterable = false, Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "listPrice",        Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "size",             Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = true,  Facetable = true,  Retrievable = true },
                    new { Name = "weight",           Type = "Edm.Double",         Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "sellStartDate",    Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "sellEndDate",      Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = false },
                    new { Name = "discontinuedDate", Type = "Edm.DateTimeOffset", Key = false, Searchable = false, Filterable = true,  Sortable = false, Facetable = false, Retrievable = true },
                    new { Name = "categoryName",     Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "modelName",        Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = true,  Retrievable = true },
                    new { Name = "description",      Type = "Edm.String",         Key = false, Searchable = true,  Filterable = true,  Sortable = false, Facetable = false, Retrievable = true }
                },
                suggesters = new[]
                {
                new {
                    name = "sg",
                    searchMode = "analyzingInfixMatching",
                    sourceFields = new []{"name", "productNumber", "categoryName", "modelName", "description"}
                    }
                 }
            };

##Avaliar novos recursos e abordagens##

Após a portabilidade de sua solução e a confirmação da execução conforme o esperado, você pode usar estes links para ler sobre os novos recursos.

- [Pesquisa do Azure disponível (postagem de blog)](http://go.microsoft.com/fwlink/p/?LinkId=528211)
- [Novidades na atualização mais recente para a Pesquisa do Azure](search-latest-updates.md)
- [O que é a Pesquisa do Azure?](search-what-is-azure-search.md)

##Obter ajuda##

A versão de API `2015-02-28` está sob SLA. Use as opções de suporte e os links [nesta página](../support/options/) para emitir um tíquete de suporte.

 

<!---HONumber=Nov15_HO2-->