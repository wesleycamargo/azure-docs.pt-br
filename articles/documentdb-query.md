<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Consulta por Banco de Dados de Documentos do Azure

## Motivação

A linguagem de consulta SQL do Banco de Dados de Documentos suporta um subconjunto de gramáticas ANSI SQL e acrescenta suporte orientado a documentos, em forma de estruturas de dados aninhadas, matrizes e construção de objetos. As consultas são servidas de modo eficiente por meio de índices de Bancos de Dados de Documentos, que são mantidos atualizados automaticamente para todas as propriedades em documentos sem a sobrecarga de gerenciamento de índices. Os desenvolvedores também podem aproveitar o potencial total do JavaScript dentro das consultas por meio das funções definidas pelo usuário (UDFs).

## Por que consulta SQL?

O Banco de Dados de Documentos suporta SQL para fornecer uma interface muito simples e abordável para consultar dados. O SQL como linguagem é estruturalmente simples, mas apesar disso é poderoso e amplamente usado por desenvolvedores de aplicativos. O Banco de Dados de Documentos é profundamente comprometido em abrir plataformas e padrões como HTTP, JSON, JavaScript e, agora, SQL.
A consulta de SQL é suportada por meio da API REST assim como .NET, Node.js e SDKs de cliente JavaScript. As consultas do SQL também são suportadas em procedimentos armazenados e disparadas na API de JavaScript do lado do servidor. Além de consultas SQL, o Banco de Dados de Documentos também suporta consultas via LINQ no SDK .NET.

# Tipos de dados

No Banco de Dados de Documentos, o formato de dados para documentos e metadados é expresso em Notação de Objetos de JavaScript (JSON). O JSON é amplamente utilizado devido ao crescimento de aplicativos em JavaScript e sua evolução em um formato de troca de dados independente de plataforma. Devido ao caráter autodescritivo do JSON, ele também é adequado para bancos de dados livres de esquema.

Os tipos de dados primitivos suportados em consultas de Banco de Dados de Documentos são os mesmos que os do JSON. O JSON tem um sistema de tipo simples, que consiste em:

-   Cadeias de caracteres
-   Números (precisão dupla IEEE754)
-   Booleanos – verdadeiro e falso
-   Nulos

Tipos de dados mais complexos podem ser representados tanto no JSON quanto no Banco de Dados de Documentos por meio da criação de objetos aninhados usando o operador { } e matrizes usando o operador [ ].

# Gramática de consulta

Usando a linguagem SQL de Banco de Dados de Documentos, os usuários podem obter dados dos documentos JSON usando instruções **SELECT**. Eis aqui uma amostra de instrução SQL em relação a uma coleção de documentos, contendo publicações em uma rede social.

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

A saída de uma consulta é uma matriz de fragmentos de documentos JSON, um para cada documento que corresponda às restrições especificadas.

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

A gramática formal completa (BNF) para a linguagem SQL de consulta está incluída no final deste documento.

# Cláusula FROM

A cláusula **FROM** é normalmente o nome da coleção atual em que a consulta é realizada. Por exemplo, isso seleciona todos os documentos na coleção nomeada “publicações”. Se o nome estiver incorreto, o usuário recebe uma mensagem de erro.

    SELECT * 
    FROM posts

A palavra-chave **ROOT** pode ser utilizada em vez de se fazer referência à coleção por nome – desde que por meio do SDK ou API REST, as consultas podem ser destinadas a um escopo específico de coleção.

    SELECT * 
    FROM ROOT

As propriedades de saída de FROM podem receber um alias. Isso é útil ao utilizar múltiplas fontes de FROM usando **JOINs**. Esses pontos são explorados mais tarde.

    SELECT P.* 
    FROM posts P

Também é possível realizar uma consulta por meio de uma propriedade aninhada. Por exemplo, se os documentos da publicação contêm um subdocumento para o usuário que criou a publicação, é possível realizar a consulta usando a sintaxe a seguir.

    SELECT U.* 
    FROM posts.user U

A origem da instrução FROM também pode ser uma matriz escalar (conjunto ordenado).

    SELECT E
    FROM ["documentdb", "json", "sql"] E

Também é possível omitir a instrução FROM por completo, enquanto se avalia expressões escalares.

`SELECT "documentdb"`

# Cláusula WHERE (Filtragem)

A linguagem SQL de Documento de Banco de Dados suporta uma cláusula **WHERE** opcional que filtra o conjunto de resultados com base nas condições especificadas que o documento precisa satisfazer. A expressão de filtragem pode conter comparações lógicas com relação a qualquer propriedade ou caminho aninhado nos documentos.

Por exemplo, qualquer propriedade como mensagem pode ser utilizada em consultas. A filtragem é realizada de modo eficiente utilizando o índice automático do Banco de Dados de Documentos.

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

Pode-se utilizar propriedades aninhadas para filtragem usando a notação pontilhada. Os caminhos podem ser arbitrariamente longos, dependendo do esquema dos documentos.

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

Pode-se fazer referência aos valores individuais dentro das matrizes usando o operador de desreferenciação. O desenrolar matricial é suportado usando a palavra-chave **IN** (discutida posteriormente).

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

Comparações de intervalo (\>, \<. \>=, \<=, !=) são suportadas para valores numéricos. Os números também podem ser comparados ao resultado de uma expressão aritmética conforme demonstrado abaixo.

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

Os operadores lógicos podem ser combinados usando os operadores AND, OR e NOT exatamente como ocorre no SQL comum. Esses operadores são processados de modo eficiente pela intersecção dos índices para as propriedades especificadas.

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## Gerenciando as propriedades Missing e Null

NULL é suportada como uma palavra-chave, sendo que as propriedades podem ser comparadas à nula como podem ser comparadas às outras escalares.

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

Já que este esquema para documentos pode estar em rápida mudança ou ser desconhecido anteriormente, a linguagem também suporta operadores especiais para verificar se há propriedades faltando – por meio do operador ISUNDEFINED, similar a **undefined** em JavaScript .

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> Observação: Similar ao JavaScript, = undefined retorna sempre um valor false.

# Cláusula SELECT (projeção)

A cláusula **SELECT** pode ser usada para extrair determinadas propriedades da consulta. O operador asterisco (\*) retorna os documentos completos que correspondem à consulta.

    SELECT * 
    FROM posts

Campos especificados podem ser extraídos da cláusula SELECT. Os campos podem ser atômicos (cadeias, números, Booleanos) ou complexos (matrizes, objetos JSON). Tipos complexos na cláusula SELECT, como “usuário” no exemplo abaixo, retornam como resultado todo o objeto JSON sob a propriedade. Propriedades na cláusula SELECT precisam ser nomes ou aliases únicos.

    SELECT posts.id, posts.user
    FROM posts

> Observação: Já que o JSON está fora de ordem, as propriedades individuais no documento não são retornadas em nenhuma ordem específica.

> Observação: Os nomes de campos precisam ser qualificados explicitamente. Como os documentos não têm esquema fixo, é necessário que o tempo de execução da consulta realize a associação correta.

Um modo alternativo de acessar propriedades é usar a sintaxe de pesquisa de dicionário conforme mostrado abaixo:

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> Observação: A sintaxe de pesquisa de dicionário deve ser usada para dar saída a nomes de propriedade que utilizam uma palavra-chave reservada como FROM, no exemplo abaixo.

## Avaliação da expressão

Expressões escalares também podem ser usadas com expressões. Quando não há nome especificado, um nome gerado automaticamente é utilizado como um espaço reservado ($1, $2, $3, etc.).

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

As expressões a seguir podem ser usadas dentro de expressões de consulta SQL. Os operadores são digitados com rigidez, como em SQL. Por exemplo, igual (=) é igualdade rígida, que significa que 5 != “5”.

<table>
<tr><td> Operadores aritméticos             </td><td> +, -, \*, /, % (Módulo)                                </td></tr>
<tr><td> Operadores lógicos                 </td><td> AND, OR, NOT                                           </td></tr>
<tr><td> Operadores Bit a bit               </td><td> & (Bit a bit AND), | (Bit a bit OR), ^ (Bit a bit XOR) </td></tr>
<tr><td> Operadores de cadeia de caracteres </td><td> || (Concatenar)                                        </td></tr>
<tr><td> Operadores de comparação           </td><td> =, !=, \>, \<, \>=, \<=                                </td></tr>
</table>

## Transformação JSON

As consultas SQL podem retornar fragmentos JSON, não apenas concluir documentos – a palavra-chave **VALUE** pode ser usada para fazer isso. Por exemplo, o documento a seguir retornará “2” em vez de {“id”: “2”}.

    SELECT VALUE posts.id
    FROM posts

Expressões JSON mais complexas podem ser criadas como em JavaScript, usando os operadores {} e []. Isso permite flexibilidade nas consultas para transformar a forma dos conjuntos de resultados.

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> Observação: Os valores podem ser qualquer expressão válida, mas os rótulos precisam ser valores literais (cadeias de caracteres)

# Junções e Iteração

Em bancos de dados de documento, os dados referenciados são integrados como subdocumentos ou matrizes internas. Assim, a linguagem SQL suporta intradocumento, também conhecido como autojunções, usando a palavra-chave **JOIN**.

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> Observação: As junções são formalmente junções cruzadas, mas já que os dados são integrados, elas funcionam como junções internas.

No exemplo, o operador **IN** é usado para realizar a iteração em todos os elementos de uma fonte. A origem para uma cláusula IN pode ser uma matriz ou então um objeto. Quando usado com um objeto, o iterador passa por cada uma das propriedades. O IN também pode ser usado diretamente, conforme mostrado abaixo. O IN também pode ser usado com valores literais de matriz.

    SELECT tag 
    FROM tag IN posts.tags

As junções múltiplas podem ser usadas dentro de uma instrução SQL, conforme mostrado abaixo:

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# UDFs (Funções definidas pelo usuário)

As consultas a Banco de Dados de Documentos suportam extensões programáveis em formato de funções definidas pelo usuário do JavaScript. Cada coleção pode ter um ou mais **UDFs** salvos, que transformam fragmentos JSON e fazem referência a eles por nome, diretamente nos scripts.

Por exemplo, usando as funções definidas pelo usuário de criação, uma função “regex\_match” pode ser definida como:

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

Então, pode-se fazer referência a ela em consultas como a deste exemplo:

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> Observação: As UDFs têm acesso somente aos parâmetros de entrada. Nenhuma operação de armazenamento de Banco de Dados de Documentos (leitura, gravação, consulta, etc.) é suportada dentro das UDFs.

> Observação: Atualmente, só uma UDF pode ser usada em uma consulta.

As UDFs podem processar e retornar fragmentos JSON, não apenas valores literais. Por exemplo, uma função “array\_count” pode ser definida como:

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

E, em seguida, ser usada em consultas para descobrir o tamanho de uma matriz dentro de um documento:

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

As UDFs também podem ser especificadas na cláusula SELECT ou na cláusula FROM de uma consulta. Por exemplo:

    SELECT array_count(p.likes) AS count
    FROM posts p

Para obter mais detalhes sobre a criação e o gerenciamento de UDFs, consulte a documentação sobre a programabilidade do JavaScript.

# Paginamento

Cada execução de consulta retorna um lote de resultados segundo o tamanho de página configurado pelo cliente. Para ler todos os resultados para uma consulta, os aplicativos precisam fazer o paginamento por cada conjunto de resultados até que não existam mais dados para ler. Para ler não mais que dez documentos, por exemplo, os clientes podem modificar o tamanho de página para 10 para restringir o número máximo de documentos retornado. Observe que algumas consultas podem retornar menos resultados do que o tamanho de página ou até mesmo nenhum resultado, para algumas consultas. Para ler todos os resultados de uma consulta, os clientes devem buscar o próximo lote, usando o token de continuação de resposta até que esteja vazio.

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

Isso não é implementado na gramática de SQL como uma palavra-chave TOP ou LIMIT, desde que a funcionalidade já esteja disponível por meio de tokens de continuação no cliente subjacente, SDK/API REST. Para obter mais informações e exemplos, consulte a documentação API REST para documentos GET ou os métodos ReadFeed nos SDKs.

# Comportamento de consistência de consultas

O Banco de Dados de Documentos suporta quatro níveis de consistência ajustáveis para desenvolvedor – Strong, Bounded Staleness, Session e Eventual. As consultas oferecem as mesmas garantias dos níveis de consistência. Por padrão, os resultados de uma consulta são garantidos para corresponder ao nível de consistência que é solicitado pelo cliente. O índice do Banco de Dados de Documentos é estruturado em log e projetado para manter-se sempre atualizado e consistente com os dados, independentemente do volume. Quando os documentos são inseridos ou atualizados, eles ficam imediatamente disponíveis nos resultados de consulta.

Para aplicativos que precisam de consistência eventual, idealmente, a política de indexação pode ser configurada para utilizar indexação lenta. Nesse caso, o índice é atualizado de um modo eventualmente consistente sempre que a coleção está sem uso. Para resumir, eis aqui uma tabela do comportamento de consistência de consulta com as diferentes configurações de conta de banco de dados.

| Consistência de dados | Política de indexação | Comportamento de consulta |
|-----------------------|-----------------------|---------------------------|
| Strong                | Consistente           | Strong                    |
| Bounded Staleness     | Consistente           | Bounded Staleness         |
| Session               | Consistente           | Session                   |
| Eventual              | Consistente           | Eventual                  |
| Strong                | Lentidão              | Eventual                  |
| Bounded Staleness     | Lentidão              | Eventual                  |
| Session               | Lentidão              | Eventual                  |
| Eventual              | Lentidão              | Eventual                  |

Para obter mais detalhes, consulte a documentação na política de indexação e configuração.

# APIs e SDKs

As consultas podem ser executadas usando a API REST, SDKs cliente e API JavaScript do lado do servidor usando a gramática de SQL.

## Consulta usando a API REST

Os aplicativos podem realizar consultas POST em coleções usando a API REST. O conteúdo a seguir deve ser incluído em solicitações de consulta

-   Cabeçalho x-ms-documentdb-isquery: Verdadeiro para indicar que esta é uma consulta
-   Cabeçalho Content-Type: application/sql, para indicar que a linguagem SQL é utilizada
-   Corpo contendo a instrução SELECT
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    autorização: ...
    x-ms-continuation:
    x-ms-activity-id: 82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date: Sexta-feira, 30 de maio de 2014 22:46:13 GMT
    Correspondência:
    x-docdb-resource-id: 9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery: True
    Cache-Control: no-cache
    x-ms-version: 2014-02-25
    User-Agent: Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type: application/sql
    Host: ...
    Content-Length: 59
    Expect: 100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> Observação: Não há suporte à consulta usando GET ou usando cadeias de caracteres de consulta.

## Consulta usando o SDK .NET

O SDK .NET suporta consulta usando o método CreateDocumentQuery, que suporta consultas SQL como cadeias de caracteres. A saída da consulta é uma interface IQueryable que pode ser processada usando o LINQ no lado do cliente.

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

Para obter mais detalhes, consulte a documentação do SDK .NET.

## Consulta em procedimentos armazenados e gatilhos

A API de JavaScript no lado do servidor para procedimentos armazenados e gatilhos também suporta consultas usando SQL.

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

Para obter mais detalhes, consulte a documentação do SDK JavaScript do lado do servidor.

# Apêndice A – Sintaxe SQL

Os diagramas em trilhos a seguir mostram a gramática SQL para a linguagem de consulta de Banco de Dados de Documentos

![][]<br />
![][1]<br />
![][2]<br />
![][3]<br />
![][4]<br />
![][5]<br />
![][6]<br />
![][7]<br />
![][8]<br />
![][9]<br />
![][10]<br />
![][11]<br />
![][12]<br />
![][13]<br />
![][14]
![][15]
![][16]

  []: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
