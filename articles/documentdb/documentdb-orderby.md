---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: d59ebef3cda36ba048b844f0cd2326fff66b4aa5
ms.openlocfilehash: c5fc68fb25a4cb2166e0c0a72871d16c71397852
ms.lasthandoff: 02/16/2017


---
# <a name="sorting-documentdb-data-using-order-by"></a>Classificando dados do Banco de Dados de Documentos usando Order By
O Banco de Dados de Documentos do Microsoft Azure tem suporte para a realização da consulta de documentos utilizando uma SQL em documentos JSON. Os resultados de consulta podem ser ordenados usando a cláusula ORDER BY nas instruções de consulta SQL.

Após ler este artigo, você poderá responder as perguntas a seguir: 

* Como consultar com Order By?
* Como configurar uma política de indexação para Order By?
* Qual é o próximo passo?

Também são fornecidas [amostras](#samples) e [Perguntas frequentes](#faq).

Para obter uma referência completa sobre consultas SQL, consulte o [tutorial de Consulta do Banco de Dados de Documentos](documentdb-sql-query.md).

## <a name="how-to-query-with-order-by"></a>Como consultar com Order By
Como no ANSI-SQL, agora você pode incluir uma cláusula opcional Order By em instruções SQL ao consultar o Banco de Dados de Documentos. A cláusula pode incluir um argumento ASC/DESC opcional para especificar a ordem na qual os resultados devem ser recuperados. 

### <a name="ordering-using-sql"></a>Ordenação usando SQL
Por exemplo, aqui está uma consulta para recuperar os 10 primeiros registros em ordem decrescente de títulos. 

    SELECT TOP 10 * 
    FROM Books 
    ORDER BY Books.Title DESC

### <a name="ordering-using-sql-with-filtering"></a>Ordenação usando SQL com filtragem
É possível ordenar usando qualquer propriedade aninhada dentro de documentos como Books.ShippingDetails.Weight e especificar filtros adicionais na cláusula WHERE em combinação com Order By, como neste exemplo:

    SELECT * 
    FROM Books 
    WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### <a name="ordering-using-the-linq-provider-for-net"></a>Ordenação usando o provedor LINQ para .NET
Usando o SDK do .NET versão 1.2.0 e superior, você também pode usar a cláusula OrderBy() ou OrderByDescending() em consultas LINQ, como neste exemplo:

    foreach (Book book in client.CreateDocumentQuery<Book>(UriFactory.CreateDocumentCollectionUri("db", "books"))
        .OrderBy(b => b.PublishTimestamp)
        .Take(100))
    {
        // Iterate through books
    }

O Banco de Dados de Documentos dá suporte à ordenação com uma propriedade numérica simples, de cadeia de caracteres ou booliana por consulta, com tipos de consulta adicionais que chegarão em breve. Consulte [Qual é o próximo passo](#Whats_coming_next) para obter mais detalhes.

## <a name="configure-an-indexing-policy-for-order-by"></a>Configurar uma política de indexação para Order By
Lembre-se de que o Banco de Dados de Documentos dá suporte a dois tipos de índices (Hash e Intervalo), que podem ser definidos para caminhos/propriedades específicos, tipos de dados (cadeias de caracteres/números) e valores de precisão diferente (precisão máxima ou um valor de precisão fixa). Como o Banco de Dados de Documentos usa como padrão a indexação Hash, você deve criar uma nova coleção com uma política de indexação personalizada com Intervalo em números, cadeias de caracteres ou ambos, para usar Order By. 

> [!NOTE]
> Índices de intervalo de cadeia de caracteres foram introduzidos em 7 de julho de 2015 com a API REST versão 2015-06-03. Para criar políticas de Order By em cadeias de caracteres, você deve usar a versão SDK 1.2.0 do .NET ou a versão 1.1.0 do Python, Node. js ou Java.
> 
> Antes da versão 2015-06-03 da API REST, a política de indexação de coleção padrão era Hash para cadeias de caracteres e números. Isso foi alterado para Hash para cadeias de caracteres e Intervalo para números. 
> 
> 

Para obter mais detalhes, consulte [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md).

### <a name="indexing-for-order-by-against-all-properties"></a>Indexação de Order By em relação a todas as propriedades
Aqui está como você pode criar uma coleção com indexação "Todos os Intervalos" para Order By em todas as propriedades numéricas ou de cadeia de caracteres que aparecem em documentos JSON dentro dela. Aqui, substituímos o tipo de índice padrão para valores de cadeia de caracteres para Intervalo e com a precisão máxima (-1).

    DocumentCollection books = new DocumentCollection();
    books.Id = "books";
    books.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), books);  

> [!NOTE]
> Observe que Order By só retornará resultados dos tipos de dados (cadeia de caracteres e número) indexados com um RangeIndex. Por exemplo, se você tiver a política de indexação padrão que só tem RangeIndex em números, um Order By em um caminho com valores de cadeia de caracteres não retornará nenhum documento.
> 
> 

### <a name="indexing-for-order-by-for-a-single-property"></a>Indexação de Order By para uma única propriedade.
É possível criar uma coleção com indexação Order By em relação a qualquer propriedade de Título, que seja uma cadeia de caracteres. Há dois caminhos, um para a propriedade de Título ("/Title/?") com indexação por Intervalo e outro para todas as outra propriedade com o esquema de indexação padrão, que é Hash para cadeias de caracteres e Intervalo para números.                    

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), booksCollection);  


## <a name="samples"></a>Exemplos
Vejamos este [projeto de amostras do Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) que demonstra como usar Order By, incluindo a criação de políticas de indexação e paginação usando Order By. Os exemplos são de software livre e nós o encorajamos a enviar solicitações pull com contribuições que poderiam beneficiar outros desenvolvedores do Banco de Dados de Documentos. Consulte as [Diretrizes de contribuição](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obter instruções sobre como contribuir.  

## <a name="faq"></a>FAQ
**O que é o consumo de Unidade de Solicitação (RU) esperado de consultas Order By?**

Como Order By utiliza o índice de Banco de Dados de Documentos para pesquisas, o número de unidades de solicitação consumidas por consultas de Order By será semelhante às consultas equivalentes sem Order By. Como qualquer outra operação no Banco de Dados de Documentos, o número de unidades de solicitação depende do tamanho/forma dos documentos, bem como da complexidade da consulta. 

**Qual é a sobrecarga de indexação esperada para Order By?**

A sobrecarga de armazenamento de indexação será proporcional ao número de propriedades. Na pior das hipóteses, a sobrecarga de índice será igual a 100% dos dados. Não há nenhuma diferença na sobrecarga de taxa de transferência (Unidades de Solicitação) entre a indexação de intervalo/Order By e a indexação de Hash padrão.

**Como faço para consultar os meus dados existentes no Banco de Dados de Documentos usando Order By**

Para classificar os resultados da consulta usando Order By, você deve modificar a política de indexação da coleção a fim de usar um tipo de indexação de Intervalo em relação a propriedade usada para classificar. Consulte [Modificando a política indexação](documentdb-indexing-policies.md#modifying-the-indexing-policy-of-a-collection). 

**Quais são as limitações atuais de Order By?**

Order By pode ser especificado somente em uma propriedade, seja de valores numéricos ou cadeia de caracteres, quando o intervalo é indexado com a precisão máxima (-1).

Não é possível executar o seguinte:

* Order By com propriedades de cadeia de caracteres interna, como id, _rid e _self (em breve). 
* Order By com propriedades derivadas do resultado de uma junção intradocumento (em breve).
* Order By com múltiplas propriedades (em breve).
* Order By com consultas em bancos de dados, coleções, usuários, permissões ou anexos (em breve).
* Order By com as propriedades calculadas, por exemplo, o resultado de uma expressão ou uma função UDF/incorporada.

No momento, não há suporte para Order By em consultas entre partições ao usar o Gerenciador de Consultas no portal do Azure.

## <a name="troubleshooting"></a>Solucionar problemas
Se você receber um erro informando que não há suporte para Order By, verifique se você está usando uma versão do [SDK](documentdb-sdk-dotnet.md) que dá suporte a Order By. 

## <a name="next-steps"></a>Próximas etapas
Ramifique o [projeto de amostras do Github](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries) e comece a ordenar os seus dados! 

## <a name="references"></a>Referências
* [Referência de consulta do Banco de Dados de Documentos](documentdb-sql-query.md)
* [Referência de políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md)
* [Referência de SQL do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Amostras de Order By do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/code-samples/Queries)


