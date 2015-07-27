<properties 
	pageTitle="Classificando dados do Banco de Dados de Documentos usando Order By | Azure" 
	description="Saiba como usar ORDER BY em consultas do Banco de Dados de Documentos em LINQ e SQL, e como especificar uma política de indexação para consultas de ORDER BY." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/06/2015" 
	ms.author="arramac"/>

# Classificando dados do Banco de Dados de Documentos usando Order By
O Banco de Dados de Documentos do Microsoft Azure tem suporte para a realização da consulta de documentos utilizando uma SQL em documentos JSON. Os resultados de consulta podem ser ordenados usando a cláusula ORDER BY nas instruções de consulta SQL.

Após ler este artigo, você poderá responder as perguntas a seguir:

- Como consultar com Order By?
- Como configurar uma política de indexação para Order By?
- Qual é o próximo passo?

Também são fornecidas [amostras](#samples), além de um [FAQ](#faq).

Para obter uma referência completa sobre consultas SQL, consulte o [tutorial de Consulta do Banco de Dados de Documentos](documentdb-sql-query.md).

## Como consultar com Order By
Como no ANSI-SQL, agora você pode incluir uma cláusula opcional Order By em instruções SQL ao consultar o Banco de Dados de Documentos. A cláusula pode incluir um argumento ASC/DESC opcional para especificar a ordem na qual os resultados devem ser recuperados.

### Ordenação usando SQL
Por exemplo, aqui está uma consulta para recuperar registros em ordem decrescente de títulos.

    SELECT * 
    FROM Books 
    ORDER BY Books.Title DESC

### Ordenação usando SQL com filtragem
É possível ordenar usando qualquer propriedade aninhada dentro de documentos como Books.ShippingDetails.Weight e especificar filtros adicionais na cláusula WHERE em combinação com Order By, como neste exemplo:

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### Ordenação usando o provedor LINQ para .NET
Usando o SDK do .NET versão 1.2.0 e superior, você também pode usar a cláusula OrderBy() ou OrderByDescending() em consultas LINQ, como neste exemplo:

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### Ordenação com paginação usando o SDK do .NET
Usando o suporte nativo de paginação dentro dos SDKs do Banco de Dados de Documentos, é possível recuperar uma página de resultados por vez, como no seguinte trecho de código do .NET. Aqui buscamos até 10 resultados por vez usando o FeedOptions.MaxItemCount e a interface IDocumentQuery.

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

O Banco de Dados de Documentos dá suporte à ordenação com uma propriedade numérica simples, de cadeia de caracteres ou booliana por consulta, com tipos de consulta adicionais que chegarão em breve. Consulte [Qual é o próximo passo](#Whats_coming_next) para obter mais detalhes.

## Configurar uma política de indexação para Order By

Lembre-se de que o Banco de Dados de Documentos dá suporte a dois tipos de índices (Hash e Intervalo), que podem ser definidos para caminhos/propriedades específicos, tipos de dados (cadeias de caracteres/números) e valores de precisão diferente (precisão máxima ou um valor de precisão fixa). Como o Banco de Dados de Documentos usa como padrão a indexação Hash, você deve criar uma nova coleção com uma política de indexação personalizada com Intervalo em números, cadeias de caracteres ou ambos, para usar Order By.

>[AZURE.NOTE]Índices de intervalo de cadeia de caracteres foram introduzidos em 7 de julho de 2015 com a API REST versão 2015-06-03. Para criar políticas de Order By em cadeias de caracteres, você deve usar a versão SDK 1.2.0 do .NET ou a versão 1.1.0 do Python, Node. js ou Java.
>
>Antes da versão 2015-06-03 da API REST, a política de indexação de coleção padrão era Hash para cadeias de caracteres e números. Isso foi alterado para Hash para cadeias de caracteres e Intervalo para números.

Para obter mais detalhes, consulte [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md).

### Indexação de Order By em relação a todas as propriedades
Aqui está como você pode criar uma coleção com indexação "Todos os Intervalos" para Order By em todas as propriedades numéricas ou de cadeia de caracteres que aparecem em documentos JSON dentro dela. Aqui, "/ *" representa todas as propriedades JSON/caminhos dentro da coleção e -1 representa a precisão máxima.
                   
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

>[AZURE.NOTE]Observe que Order By só retornará resultados dos tipos de dados (cadeia de caracteres e número) indexados com um RangeIndex. Por exemplo, se você tiver a política de indexação padrão que só tem RangeIndex em números, um Order By em um caminho com valores de cadeia de caracteres não retornará nenhum documento.

### Indexação de Order By para uma única propriedade.
É possível criar uma coleção com indexação Order By em relação a qualquer propriedade de Título, que seja uma cadeia de caracteres. Há dois caminhos, um para a propriedade de Título ("/Title/?") com indexação por Intervalo e outro para todas as outra propriedade com o esquema de indexação padrão, que é Hash para cadeias de caracteres e Intervalo para números.
    
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 } } 
            });
    
    // Use defaults which are:
    // (a) for strings, use Hash with precision 3 (just equality queries)
    // (b) for numbers, use Range with max precision (for equality, range and order by queries)
    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*",
            Indexes = new Collection<Index> { 
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }            
        });

## Exemplos
Vejamos este [projeto de amostras do Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) que demonstra como usar Order By, incluindo a criação de políticas de indexação e paginação usando Order By. Os exemplos são de software livre e nós o encorajamos a enviar solicitações pull com contribuições que poderiam beneficiar outros desenvolvedores do Banco de Dados de Documentos. Consulte as [Diretrizes de contribuição](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obter instruções sobre como contribuir.

## Qual é o próximo passo?

Atualizações futuras de serviços se aprofundarão no suporte de Order By apresentado aqui. Estamos trabalhando nas seguintes adições e priorizaremos o lançamento desses aprimoramentos com base nos seus comentários:

- Políticas de indexação dinâmica: suporte para modificar a política de indexação após a criação de coleção e no portal do Azure
- Suporte para Índices Compostos para Order By e Order By em várias propriedades mais eficiente.

## Perguntas frequentes

**Quais plataformas/versões do SDK oferecem suporte à ordenação?**

Para criar coleções com a política de indexação exigida para Order By, você deve baixar a versão mais recente do SDK (1.2.0 para .NET e 1.1.0 para Node.js, JavaScript, Python e Java). O SDK do .NET 1.2.0 também é necessário para usar OrderBy() e OrderByDescending() em expressões de LINQ.


**O que é o consumo de Unidade de Solicitação (RU) esperado de consultas Order By?**

Como Order By utiliza o índice de Banco de Dados de Documentos para pesquisas, o número de unidades de solicitação consumidas por consultas de Order By será semelhante às consultas equivalentes sem Order By. Como qualquer outra operação no Banco de Dados de Documentos, o número de unidades de solicitação depende do tamanho/forma dos documentos, bem como da complexidade da consulta.


**Qual é a sobrecarga de indexação esperada para Order By?**

A sobrecarga de armazenamento de indexação será proporcional ao número de propriedades. Na pior das hipóteses, a sobrecarga de índice será igual a 100% dos dados. Não há nenhuma diferença na sobrecarga de taxa de transferência (Unidades de Solicitação) entre a indexação de intervalo/Order By e a indexação de Hash padrão.

**Como faço para consultar os meus dados existentes no Banco de Dados de Documentos usando Order By**

Esse recurso receberá suporte com a disponibilidade da melhoria das Políticas de Indexação Dinâmica mencionada na seção [Qual é o próximo passo](what's-coming-next). Para fazer isso hoje, é preciso exportar os seus dados e reimportá-los em uma nova coleção do Banco de Dados de Documentos criada com um índice de intervalo/Order By. A ferramenta de importação do Banco de Dados de Documentos pode ser usada para migrar dados entre coleções.

**Quais são as limitações atuais de Order By?**

Order By pode ser especificado somente em uma propriedade, seja de valores numéricos ou cadeia de caracteres, quando o intervalo é indexado com a precisão máxima (-1).

Não é possível executar o seguinte:
 
- Order By com propriedades de cadeia de caracteres interna, como id, _rid e _self (em breve). - Order By com propriedades derivadas do resultado de uma junção intradocumento (em breve).
- Order By com múltiplas propriedades (em breve).
- Order By com consultas em bancos de dados, coleções, usuários, permissões ou anexos (em breve).
- Order By com as propriedades calculadas, por exemplo, o resultado de uma expressão ou uma função UDF/incorporada.

## Próximas etapas

Ramifique o [projeto de amostras do Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) e comece a ordenar os seus dados!

## Referências
* [Referência de consulta do Banco de Dados de Documentos](documentdb-sql-query.md)
* [Referência de políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md)
* [Referência de SQL do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Amostras de Order By do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=July15_HO3-->