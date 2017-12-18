# [Visão geral](search-what-is-azure-search.md)
## [O que é o Azure Search?](search-what-is-azure-search.md)

# Introdução

## [Criar um serviço](search-create-service-portal.md)
## [Criar um índice](search-what-is-an-index.md)
### [portal do Azure](search-create-index-portal.md)
### [.NET](search-create-index-dotnet.md)
### [REST](search-create-index-rest-api.md)
## [Adicionar dados](search-what-is-data-import.md)
### [Portal do Azure](search-import-data-portal.md)
### [.NET](search-import-data-dotnet.md)
### [REST](search-import-data-rest-api.md)
## [Pesquisar um índice](search-query-overview.md)
### [portal do Azure](search-explorer.md)
### [.NET](search-query-dotnet.md)
### [REST](search-query-rest-api.md)

# Tutoriais

## [Desenvolver em .NET](search-howto-dotnet-sdk.md)
## [Visualização de sinônimos no .NET](search-synonyms-tutorial-sdk.md)
## [Indexador de dados SQL no .NET](search-indexer-tutorial.md)
## [Passo a passo sobre o portal](search-get-started-portal.md)
## [Pesquisar dados semiestruturados](search-semi-structured-data.md)
## [Explorar a API REST](search-fiddler.md)

# Como

## Planejar e Projetar
### [Escolher um SKU](search-sku-tier.md)
### [Limites de serviço](search-limits-quotas-capacity.md)
### [Escalabilidade de serviço](search-capacity-planning.md)
### [Projetar padrões para multilocação](search-modeling-multitenant-saas-applications.md)
## Segurança
### [Dados e segurança operacional](search-security-overview.md)
### [Proteger com filtros de identidade](search-security-trimming-for-azure-search.md)
### [Sincronizado com o Active Directory](search-security-trimming-for-azure-search-with-aad.md)
## Desenvolvimento
### [Versões de API](search-api-versions.md)
### [Desenvolver em Node.js](search-get-started-nodejs.md)
### [Desenvolver em Java](search-get-started-java.md)
### [Atualizar o SDK](search-dotnet-sdk-migration.md)
### [Atualizar a API REST](search-api-migration.md)
### [Modelar tipos de dados complexos](search-howto-complex-data-types.md)
### [Suporte a atualizações simultâneas](search-howto-concurrency.md)
### [Exemplos de código](https://azure.microsoft.com/resources/samples/?service=search)
## Carregar dados
### [Visão geral do indexador](search-indexer-overview.md)
### [Indexador do Armazenamento de Blobs do Azure](search-howto-indexing-azure-blob-storage.md)
### [Indexador do Armazenamento de Tabelas do Azure](search-howto-indexing-azure-tables.md)
### [Indexador do SQL do Azure](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
### [Indexador de Banco de dados do Azure Cosmos](search-howto-index-documentdb.md)
### [Indexar blobs do CSV](search-howto-index-csv-blobs.md)
### [Indexar blobs do JSON](search-howto-index-json-blobs.md)
### [Configurar conexões do indexador para o SQL Server na VM do Azure](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md)
### [Mapeamentos de campo em indexadores](search-indexer-field-mappings.md)
##  Pesquisar
### [Como funciona a pesquisa de texto completo](search-lucene-query-architecture.md)
### Construção da Consulta
#### [Sintaxe de consulta simples](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search)
#### [Sintaxe de consulta Lucene](https://docs.microsoft.com/rest/api/searchservice/lucene-query-syntax-in-azure-search)
#### [Exemplos de consulta do Lucene](search-query-lucene-examples.md)
### Analisadores no Azure Search
#### [Visão geral](search-analyzers.md)
#### [Analisadores de linguagem](https://docs.microsoft.com/rest/api/searchservice/language-support)
#### [Analisadores personalizados](https://docs.microsoft.com/rest/api/searchservice/custom-analyzers-in-azure-search)
### Filtros no Azure Search
#### [Visão geral](search-filters.md)
#### [Filtros da faceta](search-filters-facets.md)
#### [Filtros da linguagem](search-filters-language.md)
#### [Referência de sintaxe da expressão](https://docs.microsoft.com/rest/api/searchservice/odata-expression-syntax-for-azure-search)
### [Resultados da paginação](search-pagination-page-layout.md)
### [Pontuação](https://docs.microsoft.com/rest/api/searchservice/add-scoring-profiles-to-a-search-index)
### [Sugestões](https://docs.microsoft.com/rest/api/searchservice/suggesters)
### [Navegação facetada](search-faceted-navigation.md)
### [Visualização de sinônimos](search-synonyms.md)
### [Visualização de moreLikeThis](search-more-like-this.md)
## Administração e Análise
### [Gerenciar usando o portal do Azure](search-manage.md)
### [Gerenciar usando o PowerShell](search-manage-powershell.md)
### [Monitorar uso e estatísticas](search-monitor-usage.md)
### [Análise do tráfego de pesquisa](search-traffic-analytics.md)
### [Desempenho e otimização](search-performance-optimization.md)

# Referência

## [.NET](/dotnet/api/?term=microsoft.azure.search)
## [.NET (Gerenciamento)](/dotnet/api/?term=microsoft.azure.management.search)
## [Python (Gerenciamento)](http://azure-sdk-for-python.readthedocs.io/en/latest/ref/azure.mgmt.search.html)
## [REST](/rest/api/searchservice)
## [REST (Gerenciamento)](/rest/api/searchmanagement)
## [Serviço REST (Visualização)](search-api-2016-09-01-preview.md)

# Recursos

## [FAQ - Perguntas Frequentes ](search-faq-frequently-asked-questions.md)
## [Preços](https://azure.microsoft.com/pricing/details/search/)
## [Calculadora de preço](https://azure.microsoft.com/pricing/calculator/)
## [Atualizações de serviço](https://azure.microsoft.com/updates/?product=search)
## Curso e tutoriais
### [Vídeos e tutoriais](search-video-demo-tutorial-list.md)
### [Academia virtual](https://mva.microsoft.com/training-courses/using-windows-azure-search-10540?l=ADkxnd97_9304984382)
## Sites de demonstração
### [Demonstração do analisador de pesquisa](http://alice.unearth.ai/)
### [Aplicativos de demonstração ao vivo](https://searchsamples.azurewebsites.net/)
### [Aplicativo de lista de trabalhos](http://aka.ms/azjobsdemo)
## Parceiro e comunidade
### [GitHub do Azure Search](https://github.com/Azure-Samples/?utf8=%E2%9C%93&query=search)
### [Fórum do MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureSearch)
### [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-search)
### [blog: modelar dados relacionais](http://blogs.technet.com/b/onsearch/archive/2015/09/08/modeling-the-adventureworks-inventory-database-for-azure-search.aspx)
### [blog: faceta em vários níveis](http://blogs.technet.com/b/onsearch/archive/2015/09/09/multi-level-taxonomy-facets-in-azure-search.aspx)



