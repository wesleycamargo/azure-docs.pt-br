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
	ms.date="06/04/2015" 
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
Por exemplo, aqui está uma consulta para recuperar os registros em ordem decrescente de PublishTimestamp.

    SELECT * 
    FROM Books 
    ORDER BY Books.PublishTimestamp DESC

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

O Banco de Dados de Documentos oferece suporte à ordenação em relação aos tipos numéricos (não cadeias de caracteres) e somente para uma única propriedade Order By por consulta nesta visualização do recurso. Consulte [Qual é o próximo passo](#Whats_coming_next) para obter mais detalhes.

## Configurar uma política de indexação para Order By
Para executar consultas de Order By, é preciso:

- Indexar caminhos específicos em documentos com precisão máxima, (ou) 
- Indexar *todos* os caminhos recursivamente para toda a coleção com precisão máxima 

A precisão máxima (representada como precisão de -1 na configuração JSON) utiliza um número variável de bytes, dependendo do valor que está sendo indexado. Portanto:

- As propriedades com valores numéricos maiores, por exemplo, os carimbos de data/hora, a precisão máxima terá uma grande sobrecarga de índice. 
- As propriedades com valores numéricos menores (enumerações, zeros, códigos postais, idades, etc.), terão uma sobrecarga de índice baixa.

Para obter mais detalhes, consulte [Políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md).

### Indexação de Order By em relação a todas as propriedades numéricas
É possível criar uma coleção com indexação de Order By em relação a qualquer propriedade (numérica) da seguinte maneira.
                   

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range, 
            Path = "/",
            NumericPrecision = -1 });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

### Indexação de Order By para uma única propriedade.
É possível criar uma coleção com indexação de Order By em relação a qualquer propriedade (numérica) da seguinte maneira.

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range,
            Path = "/"PublishTimestamp"/?",
            NumericPrecision = -1
        });

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            Path = "/"
        });


## Exemplos
Vejamos este [projeto de amostras do Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) que demonstra como usar Order By, incluindo a criação de políticas de indexação e paginação usando Order By. Os exemplos são de software livre e nós o encorajamos a enviar solicitações pull com contribuições que poderiam beneficiar outros desenvolvedores do Banco de Dados de Documentos. Consulte as [Diretrizes de contribuição](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obter instruções sobre como contribuir.

## Qual é o próximo passo?

Atualizações futuras de serviços se aprofundarão no suporte de Order By apresentado aqui. Estamos trabalhando nas seguintes adições e priorizaremos o lançamento desses aprimoramentos com base nos seus comentários:

- Políticas de indexação dinâmicas: Suporte para modificar a política de indexação após a criação de coleção
- Índices de intervalo de cadeia de caracteres: índice para oferecer suporte a consultas de intervalo (>, <, >=, <=) com base nos valores de cadeia de caracteres. Para oferecer suporte a isso, apresentaremos um novo esquema mais rico para indexação de políticas.
- Suporte para Order By de cadeia de caracteres na consulta de Banco de Dados de Documentos.
- Capacidade de atualizar a política de indexação usando o Portal de Visualização do Azure.
- Suporte para Índices Compostos para Order By e Order By em várias propriedades mais eficiente.

## Perguntas frequentes

**Quais plataformas/versões do SDK oferecem suporte à ordenação?**

Como Order By é uma atualização do servidor, não é necessário fazer o download de uma nova versão do SDK para usar esse recurso. Todas as plataformas e versões do SDK, incluindo o SDK do JavaScript no servidor, podem usar Order By usando cadeias de caracteres de consulta SQL. Se você estiver usando o LINQ, deve baixar a versão 1.2.0 ou mais recente do Nuget.

**O que é o consumo de Unidade de Solicitação (RU) esperado de consultas Order By?**

Como Order By utiliza o índice de Banco de Dados de Documentos para pesquisas, o número de unidades de solicitação consumidas por consultas de Order By será semelhante às consultas equivalentes sem Order By. Como qualquer outra operação no Banco de Dados de Documentos, o número de unidades de solicitação depende do tamanho/forma dos documentos, bem como da complexidade da consulta.


**Qual é a sobrecarga de indexação esperada para Order By?**

A sobrecarga de armazenamento de indexação será proporcional ao número de propriedades numéricas. Na pior das hipóteses, a sobrecarga de índice será igual a 100% dos dados. Não há nenhuma diferença na sobrecarga de taxa de transferência (Unidades de Solicitação) entre a indexação de intervalo/Order By e a indexação de Hash padrão.

**Essa alteração afeta as consultas sem Order By?**

Não há nenhuma alteração na maneira como as consultas sem Order By funcionam hoje. Antes do lançamento desse recurso, todas as consultas do Banco de Dados de Documentos retornaram resultados em ordem de ResourceId (_rid). Com Order By, as consultas serão naturalmente retornadas na ordem de valores especificada. Em consultas de Order By, _rid será usado como ordem de classificação secundária quando houver vários documentos retornados com o mesmo valor.

**Como faço para consultar os meus dados existentes no Banco de Dados de Documentos usando Order By**

Esse recurso receberá suporte com a disponibilidade da melhoria das Políticas de Indexação Dinâmica mencionada na seção [Qual é o próximo passo](what's-coming-next). Para fazer isso hoje, é preciso exportar os seus dados e reimportá-los em uma nova coleção do Banco de Dados de Documentos criada com um índice de intervalo/Order By. A ferramenta de importação do Banco de Dados de Documentos pode ser usada para migrar dados entre coleções.

**Quais são as limitações atuais de Order By?**

Order By pode ser especificado somente em uma propriedade numérica e somente quando for indexado por intervalo com indexação de precisão máxima (-1). Order By é suportado apenas com coleções de documento.

Não é possível executar o seguinte:
 
- Order By com propriedades de cadeia de caracteres (em breve).
- Order By com propriedades de cadeia de caracteres interna, como id, _rid e _self (em breve). - Order By com propriedades derivadas do resultado de uma junção intradocumento (em breve).
- Order By com múltiplas propriedades (em breve).
- Order By com as propriedades calculadas, por exemplo, o resultado de uma expressão ou uma função UDF/incorporada.
- Order By com consultas em bancos de dados, coleções, usuários, permissões ou anexos.

## Próximas etapas

Ramifique o [projeto de amostras do Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) e comece a ordenar os seus dados!

## Referências
* [Referência de consulta do Banco de Dados de Documentos](documentdb-sql-query.md)
* [Referência de políticas de indexação do Banco de Dados de Documentos](documentdb-indexing-policies.md)
* [Referência de SQL do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Amostras de Order By do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=62-->