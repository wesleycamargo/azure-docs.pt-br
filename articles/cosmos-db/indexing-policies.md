---
title: "Políticas de indexação do Azure Cosmos DB | Microsoft Docs"
description: "Entenda como funciona a indexação no Azure Cosmos DB. Saiba como configurar e alterar a política de indexação para indexação automática e um melhor desempenho."
keywords: "como funciona a indexação, indexação automática, banco de dados de indexação"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: b09f5323f0378721412baade9be9926ebd0c171e
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="how-does-azure-cosmos-db-index-data"></a>Como o Azure Cosmos DB indexa dados?

Por padrão, todos os dados do Azure Cosmos DB são indexados. Embora muitos clientes estejam satisfeitos em permitir que o Azure Cosmos DB identifique automaticamente todos os aspectos da indexação, é possível especificar uma *política de indexação* personalizada para coleções durante a criação no Azure Cosmos DB. As políticas de indexação no Azure Cosmos DB são mais flexíveis e mais eficientes que os índices secundários oferecidos em outras plataformas de banco de dados. No Azure Cosmos DB, é possível projetar e personalizar a forma do índice sem sacrificar a flexibilidade do esquema. 

Para saber como a indexação funciona no Azure Cosmos DB, é importante entender que ao gerenciar a política de indexação será possível uma compensação refinada entre sobrecarga de armazenamento de índice, taxa de transferência de consulta e gravação e consistência de consultas.  

Nesse artigo, examinamos de detalhadamente as políticas de indexação do Azure Cosmos DB, como personalizar a política de indexação e as compensações associadas. 

Após ler este artigo, você poderá responder as perguntas a seguir:

* Como substituir as propriedades a serem incluídas ou excluídas da indexação?
* Como configurar o índice para eventuais atualizações?
* Como configurar a indexação para executar consultas ORDER BY ou de intervalo?
* Como fazer alterações à política de indexação de uma coleção?
* Como posso comparar armazenamento e desempenho de políticas de indexação diferentes?

## Personalizar a política de indexação de uma coleção <a id="CustomizingIndexingPolicy"></a>  
É possível personalizar as compensações entre armazenamento, desempenho de consultas e gravação e consistência de consultas, substituindo a política de indexação padrão em uma coleção do Azure Cosmos DB. É possível configurar os seguintes aspectos:

* **Incluir ou excluir documentos e caminhos do e para o índice**. É possível excluir ou incluir documentos específicos no índice ao inserir ou substituir os documentos na coleção. Você também pode incluir ou excluir propriedades específicas JSON, também chamadas de *caminhos*, para indexar documentos incluídos em um índice. Os caminhos incluem padrões curinga.
* **Configurar vários tipos de índice**. Para cada caminho incluído, é possível especificar o tipo de índice que o caminho requer para uma coleção. Além disso, você pode especificar o tipo de índice com base nos dados do caminho, na carga de trabalho de consulta esperada e na “precisão” da cadeia de caracteres/numérica.
* **Configurar os modos de atualização de índice**. O Azure Cosmos DB dá suporte a três modos de indexação: Consistente, Lento e Nenhum. É possível configurar os modos de indexação através da política de indexação em uma coleção do Azure Cosmos DB. 

O seguinte trecho de código Microsoft .NET mostra como configurar uma política de indexação personalizada ao criar uma coleção. Nesse exemplo, definimos a política com um índice do intervalo para cadeias de caracteres e números com a máxima precisão. É possível usar essa política para executar consultas ORDER BY com relação a cadeias de caracteres.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> O esquema JSON para política de indexação foi alterado com o lançamento da versão API REST 2015-06-03. Com esse lançamento, o esquema JSON para política de indexação dá suporte aos índices de intervalo com relação a cadeias de caracteres. O SDK 1.2.0 do .NET e os SDKs 1.1.0 do Java, Python e Node.js suportam o novo esquema de política. Versões anteriores do SDK usam a API REST versão 2015-04-08. Eles dão suporte ao esquema anterior para a política de indexação.
> 
> Por padrão, o Azure Cosmos DB indexa todas as propriedades de cadeia de caracteres dentro de documentos consistentemente com um índice de hash. Isso indexa todas as propriedades numéricas dentro dos documentos consistentemente com um índice do intervalo.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Personalizar a política de indexação no portal

Você pode alterar a política de indexação de uma coleção no Portal do Azure: 

1. No portal, acesse sua conta do Azure Cosmos DB e selecione a coleção. 
2. No menu de navegação à esquerda, selecione **Configurações** e, em seguida, selecione **Política de Indexação**. 
3. Em **Política de Indexação**, altere a política de indexação e selecione **OK**. 

### Modos de indexação do banco de dados <a id="indexing-modes"></a>  
O Azure Cosmos DB dá suporte a três modos de indexação que são possíveis de configurar através da política de indexação em uma coleção do Azure Cosmos DB: Consistente, Lento e Nenhum.

**Consistente**: se uma política de coleção do Azure Cosmos DB for Consistente, as consultas em uma coleção do Azure Cosmos DB específica seguirão o mesmo nível de consistência especificado para as leituras de pontos (forte, desatualização limitada, sessão ou eventual). O índice é atualizado de forma síncrona como parte da atualização do documento (inserir, substituir, atualizar e excluir um documento em uma coleção do Azure Cosmos DB).

A indexação consistente dá suporte a consultas consistentes ao custo de uma possível redução na taxa de transferência de gravação. Essa redução é uma função dos caminhos exclusivos que precisam ser indexados e do "nível de consistência." O modo de indexação consistente foi projetado para cargas de trabalho de "gravação rápida, consulta imediata”.

**Lento**:  o índice é atualizado de forma assíncrona quando uma coleção do Azure Cosmos DB está inativa, ou seja, quando a capacidade de taxa de transferência da coleção não é totalmente utilizada para atender às solicitações do usuário. O modo de indexação Lento pode ser adequado para cargas de trabalho "ingerir agora, consultar depois" que exigem a ingestão de documentos. Observe que é possível obter resultados inconsistentes devido aos dados sendo ingeridos e indexados lentamente. Isso significa que suas consultas COUNT ou resultados de consulta específica podem não ser consistentes ou repetitivos em um determinado momento. 

O índice geralmente está em modo de atualização com dados ingeridos. Com indexação em modo Lento, as mudanças de vida útil resultam em que o índice seja ignorado e recriado. Isso torna o COUNT e os resultados da consulta inconsistentes por um período de tempo. Devido a isso, a maioria das contas do Azure Cosmos DB devem usar o modo de indexação Consistente.

**Nenhum**: uma coleção que possui um modo de índice Nenhum não possui índices associados à coleção. Isso é comumente usado se o Azure Cosmos DB for usado como um armazenamento de chave-valor e os documentos são acessados apenas pela propriedade ID. 

> [!NOTE]
> Configurar a política de indexação como Nenhum tem o efeito colateral de ignorar qualquer índice existente. Use essa opção se seus padrões de acesso exigirem apenas ID ou self-link.
> 
> 

A tabela a seguir mostra a consistência para as consultas baseadas no modo de indexação (Consistente e Lento) configurado para a coleção e o nível de consistência especificado para a solicitação de consulta. Isso se aplica a consultas feitas usando qualquer interface: API REST, SDKs ou em gatilhos e procedimentos armazenados. 

|Consistência|Modo de indexação: Consistente|Modo de indexação: Lento|
|---|---|---|
|Strong|Strong|Eventual|
|Bounded staleness|Bounded staleness|Eventual|
|Session|Session|Eventual|
|Eventual|Eventual|Eventual|

O Azure Cosmos DB retorna um erro para as consultas feitas em coleções que possuem um modo de indexação Nenhum. As consultas ainda podem ser executadas como digitalizações através do cabeçalho explícito **x-ms-documentdb-enable-scan** na API REST ou na opção de solicitação **EnableScanInQuery** usando o .NET SDK. Alguns recursos de consulta, como ORDER BY, não têm suporte como digitalizações com **EnableScanInQuery**.

A tabela a seguir mostra a consistência para as consultas com base no modo de indexação (Consistente, Lento e Nenhum) quando **EnableScanInQuery**é especificado.

|Consistência|Modo de indexação: Consistente|Modo de indexação: Lento|Modo de indexação: Nenhum|
|---|---|---|---|
|Strong|Strong|Eventual|Strong|
|Bounded staleness|Bounded staleness|Eventual|Bounded staleness|
|Session|Session|Eventual|Session|
|Eventual|Eventual|Eventual|Eventual|

O exemplo de código a seguir mostra como criar uma coleção do Azure Cosmos DB usando o SDK do .NET com indexação consistente em todas as inserções de documentos.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Caminhos de índice
Documentos JSON dos modelos do Azure Cosmos DB e o índice como árvores. Você pode ajustar políticas para caminhos dentro da árvore. Nos documentos, é possível escolher os caminhos para incluir ou excluir da indexação. Isso pode oferecer um melhor desempenho de gravação e menor armazenamento de índice para cenários nos quais os padrões de consultas são previamente conhecidos.

Caminhos de índice começam com a raiz (/) e geralmente terminam operador curinga. Isso indica que há vários valores possíveis para o prefixo. Por exemplo, para servir SELECT * FROM Families F WHERE F.familyName = "Andersen", você deve incluir um caminho de índice para /familyName/? na política de índice da coleção.

Caminhos de índice também podem usar o operador curinga \* para especificar o comportamento de caminhos recursivamente sob o prefixo. Por exemplo, /payload/* pode ser usado para excluir tudo na propriedade de conteúdo da indexação.

Estes são os padrões comuns para especificar caminhos de índice:

| Caminho                | Descrição/caso de uso                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Caminho padrão para coleta. Recursivo e aplica-se a toda a árvore de documentos.                                                                                                                                                                                                                                   |
| /prop/?             | Caminho de índice necessário para atender consultas como as seguintes (com tipos Hash e de Intervalo, respectivamente):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /prop/*             | Caminho de índice para todos os caminhos sob o rótulo especificado. Funciona com as seguintes consultas<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de escalares como ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | O caminho do índice necessário para atender a consultas de JOIN e iteração em matrizes de objetos como [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Caminho de índice necessário para atender consultas (com tipos Hash ou Intervalo, respectivamente):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Ao configurar caminhos de índice personalizados, é necessário especificar a regra de indexação padrão para toda a árvore de documentos, que é indicada pelo caminho especial "/*". 
> 
> 

O exemplo a seguir configura um caminho específico com índice do Intervalo e um valor de precisão personalizado de 20 bytes:

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

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Tipos de dados, tipos de índices e precisões
Você possui várias opções quando configura a política de indexação para um caminho. Você pode especificar uma ou mais definições de indexação para cada caminho:

* **Tipo de dados**: Cadeia de caracteres, Número, Ponto, Polígono ou LineString (pode conter apenas uma entrada por tipo de dados por caminho).
* **Tipo de Índice**: Hash (consultas de igualdade), Intervalo (consultas ORDER BY, intervalo ou igualdade) ou Espacial (consultas espaciais) .
* **Precisão**: para um índice de Hash, isso varia de 1 a 8 para ambas as cadeias de caracteres e números. O padrão é 3. Para um índice do Intervalo, esse valor pode ser -1 (precisão máxima). Pode variar entre 1 e 100 (precisão máxima) para valores de números ou cadeia de caracteres.

#### <a name="index-kind"></a>Tipo de índice
O Azure Cosmos DB dá suporte ao índice de Hash e os tipos de índices Intervalo para cada caminho que pode ser configurado para tipos de dados de Número ou Cadeia de Caracteres, ou ambos.

* **Hash** dá suporte a consultas JOIN e de igualdade eficientes. Na maioria dos casos de uso, os índices de Hash não exigem uma precisão maior do que o valor padrão de 3 bytes. O tipo de dados pode ser Cadeia de Caracteres ou Número.
* **Intervalo** dá suporte a consultas de igualdade eficientes, consultas de intervalo (usando >, <, >=, <=, !=) e consultas ORDER BY. Por padrão,consultas ORDER By também requerem precisão máxima de índice (-1). O tipo de dados pode ser Cadeia de Caracteres ou Número.

O Azure Cosmos DB também dá suporte ao tipo de índice Espacial em todos os caminhos que podem ser especificados para os tipos de dados Ponto, Polígono ou LineString. O valor no caminho especificado deve ser um fragmento GeoJSON válido, como `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Espacial** dá suporte a consultas espaciais (interna e de distância) eficientes. Os tipos de dados podem ser Ponto, Polígono ou LineString.

> [!NOTE]
> O Azure Cosmos DB dá suporte à indexação automática de tipos de dados Ponto, Polígono e LineString.
> 
> 

Estes são os tipos de índice com suporte e exemplos de consultas que eles podem usar para servir:

| Tipo de índice | Descrição/caso de uso                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | O hash em /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash em /props/[]/? (or / or /props/) pode ser usado para servir às consultas a seguir eficientemente:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Intervalo      | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| Espacial     | O intervalo em over /prop/? (or /) pode ser usado para servir às seguintes consultas eficientemente:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --com indexação nos pontos habilitada<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --com indexação em polígonos habilitada              |

Por padrão, um erro será retornado para consultas com operadores de intervalo como >= se não houver nenhum índice do Intervalo (de qualquer precisão) para sinalizar que uma digitalização pode ser necessária para atender a consulta. As consultas de intervalo podem ser realizadas sem um índice do Intervalo, usando o cabeçalho **x-ms-documentdb-enable-scan** na API REST ou a opção de solicitação **EnableScanInQuery** usando o SDK do .NET. Se houver outros filtros na consulta nos quais o Azure Cosmos DB pode usar o índice para filtrar, nenhum erro será retornado.

As mesmas regras se aplicam a consultas espaciais. Por padrão, um erro retornará para consultas espaciais se não houver um índice espacial e se não houver outros filtros que possam ser atendidos a partir do índice. É possível executá-las como uma digitalização, usando **x-ms-documentdb-enable-scan** ou **EnableScanInQuery**.

#### <a name="index-precision"></a>Precisão de índice
Você pode usar a precisão do índice para fazer compensações entre a sobrecarga de armazenamento de índice e o desempenho de consultas. Para números, recomendamos usar a configuração de precisão padrão -1 (máximo). Devido aos números serem de 8 bytes em JSON, isso será equivalente a uma configuração de 8 bytes. Escolher um valor menor para precisão, como 1 a 7, significa que os valores dentro de alguns intervalos podem ser mapeados para a mesma entrada de índice. Portanto, o espaço de armazenamento do índice é reduzido, mas a execução da consulta talvez necessite processar mais documentos. Consequentemente, isso consome mais taxa de transferência nas unidades de solicitação.

A configuração de precisão do índice tem mais aplicação prática com intervalos de cadeia de caracteres. Como as cadeias de caracteres podem ter qualquer comprimento arbitrário, a escolha da precisão do índice pode afetar o desempenho das consultas de intervalo de cadeias de caracteres. Isso também pode afetar a quantidade de espaço de armazenamento de índice necessária. Os índices de Intervalo de Cadeia de Caracteres podem ser configurados com 1 a 100 ou -1 (máximo). Caso queira realizar consultas ORDER BY em propriedades de cadeias de caracteres, será necessário especificar uma precisão de -1 para os caminhos correspondentes.

Os índices espaciais sempre usam a precisão de índice padrão para todos os tipos (Ponto, LineString e Polígono). A precisão de índice padrão para índices espaciais não pode ser substituída. 

O exemplo a seguir mostra como aumentar a precisão dos índices de Intervalo em uma coleção usando o SDK do .NET. 

**Criar uma coleção com uma precisão de índice personalizada**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> O Azure Cosmos DB retorna um erro quando uma consulta usa ORDER BY, mas não tem um índice do Intervalo no caminho consultado com a precisão máxima. 
> 
> 

Da mesma forma, será possível excluir completamente os caminhos da indexação. O próximo exemplo mostra como excluir uma seção inteira dos documentos (uma *subárvore*) da indexação, usando o operador curinga \*.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Aceitar e recusar a indexação
Você pode escolher se deseja que a coleção indexe automaticamente todos os documentos. Por padrão, todos os documentos são indexados automaticamente, mas você pode desligar a indexação automática. Quando a indexação estiver desativada, os documentos poderão ser acessados somente através de self-links ou por meio de consultas usando a ID do documento.

Com a indexação automática desativada, você ainda pode adicionar seletivamente somente documentos específicos para o índice. Em contrapartida, é possível deixar a indexação automática e selecionar seletivamente para excluir documentos específicos. As configurações ativar/desativar a indexação são úteis quando você tem apenas um subconjunto de documentos que precisa ser consultado.

O exemplo a seguir mostra como incluir um documento explicitamente usando o [SDK do .NET na API do SQL](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) e a propriedade [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Modificar a política de indexação de uma coleção
No Azure Cosmos DB é possível fazer alterações na política de indexação de uma coleção em tempo real. Uma mudança na política de indexação em uma coleção do Azure Cosmos DB pode levar a uma alteração na forma do índice. A mudança afeta os caminhos que podem ser indexados, assim como a precisão e o modelo de consistência do próprio índice. Uma mudança na política de indexação efetivamente exige uma transformação do índice antigo em um novo índice. 

**Transformações de índice online**

![Como funciona a indexação – Transformações de índice online do Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

As transformações do índice são feitas online. Isso significa que os documentos indexados pela política antiga serão transformados com eficiência pela nova política *sem afetar a disponibilidade de gravação ou a taxa de transferência* provisionada da coleção. A consistência das operações de leitura e gravação feitas usando a API REST, SDKs ou em gatilhos e procedimentos armazenados não será afetada durante a transformação do índice. Não haverá degradação de desempenho ou tempo de inatividade em seus aplicativos ao fazer uma alteração de política de indexação.

No entanto, durante o tempo em que a transformação de índice estiver em andamento, as consultas por fim serão consistentes, independentemente da configuração do modo indexação (Consistente ou Lento). Isso também se aplica a consultas de todas as interfaces: API REST, SDKs ou de gatilhos e procedimentos armazenados. Assim como com a indexação no modo Lento, a transformação do índice é realizada de forma assíncrona em segundo plano nas réplicas, usando os recursos de reposição disponíveis para uma réplica específica. 

Transformações de índice também são feitas in-loco. O Azure Cosmos DB não mantém duas cópias do índice e troca o índice antigo por um novo. Isso significa que nenhum espaço em disco adicional é necessário ou consumido em suas coleções, enquanto as transformações de índice ocorrem.

Ao alterar a política de indexação as mudanças são aplicadas para substituir o índice antigo para o novo, principalmente com base nas configurações do modo de indexação. As configurações do modo de indexação desempenham uma função maior do que outros valores, como caminhos incluídos/excluídos, tipos de índice e precisões. 

Se as políticas antigas e as novas usarem indexação Consistente, o Azure Cosmos DB executará uma transformação de índice online. Não será possível aplicar outra alteração de política de indexação que tenha um modo de indexação Consistente, enquanto a transformação estiver em andamento. No entanto, você poderá mover para o modo de indexação Lento ou Nenhum, enquanto uma transformação estiver em progresso: 

* Ao mudar para Lento, a alteração da política de índice será imediatamente efetivada. O Azure Cosmos DB começa a recriar o índice de maneira assíncrona. 
* Ao mudar para Nenhum, o índice será imediatamente ignorado. Mover para Nenhum será útil quando quiser cancelar uma transformação em andamento e iniciar novamente com uma política de indexação diferente. 

O trecho de código a seguir mostra como modificar a política de indexação de uma coleção, desde o modo de indexação Consistente até o modo de indexação Lento. Se estiver usando o SDK do .NET, você poderá iniciar uma alteração de política de indexação usando o método **ReplaceDocumentCollectionAsync** novo.

**Modificar a política de indexação de Consistente para Lento**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Acompanhar o andamento da transformação de índice**

É possível acompanhar o progresso percentual da transformação do índice para um índice Consistente, usando a propriedade de resposta **IndexTransformationProgress** de uma chamada **ReadDocumentCollectionAsync**. Outros SDKs, e a API REST, dão suporte a propriedades e métodos equivalentes para fazer alterações na política de indexação. É possível verificar o progresso de uma transformação de índice para um índice Consistente, chamando **ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * A propriedade **IndexTransformationProgress** será aplicável somente quando transformar para um índice Consistente. Use a propriedade **ResourceResponse.LazyIndexingProgress** para acompanhar as transformações para um índice Lento.
> * As propriedades **IndexTransformationProgress** e **LazyIndexingProgress** são preenchidas apenas para uma coleção não particionada, ou seja, uma coleção que é criada sem uma chave de partição.
>

Você pode remover o índice de uma coleção mudando para o modo de indexação Nenhum. Essa poderá ser uma ferramenta operacional útil caso queira cancelar uma transformação em andamento e, em seguida, iniciar imediatamente uma nova.

**Ignorar o índice para uma coleção**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

Quando você fará alterações na política de indexação de suas coleções do Azure Cosmos DB? A seguir, os casos de uso mais comuns:

* Atende resultados consistentes durante a operação normal, mas retornam ao modo de indexação Lento durante importações de dados em massa.
* Iniciar o uso dos novos recursos de indexação nas coleções do Azure Cosmos DB atuais. Por exemplo, é possível usar consulta geoespacial, que requer o tipo de índice Espacial, ou consultas de intervalo de cadeia de caracteres/ORDER BY, que exigem o tipo de índice do Intervalo de cadeia de caracteres.
* Selecionar manualmente as propriedades a serem indexadas e alterá-las ao longo do tempo.
* Ajustar a precisão da indexação para melhorar o desempenho da consulta ou reduzir o armazenamento consumido.

> [!NOTE]
> Para modificar a política de indexação usando **ReplaceDocumentCollectionAsync**, será necessário usar a versão 1.3.0 ou uma versão posterior do SDK do .NET.
> 
> Para que a transformação do índice seja concluída com sucesso, assegure-se de que há espaço de armazenamento disponível suficiente na coleção. Se a coleção atingir a cota de armazenamento, a transformação do índice será pausada. A transformação do índice será retomada automaticamente quando o espaço de armazenamento estiver disponível, por exemplo, ao excluir alguns documentos.
> 
> 

## <a name="performance-tuning"></a>Ajuste de desempenho
As APIs SQL fornecem informações sobre métricas de desempenho, como o armazenamento de índice usado e o custo de taxa de transferência (unidades de solicitação) para cada operação. É possível usar essas informações para comparar várias políticas de indexação e ajustar o desempenho.

Para verificar a quota de armazenamento e o uso de uma coleção, execute uma solicitação **HEAD** ou **GET** no recurso da coleção. Em seguida, inspecione os cabeçalhos **x-ms-request-quota** e **x-ms-request-usage**. No SDK do .NET, as propriedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) e [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) em [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contêm esses valores correspondentes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Para medir a sobrecarga da indexação em cada operação de gravação (criar, atualizar ou excluir), inspecione o cabeçalho **x-ms-request-charge** (ou a propriedade equivalente [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) em [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) no SDK do .NET) para medir o número de unidades de solicitação consumidas por essas operações.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Alterações à especificação da política de indexação
Uma mudança no esquema da política de indexação foi introduzida em 7 de julho de 2015, com a versão 2015-06-03 da API REST. As classes correspondentes nas versões do SDK têm novas implementações para corresponder ao esquema. 

As seguintes alterações foram implementadas na especificação JSON:

* A política de indexação dá suporte a índices de Intervalo para cadeias de caracteres.
* Cada caminho pode ter múltiplas definições de índice. E é possível ter um para cada tipo de dados.
* A precisão de indexação suporta de 1 a 8 para números, 1 a 100 para cadeias de caracteres e -1 (precisão máxima).
* Segmentos de caminhos não exigem aspas duplas para cada caminho de escape. Por exemplo, você pode adicionar um caminho **/title/?** em vez de **/"title"/?**.
* O caminho raiz representando "todos os caminhos" pode ser representado como **/\*** (além de **/**).

Se você tiver um código que provisione coleções com uma política de indexação personalizada gravada com versão 1.1.0 do SDK do .NET ou uma versão anterior, para mover para o SDK versão 1.2.0, você deverá alterar o código do aplicativo para lidar com essas mudanças. Se você não possui um código que configure a política de indexação, ou se planeja continuar usando uma versão anterior do SDK, nenhuma alteração será necessária.

Para uma comparação prática, aqui está um exemplo de uma política de indexação personalizada gravada usando a API REST versão 2015-06-03, seguida da mesma política de indexação gravada usando a API REST versão 2015-04-08 anterior.

**Política de indexação JSON atual (API REST versão 2015-06-03)**

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


**Política de indexação JSON anterior (API REST versão 2015-04-08)**

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
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>Próximas etapas
Para exemplos de gerenciamento de políticas de índice e saber mais sobre a linguagem de consulta do Azure Cosmos DB, consulte os seguintes links:

* [Exemplos de código de gerenciamento de índice .NET da API SQL](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Operações de coleção da API REST SQL](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Consulta com SQL](sql-api-sql-query.md)

