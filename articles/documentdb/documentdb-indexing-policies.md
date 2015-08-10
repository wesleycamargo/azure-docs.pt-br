<properties 
    pageTitle="Políticas de Indexação do Banco de Dados de Documentos | Azure" 
    description="Compreenda como a indexação funciona no Banco de Dados de Documentos e saiba como configurar a política de indexação." 
    services="documentdb" 
    documentationCenter="" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="07/19/2015" 
    ms.author="mimig"/>


# Políticas de indexação do Banco de Dados de Documentos

O Banco de Dados de Documentos é um verdadeiro banco de dados livre de esquema. Ele não assume nem requer qualquer esquema para os documentos JSON que indexa. Isso permite que você defina rapidamente e itere em modelos de dados de aplicativo. Ao incluir documentos em uma coleção, o Banco de Dados de Documentos indexa todas as propriedades de documento automaticamente e elas ficam disponíveis para consulta. A indexação automática permite armazenar tipos heterogêneos de documentos.

A indexação automática de documentos é habilitada por técnicas de manutenção de índice de gravação otimizada, livre de bloqueio e log estruturadas. O Banco de Dados de Documentos oferece suporte a um volume permanente de gravações rápidas ao mesmo tempo em que oferece consultas consistentes.

O subsistema de indexação do Banco de Dados de Documentos foi projetado para oferecer suporte a:

-  Pesquisas hierárquicas e relacionais ricas e eficientes sem quaisquer definições de esquema ou índice.
-  Resultados de consulta consistentes ao manipular um volume prolongado de gravações. Para possibilitar cargas de trabalho com alta produtividade de gravação com consultas consistentes, o índice é atualizado gradativamente, eficientemente e online, ao manipular um volume prolongado de gravações.
- Eficiência do armazenamento. Para manter um bom custo-benefício, a sobrecarga do armazenamento em disco do índice é vinculada e previsível.
- Multilocação. As atualizações do índice são realizadas dentro do orçamento dos recursos do sistema alocados por coleção de documento. 

Para a maioria dos aplicativos, você pode usar a diretiva de indexação automática padrão já que ela permite mais flexibilidade e compensações perfeitas entre desempenho e eficiência de armazenamento. Por outro lado, especificar uma política de indexação personalizada permite que você faça compensações granulares entre desempenho de pesquisa, desempenho de gravação e sobrecarga de armazenamento do índice.

Por exemplo, excluindo certos documentos ou caminhos nos documentos da indexação, você pode reduzir o espaço armazenamento usado para indexação, bem como o custo de tempo de inserção para manutenção de índice. Você pode alterar o tipo do índice mais adequado para consultas de intervalo ou aumentar a precisão de índice em bytes para melhorar o desempenho da consulta. Este artigo descreve diferentes configurações de indexação disponíveis no Banco de Dados de Documentos e como personalizar a política de indexação para suas cargas de trabalho.

Após ler este artigo, você poderá responder as perguntas a seguir:

- Como o Banco de Dados de Documentos oferece suporte a indexação de todas as propriedades por padrão?
- Como substituir as propriedades a serem incluídas ou excluídas da indexação?
- Como configurar o índice para eventuais atualizações?
- Como configurar a indexação para executar consultas Order By ou de intervalo?

## Como funciona a indexação do Banco de Dados de Documentos

A indexação do Banco de Dados de Documentos aproveita-se que a gramática JSON permite que os documentos sejam **representados como árvores**. Para um documento JSON ser representado como uma árvore, precisa ser criado um nó raiz fictício que seja pai do restante de nós reais no documento abaixo. Cada rótulo, inclusive os índices da matriz em um documento JSON torna-se um nó da árvore. A figura a seguir ilustra um documento JSON de exemplo e sua representação de árvore correspondente.

![Políticas de indexação](media/documentdb-indexing-policies/image001.png)

Por exemplo, a propriedade JSON `{"headquarters": "Belgium"}` no exemplo acima corresponde ao caminho `/headquarters/Belgium`. A matriz JSON `{"exports": [{"city": “Moscow"}, {"city": Athens"}]}` corresponde aos caminhos `/exports/[]/city/Moscow` e `/exports/[]/city/Athens`.

>[AZURE.NOTE]A representação de caminho confunde os limites entre a estrutura/esquema e os valores de instância em documentos, permitindo que o Banco de Dados de Documentos seja realmente livre de esquema.

No Banco de Dados de Documentos, os documentos são organizados em coleções que podem ser consultadas usando o SQL ou processadas dentro do escopo de uma única transação. Cada coleção pode ser configurada com sua própria política de indexação expressada em termos de caminhos. Na seção a seguir, veremos como configurar o comportamento de indexação de uma coleção do Banco de Dados de Documentos.

## Configurando a política de indexação de uma coleção

Para cada coleção de Banco de Dados de Documentos, você pode configurar as seguintes opções:

- Modo de indexação: **Consistente**, **Lento** (para atualizações assíncronas) ou **Nenhum** (acesso com base apenas "id")
- Caminhos incluídos ou excluídos: escolha quais caminhos em JSON são incluídos ou excluídos
- Tipo de índice: **Hash** (para consultas de igualdade), **Intervalo** (para consultas de igualdade, intervalo e ordenar por com armazenamento mais alto)
- Precisão de índice: 1 a 8 ou máximo (-1) para o equilíbrio entre desempenho e armazenamento
- Automático: **true** ou **false** para habilitar ou **manual** (aceitação com cada inserção)

O seguinte trecho de código .NET mostra como definir uma política de indexação personalizada durante a criação de uma coleção. Aqui, definimos a política com índice de intervalo de cadeias de caracteres e números à precisão máxima. Essa política nos permite executar consultas de Ordenar por com relação a cadeias de caracteres.

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE]O esquema JSON para política de indexação foi alterado com o lançamento da versão 2015-06-03 da API REST para dar suporte a índices de intervalo nas cadeias de caracteres. .NET SDK 1.2.0 e Java, Python e SDKs do Node. js 1.1.0 oferecem suporte para o novo esquema de política. SDKs mais antigos usam a API REST versão 2015-04-08 e dão suporte ao esquema mais antigo da política de indexação.
>
>Por padrão, Banco de Dados de Documentos indexa todas as propriedades da cadeia de caracteres dentro de documentos de forma consistente com um índice de Hash e propriedades numéricas com um índice de intervalo.

### Modos de Indexação

Escolha entre atualizações de índice síncronas (\*\*Consistentes\*\*), assíncronas (\*\*Lentas\*\*) e ausentes (\*\*Nenhuma\*\*). Por padrão, o índice é atualizado sincronamente em cada ação de inserção, substituição ou exclusão realizada em um documento na coleção. Isso permite que as consultas obedeçam ao mesmo nível de consistência das leituras de documentos sem demora para o índice atualizado.

Embora o Banco de Dados de Documentos seja otimizado para gravação e dê suporte a volumes constantes de gravações de documentos junto com a manutenção síncrona de índice e atendimento a consultas consistentes, você pode configurar determinadas coleções para atualizar seu índice, sem pressa. A indexação lenta é excelente para situações em que dados são gravados em picos e deseja amortizar o trabalho necessário para indexar o conteúdo em um período mais longo de tempo. Isso permite que você use a taxa de transferência provisionada com eficiência e atenda solicitações de gravação em horários de pico com latência mínima. Com a indexação lenta ativada, os resultados de consultas serão acabarão sendo consistentes, não importa o nível de consistência configurado para a conta de banco de dados.

O exemplo a seguir mostra como criar uma coleção do Banco de Dados de Documentos usando o SDK do .NET com indexação automática consistente em todas as inserções de documentos.


     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     // Optional. Override Automatic to false for opt-in indexing of documents.
     collection.IndexingPolicy.Automatic = true;
     
     // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
     // collections. Queries might return stale results with Lazy indexing.
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);

### Caminhos de índice

Nos documentos, você pode escolher quais caminhos devem ser incluídos ou excluídos da indexação. Isso pode oferecer um melhor desempenho de gravação e menor armazenamento de índice para situações onde os padrões de consulta são previamente conhecidos.

Caminhos de índice começam com a raiz (/) e geralmente terminam com o operador de curinga ?, indicando que há vários valores possíveis para o prefixo. Por exemplo, para servir SELECT \* FROM Families F WHERE F.familyName = "Andersen", você deve incluir um caminho de índice para /familyName/? na política de índice da coleção.

Caminhos de índice também podem usar o \* operador curinga para especificar o comportamento de caminhos recursivamente sob o prefixo. Por exemplo, /payload/\* pode ser usado para excluir tudo sob a propriedade de carga da indexação.

Estes são os padrões comuns para especificar caminhos de índice:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Caminho</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Descrição/caso de uso</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho padrão para coleta. Recursiva e aplica-se em toda árvore do documento inteiro.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho de índice necessário para fazer consultas como as seguintes (com tipos hash e de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho de índice para todos os caminhos sob o rótulo especificado. Funciona com as seguintes consultas
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de escalares como ["a", "b", "c"]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de objetos como [{subprop: "a"}, {subprop: "b"}]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Caminho de índice necessário para fazer consultas (com tipos hash e de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]Ao definir caminhos de índice personalizados, é necessário especificar a regra de indexação padrão para a árvore de todo o documento indicada pelo caminho especial "/\*".

O exemplo a seguir configura um caminho específico com a indexação de intervalo e um valor personalizado de precisão de 20 bytes:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### Tipos, modelos e precisões de dados

Agora que já vimos como especificar caminhos, vamos examinar as opções que podemos usar para configurar a política de indexação para um caminho. Você pode especificar uma ou mais definições de indexação para cada caminho:

- Tipo de dados: **Sequência** ou **Número** (pode conter apenas uma entrada por tipo de dado por caminho)
- Tipo de índice: **Hash** (consultas de igualdade) ou **Intervalo** (consultas de igualdade, intervalo ou Ordenar por)
- Precisão: 1 a 8 ou -1 (precisão máxima) para os números de 1 a 100 (precisão máxima) para a cadeia de caracteres

#### Tipo de índice

O Banco de Dados de Documentos dá suporte a dois tipos de índice para cada par de tipo de dados e caminho.

- **Hash** dá suporte a consultas de igualdade eficientes. Na maioria dos casos de uso, os índices de hash não precisam de uma precisão maior que o valor padrão de 3 bytes.
- **Intervalo** dá suporte a consultas de igualdade eficientes, consultas de intervalo (usando >, <>, =, < =,! =) e as consultas de Ordenar por. Por padrão, as consultas Ordenar por também exigem a precisão máxima de índice (-1).

#### Precisão de índice

A precisão de índice permite definir um equilíbrio entre a sobrecarga de armazenamento de índice e o desempenho da consulta. Para números, recomendamos usar a configuração de precisão padrão de -1. Como os números são 8 bytes em JSON, isso é equivalente a uma configuração de 8 bytes. Escolher um valor mais baixo para precisão, como 1 a 7, significa que os valores dentro de alguns intervalos podem ser mapeados para a mesma entrada de índice. Portanto, você reduzirá o espaço de armazenamento do índice, mas a execução da consulta talvez precise processar mais documentos e, como consequência, consumirá mais taxa de transferência, ou seja, unidades de solicitação.

A configuração de precisão do índice é praticamente mais útil com intervalos de cadeia de caracteres. Como cadeias de caracteres podem ter qualquer comprimento arbitrário, a escolha de precisão do índice pode afetar o desempenho de consultas de intervalo de cadeia de caracteres e a quantidade de espaço de armazenamento de índice necessária. Os índices de intervalo de cadeia de caracteres podem ser configurados com 1-100 ou o valor de precisão máxima (-1). Se você precisar de Ordenar por em cadeias de caracteres, deve definir sobre o caminho especificado (-1).

O exemplo a seguir mostra como aumentar a precisão de índices de intervalo em uma coleção usando o SDK do .NET. Observe que isso usa o caminho padrão "/\*".

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


> [AZURE.NOTE]O Banco de Dados de Documentos retorna um erro quando uma consulta usa um Ordenar por, mas não tem um índice de intervalo do caminho consultado com a precisão máxima.
>
> Um erro é retornado para consultas com operadores de intervalo como > = se não houver nenhum índice de intervalo (de qualquer precisão), mas elas poderão ser atendidas se houver outros filtros que possam ser atendidos por meio do índice.
> 
> Consultas de intervalo podem ser executadas sem um índice de intervalo usando o cabeçalho x-ms-documentdb-enable-scans na API REST ou na opção de solicitação EnableScanInQuery usando o SDK do .NET.

Da mesma forma, caminhos podem ser excluídos completamente da indexação. O exemplo a seguir mostra como excluir uma seção inteira de documentos (também conhecida como uma subárvore) de indexação usando o curinga "\*".

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


### Indexação automática

Você pode escolher se deseja que a coleção indexe automaticamente todos os documentos. Por padrão, todos os documentos são indexados automaticamente, mas você pode optar por desativá-lo. Quando a indexação estiver desativada, documentos podem ser acessados somente por meio de seus self links ou através de consultas usando um ID.

Com a indexação automática desativada, você ainda pode adicionar seletivamente somente documentos específicos para o índice. Por outro lado, você pode deixar a indexação automática e seletivamente optar por excluir apenas documentos específicos. As configurações de Ativar/desativar a indexação são úteis quando você tem apenas um subconjunto dos documentos que precisa ser consultado.

Por exemplo, o exemplo a seguir mostra como incluir um documento explicitamente usando o [SDK DocumentDB.NET](https://github.com/Azure/azure-documentdb-java) e a propriedade [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## Ajuste de desempenho

As APIs do Banco de Dados de Documentos fornecem informações sobre as métricas de desempenho, como o armazenamento de índice usado e o custo da taxa de transferência (unidades de solicitação) para cada operação. Essas informações podem ser usadas para comparar várias políticas de indexação e para ajuste de desempenho.

Para verificar se a cota de armazenamento e o uso de uma coleção, execute uma solicitação HEAD ou GET em relação ao recurso de coleção e inspecione os cabeçalhos x-ms-request-quota e x-ms-request-usage. No SDK do .NET, as propriedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) em [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) contêm esses valores correspondentes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Para medir a sobrecarga de indexação em cada operação de gravação (criar, atualizar ou excluir), inspecione o cabeçalho x-ms-request-charge (ou a propriedade equivalente [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) em [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) no SDK do .NET) para medir o número de unidades de solicitação consumidas por essas operações.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## Alterações à especificação da política de indexação
Uma alteração no esquema da política de indexação foi introduzida em 7 de julho de 2015 com a versão 2015-06-03 da API REST. As classes correspondentes nas versões do SDK têm novas implementações para corresponder ao esquema.

As seguintes alterações foram implementadas na especificação JSON:

- A política de indexação dá suporte a índices de intervalo para cadeias de caracteres
- Cada caminho pode ter várias definições de índice, um para cada tipo de dados
- A indexação de precisão dá suporte a 1-8 para números de 1-100 para cadeias de caracteres e -1 (precisão máxima)
- Segmentos de caminhos não exigem aspas duplas para cada caminho de escape. Por exemplo, você pode adicionar um caminho /title/? em vez de /"title"/?
- O caminho raiz representando "todos os caminhos" pode ser representado como /\* (além de /)

Se você tiver o código que provisiona coleções com uma política de indexação personalizada gravada com versão 1.1.0 do SDK do .NET ou anterior, precisará alterar o código do aplicativo para lidar com essas alterações e mover para a versão 1.2.0 do SDK. Se você não tiver um código que configura a política de indexação ou planeja continuar usando uma versão anterior do SDK, nenhuma alteração será necessária.

Para uma comparação prática, aqui está um exemplo de política de indexação personalizada escrita usando a API REST versão 2015-06-03, bem como a versão 2015-04-08 anterior.

**Política de indexação anterior do JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/"nonIndexedContent"/*"
       ]
    }

**Política de indexação atual do JSON**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## Próximas etapas

Siga os links abaixo para ver exemplos de gerenciamento de políticas de índice e para saber mais sobre a linguagem de consulta do Banco de Dados de Documentos.

1.	[Exemplos de código de gerenciamento de índice .NET do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[Operações de coleção de API REST do Banco de Dados de Documentos](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[Consulta com SQL do Banco de Dados de Documentos](documentdb-sql-query.md)

 

<!---HONumber=July15_HO5-->