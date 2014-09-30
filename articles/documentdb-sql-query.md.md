<properties title="Query with DocumentDB SQL" pageTitle="Query with DocumentDB SQL | Azure" description="DocumentDB supports querying of documents using SQL-like grammar over hierarchical JSON documents without requiring explicit schema or creation of secondary indexes." metaKeywords="" services="documentdb"  documentationCenter="" solutions="data-management" authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Consulta ao Banco de Dados de Documentos

O Banco de Dados de Documentos do Azure tem suporte para a realização da consulta de documentos utilizando uma SQL (Structured Query Language) familiar em documentos hierárquicos JSON. O Banco de Dados de Documentos é verdadeiramente livre de esquemas. Em virtude de seu comprometimento com o modelo de dados JSON diretamente dentro do mecanismo do banco de dados, ele fornece a indexação automática de documentos JSON sem a necessidade de esquemas explícitos ou da criação de índices secundários.
Ao criar a linguagem de consulta para o Banco de Dados de Documentos, tínhamos dois objetivos em mente:

-   **Adotar a SQL** – Em vez de inventar uma nova linguagem para consulta, queríamos adotar a linguagem SQL. Afinal, a SQL é uma das linguagens de consulta mais familiares e populares. A linguagem SQL Banco de Dados de Documentos do Azure fornece um modelo de programação formal para consultas avançadas em documentos JSON.
-   **Estender a SQL** – Como um banco de dados de documentos JSON capaz de executar o JavaScript diretamente no mecanismo do banco de dados, queríamos usar o modelo de programação do JavaScript como os alicerces de nossa linguagem de consulta SQL. A linguagem de consulta SQL Banco de Dados de Documentos do Azure é baseada no sistema, avaliação de expressão e inovação de função do tipo JavaScript. Isso, por sua vez, oferece um modelo de programação natural para projeções relacionais, navegação hierárquica em documentos JSON, autojunções, invocação de UDFs (funções definidas pelo usuário) gravadas inteiramente em JavaScript, entre outros recursos.

Nós acreditamos que esses recursos sejam fundamentais para reduzir o atrito entre o aplicativo e o banco de dados e cruciais para a produtividade do desenvolvedor.

Neste tutorial, apresentamos os recursos e a gramática da linguagem de consulta do Banco de Dados de Documentos. Abordamos também como é possível realizar consultas no Banco de Dados de Documentos usando a API REST e as SDKs (incluindo o LINQ).

# Introdução

Para ver o Banco de Dados de Documentos SQL em funcionamento, começaremos com alguns documentos JSON simples e percorreremos algumas consultas simples relacionadas a eles. Considere esses dois documentos JSON sobre duas famílias. Observe que com o Banco de Dados de Documentos, não precisamos criar nenhum esquema ou índice secundário explicitamente. Precisamos apenas inserir os documentos JSON em uma coleção do Banco de Dados de Documentos e, em seguida, realizar a consulta.
Aqui, temos um documento JSON simples relacionado à família Andersen - os pais, filhos (e seus animais de estimação), endereço e informações de registro. O documento tem cadeias de caracteres, números, boolianos, matrizes e propriedades aninhadas.

**Documento**

    {
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }

Eis um segundo documento, com uma pequena diferença —– `givenName` e `familyName` são usados em vez de `firstName` e `lastName`.

**Documento**

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                 "givenName": "Jesse", 
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
                "familyName": "Miller", 
                 "givenName": "Lisa", 
                 "gender": "female", 
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    }

Agora, vamos tentar realizar algumas consultas nestes dados para entender alguns dos principais aspectos do Banco de Dados de Documentos SQL. Por exemplo, a consulta a seguir retornará os documentos com campos ID correspondentes a `AndersenFamily`. Por se tratar de `SELECT *`, a saída da consulta é todo o documento JSON:

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }]

Agora, considere um caso em que precisamos reformatar a saída JSON para um formato diferente. Esta consulta projeta um novo documento JSON com dois campos selecionados, Nome e Cidade, com a cidade e o estado tendo o mesmo nome. Neste caso, "NY, NY" é correspondente.

**Consulta**

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultados**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]

A consulta seguinte retorna os primeiros nomes dos filhos da família cuja ID é correspondente a `WakefieldFamily`.

**Consulta**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]

Gostaríamos de chamar atenção para alguns aspectos de destaque da linguagem de consulta do Banco de Dados de Documentos nos exemplos que vimos até agora:

-   Como o Banco de Dados de Documentos SQL trabalha com valores JSON, ele lida com entidades com formato de árvore em vez de linhas e colunas. A linguagem, portanto, possibilita a referência a nós da árvore em qualquer profundidade arbitrária, como `Node1.Node2.Node3…..Nodem`, semelhantemente à referência SQL relacional em duas partes de `<table>.<column>`.
-   A linguagem trabalha com dados sem esquema. Portanto, o sistema de tipos precisa estar vinculado dinamicamente. A mesma expressão pode obter diferentes tipos em diferentes documentos. O resultado de uma consulta é um valor JSON válido, mas não há garantia de que seja de um esquema fixo.
-   O Banco de Dados de Documentos tem suporte apenas para documentos JSON estritos. Isto significa que as expressões e sistema de tipos são restritos para lidar somente com tipos JSON. Consulte [especificação JSON] (<http://www.json.org/>) para obter mais detalhes.
-   Uma coleção do Banco de Dados de Documentos é um contêiner de documentos JSON sem esquemas. As relações nas entidades de dados dentro e entre documentos em uma coleção são capturadas implicitamente pela contenção e não pelas relações PK-FK. Este é um importante aspecto que vale a pena destacar em virtude das junções intradocumentos abordadas mais adiante neste artigo.

# Indexação do Banco de Dados de Documentos

Antes de passarmos à linguagem SQL do Banco de Dados de Documentos, vale explorar o design da indexação do Banco de Dados de Documentos.

O objetivo de índices de bancos de dados é atender a consultas em suas diversas formas com um consumo mínimo de recursos (como CPU, E/S), oferecendo alta produtividade e baixas latências. Frequentemente, a escolha do índice correto para consultar um banco de dados requer muito planejamento e experimentação. Esta abordagem representa um desafio para bancos de dados sem esquemas, nos quais os dados não seguem um esquema rígido e evoluem rapidamente.

Portanto, quando criamos o subsistema de indexação do Banco de Dados de Documentos, definimos os seguintes objetivos:

-   Indexar documentos sem precisar de um esquema: O subsistema de indexação não requer nenhuma informação de esquema e não faz suposições a priori sobre o esquema dos documentos.

-   Suporte para pesquisas hierárquicas e relacionais ricas e eficientes: O índice dá suporte à linguagem de pesquisa do Banco de Dados de Documentos com eficácia, incluindo suporte para projeções relacionais e hierárquicas.

-   Suporte para consultas consistentes diante do grande volume de gravações: Para possibilitar cargas de trabalho com alta produtividade de gravação com consultas consistentes, o índice é atualizado gradativamente, eficientemente e on-line, em face o volume contínuo de gravações. A atualização consistente do índice é crucial para atender às consultas com o nível de consistência com o qual o usuário configurou o sistema.

-   Suporte para multilocatário: Dado o modelo baseado em reserva para a governança de recurso entre os locatários, as atualizações do índice são realizadas dentro do orçamento dos recursos do sistema (CPU, memória, IOPS) alocados por réplica.

-   Eficiência do armazenamento: Para manter um bom custo-benefício, a sobrecarga do armazenamento em disco do índice é vinculada e previsível. Isto é fundamental porque o Banco de Dados de Documentos permite que o desenvolvedor tome decisões baseadas em custo entre a sobrecarga do índice tendo em vista o desempenho de consulta.

Consulte [amostras do Banco de Dados de Documentos] (http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content) no MSDN para ver como configurar a política de indexação para uma coleção. Agora, vejamos os detalhes da linguagem SQL do Banco de Dados de Documentos.

# Conceitos básicos da consulta do Banco de Dados de Documentos

Toda consulta consiste em uma cláusula **SELECT** e cláusulas **FROM** e **WHERE** opcionais de acordo com os padrões ANSI-SQL. Normalmente, para cada consulta, a fonte da cláusula FROM é enumerada. Então, o filtro da cláusula WHERE é aplicado para recuperar um subconjunto de documentos JSON. Por fim, a cláusula SELECT é usada para projetar os valores JSON solicitados na lista selecionada.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]    

# Cláusula FROM

A cláusula `FROM <from_specification>` é opcional a menos que a fonte seja filtrada/projetada mais adiante na consulta. O objetivo desta cláusula é especificar a fonte de dados na qual a consulta deve operar. Normalmente, a coleção inteira é a fonte, mas é possível também especificar um subconjunto da coleção.

Uma consulta como `SELECT * FROM Families` indica que a coleção Families inteira é a fonte a ser enumerada. Um identificador especial **ROOT** pode ser usado para representar a coleção em vez de usar o nome da coleção.
As regras de associação aplicadas por consulta são as seguintes:

-   A coleção pode receber um alias, como em `SELECT f.id FROM Families AS f`, ou simplesmente `SELECT f.id FROM Families f`. Aqui, `f` é equivalente a `Families`. `AS` é uma palavra-chave opcional para o identificador de alias.

-   Observe que após receber um alias, a fonte original não pode ser associada. Por exemplo: `SELECT Familes.id FROM Families f` é sintaticamente inválido, pois o identificador “Families” não pode mais ser resolvido.

-   Todas as propriedades que precisam ser referidas devem ser **completamente qualificadas**. Na falta de aderência a um esquema rígido, esta regra é aplicada para evitar associações ambíguas. Portanto, `SELECT id FROM Families f` é sintaticamente inválido, pois a propriedade `id` não está vinculada.

## Subdocumentos

A fonte também pode ser reduzida a um subconjunto menor. Por exemplo, se alguém estiver interessado em enumerar somente uma subárvore de cada documento, a sub-raiz pode, então, se tornar a fonte, como no exemplo a seguir.

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Embora o exemplo acima tenha usado uma matriz como fonte, um objeto também pode ser usado como fonte, como mostrado no exemplo a seguir. Qualquer valor JSON válido (que não seja Indefinido) que possa ser encontrado na fonte será considerado para inclusão nos resultados da consulta. Se certas famílias não tiverem um valor `address.state`, elas serão excluídas no resultado da consulta.

**Consulta**

    SELECT * 
    FROM Families.address.state

**Resultados**

    [
      "WA", 
      "NY"
    ]

# Cláusula WHERE

A cláusula WHERE (**`WHERE <filter_condition>`**) é opcional. Ela especifica as condições que os documentos JSON fornecidos pela fonte devem satisfazer para serem incluídas como parte dos resultados. Qualquer documento JSON deve avaliar as condições especificadas como “verdadeiras” para serem consideradas para os resultados. A cláusula WHERE é usada pela camada do índice para determinar o subconjunto absolutamente menor de documentos fonte que pode fazer parte do resultado.

A consulta a seguir solicita documentos que contenham uma propriedade de nome e cujo valor seja `AndersenFamily`. Qualquer documento que não tiver uma propriedade de nome, ou no qual o valor não corresponder a `AndersenFamily`, será excluído.

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]

O exemplo anterior mostrou uma consulta de igualdade simples. O SQL do Banco de Dados de Documentos também dá suporte a diversas expressões escalares. As expressões mais usadas são as binárias e unárias. Referências de propriedade do objeto JSON fonte também são expressões válidas.

Os operadores binários a seguir são suportados atualmente em consultas como as exemplificadas:

<table>

<tr>

<td>
Aritmético

</td>

<td>
+,-,\*,/,%

</td>

</tr>

<tr>

<td>
Bit a bit

</td>

<td>
|, &, ^

</td>

</tr>

<tr>

<td>
Lógico

</td>

<td>
AND, OR

</td>

</tr>

<tr>

<td>
Comparação

</td>

<td>
=, !=, \>, \>=, \<, \<=, \<\>

</td>

</tr>

<tr>

<td>
Cadeia de caracteres

</td>

<td>
|| (concatenado)

</td>

</tr>

</table>
</p>
Vejamos algumas consultas que usam valores binários.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5

Os operadores unários **+,-, ~ e NOT** também são suportados e podem ser usados dentro de consultas, como exemplificado a seguir:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5

Além de operadores binários e unários, as referências de propriedade também são permitidas. Por exemplo: `SELECT * FROM Families f WHERE f.isRegistered` retornaria os documentos JSON contendo a propriedade `isRegistered` cujo valor fosse igual ao valor JSON `true`. Quaisquer outros valores (falso, nulo, Indefinido, <number>, <string>, <object>, <array> etc.) fariam com que o documento fonte fosse excluído dos resultados.

## Operadores de igualdade e comparação

A tabela a seguir mostra o resultado de comparações de igualdade na SQL do Banco de Dados de Documentos entre dois tipos JSON quaisquer.

<table style="width:300px">

<tbody>

<tr>

<td valign="top">
 **Operador**

</td>

<td valign="top">
 **Indefinido**

</td>

<td valign="top">
 **Nulo**

</td>

<td valign="top">
 **Booliano**

</td>

<td valign="top">
 **Número**

</td>

<td valign="top">
 **Cadeia de caracteres**

</td>

<td valign="top">
 **Objeto**

</td>

<td valign="top">
 **Matriz**

</td>

</tr>

<tr>

<td valign="top">
 <strong>Indefinido<strong>

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

</tr>

<tr>

<td valign="top">
 <strong>Nulo<strong>

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

</tr>

<tr>

<td valign="top">
 <strong>Booliano<strong>

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

</tr>

<tr>

<td valign="top">
 <strong>Número<strong>

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

</tr>

<tr>

<td valign="top">
 <strong>Cadeia de caracteres<strong>

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

</tr>

<tr>

<td valign="top">
 <strong>Objeto<strong>

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 **OK**

</td>

<td valign="top">
 Indefinido

</td>

</tr>

<tr>

<td valign="top">
 <strong>Matriz<strong>

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 Indefinido

</td>

<td valign="top">
 **OK**

</td>

</tr>

</tbody>

</table>
</p>
Para outros operadores de comparação, como \>, \>=, !=, \< e \<=

-   Comparação entre resultados de tipos em Indefinido.
-   Comparação entre resultados de dois objetos ou duas matrizes em Indefinido.

Se o resultado da expressão escalar do filtro for Indefinido, o documento correspondente não seria incluído no resultado, uma ver que Indefinido não corresponde logicamente a “verdadeiro”.

## Operadores lógicos (AND, OR e NOT)

Eles operam com base em valores boolianos. As tabelas de verdade lógica desses operadores são mostradas abaixo.

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>OR</strong> </p></td>
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p><strong>Indefinido</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>True</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>Indefinido</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Indefinido</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>Indefinido</p></td>
<td align="left"><p>Indefinido</p></td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col width="25%" />
<col width="25%" />
<col width="25%" />
<col width="25%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>AND</strong> </p></td>
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p><strong>Indefinido</strong></p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p>True</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>Indefinido</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>False</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Indefinido</strong></p></td>
<td align="left"><p>Indefinido</p></td>
<td align="left"><p>False</p></td>
<td align="left"><p>Indefinido</p></td>
</tr>
</tbody>
</table>

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<tbody>
<tr class="odd">
<td align="left"><p><strong>NOT</strong> </p></td>
<td align="left"><p></p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>True</strong></p></td>
<td align="left"><p>False</p></td>
</tr>
<tr class="odd">
<td align="left"><p><strong>False</strong></p></td>
<td align="left"><p>True</p></td>
</tr>
<tr class="even">
<td align="left"><p><strong>Indefinido</strong></p></td>
<td align="left"><p>Indefinido</p></td>
</tr>
</tbody>
</table>

# Cláusula SELECT

A cláusula SELECT (**`SELECT <select_list>`**) é obrigatória e especifica quais valores serão recuperados da consulta, como em ANSI-SQL. O subconjunto que foi filtrado sobre os documentos fonte é passado à fase de projeção, em que os valores JSON especificados são recuperados e um novo objeto JSON é construído, para cada entrada passada a ele.

O exemplo abaixo mostra uma consulta do tipo SELECT típica:

**Consulta**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]

## Propriedades aninhadas

No exemplo a seguir, estamos projetando duas propriedades aninhadas, `f.address.state` e `f.address.city`:

**Consulta**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "state": "WA", 
      "city": "seattle"
    }]

A projeção tem suporte também para expressões JSON, conforme mostrado no exemplo a seguir.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]

Vejamos a função de `$1`. A cláusula `SELECT` precisa criar um objeto JSON e, como nenhuma chave é fornecida, usamos nomes variáveis de argumento implícito iniciados por `$1`. Por exemplo: esta consulta retorna duas variáveis de argumento implícito, rotuladas como `$1` e `$2`.

**Consulta**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]

## Atribuição de alias

Agora, vamos estender o exemplo acima com a atribuição explícita de alias aos valores. **AS** é a palavra-chave usada para a atribuição de alias. Observe que é opcional, conforme mostrado ao projetar o segundo valor como `NameInfo`.

Caso uma consulta tenha duas propriedades com o mesmo nome, a atribuição de alias deve ser usada para renomear uma ou as duas propriedades para que elas não sejam ambíguas no resultado projetado.

**Consulta**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]

## Expressões escalares

Além de referências de propriedade, a cláusula **SELECT** dá suporte também a expressões escalares como constantes, expressões aritméticas, expressões lógicas etc. Por exemplo, vejamos uma consulta simples do tipo "Hello World".

**Consulta**

    SELECT "Hello World"

**Resultados**

    [{
      "$1": "Hello World"
    }]

Este é um exemplo mais complexo que usa uma expressão escalar:

**Consulta**

    SELECT ((2 + 11 % 7)-2)/3   

**Resultados**

    [{
      "$1": 1.33333
    }]

No exemplo a seguir, o resultado da expressão escalar é um booliano.

**Consulta**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f 

**Resultados**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]

## Criação de objeto e matriz

Outro recurso fundamental da SQL do Banco de Dados de Documentos é a criação de matriz/objeto. Observe que, no exemplo anterior, criamos um novo objeto JSON. De forma semelhante, é possível construir matrizes, como demonstrado abaixo.

**Consulta**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f 

**Resultados**

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

## Palavra-chave VALUE

A palavra-chave **VALUE** é uma forma de retornar valores JSON. Por exemplo: a consulta mostrada abaixo retorna o `"Hello World"` escalar, em vez de `{$1: "Hello World"}`.

**Consulta**

    SELECT VALUE "Hello World"

**Resultados**

    [
      "Hello World"
    ]

A consulta a seguir retorna o valor JSON sem o rótulo `"address"` nos resultados.

**Consulta**

    SELECT VALUE f.address
    FROM Families f 

**Resultados**

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

O exemplo a seguir expande esse procedimento para mostrar como retornar valores JSON primitivos, ou seja, no nível da folha da árvore JSON.

**Consulta**

    SELECT VALUE f.address.state
    FROM Families f 

**Resultados**

    [
      "WA",
      "NY"
    ]

## \* Operador

Nós suportamos o operador especial (\*) para projetar o documento da forma que ele é. Quando usado, ele deve ser o único campo projetado. Embora uma consulta como `SELECT * FROM Families f` seja válida, `SELECT VALUE * FROM Families f` e `SELECT *, f.id FROM Families f` não são.

**Consulta**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
    }]

# Conceitos avançados

## Iteração

Adicionamos uma nova construção por meio da palavra-chave **IN** na SQL do Banco de Dados de Documentos SQL para dar suporte à iteração em matrizes JSON. A fonte FROM dá suporte à iteração. Comecemos com o exemplo a seguir:

**Consulta**

    SELECT * 
    FROM Families.children

**Resultados**

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Agora, vejamos outra consulta que realiza a iteração em filhos na coleção. Observe a diferença na matriz de saída. Este exemplo divide `children` e planifica os resultados em uma única matriz.

**Consulta**

    SELECT * 
    FROM c IN Families.children

**Resultados**

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Isto pode ser usado mais amplamente para filtrar cada entrada individual da matriz, como no exemplo a seguir.

**Consulta**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultados**

    [{
      "givenName": "Lisa"
    }]

## Junções

Em um banco de dados relacional, a necessidade de realizar junções entre tabelas é muito importante. É o padrão lógico para criar esquemas normalizados. De forma contrária, o Banco de Dados de Documentos lida com o modelo de dados desnormalizado dos documentos sem esquemas. Trata-se do equivalente lógico de uma “autojunção”.

A sintaxe suportada na linguagem é <from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourcen>. De modo geral, isto retornaria um conjunto de tuplas **N** (tupla com valores **N**). Cada tupla terá os valores produzidos pela iteração de todos os alias da coleção em seus respectivos conjuntos. Em outras palavras, trata-se do produto do cruzamento completo dos conjuntos que participam da junção.

Os exemplos a seguir mostram como as junções funcionam. No exemplo abaixo, o resultado é vazio porque o produto cruzado de cada documento da fonte e de um conjunto vazio é vazio.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultados**

    [{
    }]

No exemplo a seguir, a junção ocorre entre a raiz do documento e a sub-raiz `children`. Trata-se de um produto cruzado entre dois objetos JSON. O fato de os filhos serem uma matriz não tem efeito sobre a junção, pois estamos lidando com uma única raiz que é a matriz de filhos. Sendo assim, os resultados contêm apenas dois resultados, uma vez que o produto cruzado de cada documento com a matriz resulta em exatamente um documento.

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]

O exemplo a seguir é uma junção mais convencional:

**Consulta**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]

Note que `from_source` de **JOIN** é um iterador. Sendo assim, neste caso o fluxo é o seguinte

-   Expanda cada elemento filho **c** na matriz.
-   Aplique um produto cruzado com a raiz do documento **f** com cada elemento filho **c** que foi planificado na primeira etapa.
-   Por fim, projete a propriedade do nome do objeto raiz **f** sozinha.

O primeiro documento (`AndersenFamily`) contém somente um elemento filho, de modo que o conjunto de resultados contenha apenas um único objeto correspondente a esse documento. O segundo documento (`WakefieldFamily`) contém dois filhos. Sendo assim, o produto cruzado produz um objeto separado para cada filho, resultando em dois objetos, um para cada filho correspondente a este documento. Observe que os campos raiz em ambos os documentos será o mesmo, da mesma forma que você esperaria em um produto cruzado.

A utilidade real da junção é formar tuplas do produto cruzado em um formato que, de outra forma, seria difícil projetar. Além disso, como veremos no exemplo abaixo, é possível filtrar a combinação de uma tupla que permite ao usuário escolher uma condição que é satisfeita pelas tuplas de modo geral.

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Resultados**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]

Este exemplo é uma extensão natural do exemplo acima, e realiza uma junção dupla. Assim, o produto cruzado pode ser visto como o pseudocódigo abaixo.

    for-each(Family f in Families)
    {   
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` tem um filho que tem um animal de estimação. Assim, o produto cruzado traz uma linha (1*1*1) desta família. WakefieldFamily, no entanto, tem dois filhos, mas apenas a filha "Jesse" tem animais de estimação. No entanto, ela tem dois animais. Assim, o produto cruzado traz 1*1*2 = 2 linhas desta família.

No exemplo a seguir, há um filtro adicional em `pet`. Isto exclui todas as tuplas em que o nome do animal não é "Shadow". Observe que podemos criar tuplas por meio de matrizes, filtrar qualquer um dos elementos da tupla e projetas qualquer combinação dos elementos.

**Consulta**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultados**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]

# Integração JavaScript

O Banco de Dados de Documentos oferece um modelo de programação para executar a lógica de aplicativos baseados em JavaScript diretamente nas coleções em termos de procedimentos armazenados e gatilhos. Isso possibilita:

-   Capacidade de realizar CRUD transacional de alto desempenho e consultar documentos em uma coleção em virtude da profunda integração do tempo de execução do JavaScript diretamente com o mecanismo do banco de dados.
-   Um modelamento natural de fluxo de controle, escopo de variáveis, atribuição e integração de primitivos que lidam com exceções com transações de bancos de dados. Para obter mais detalhes sobre o suporte do Banco de Dados de Documentos à integração com JavaScript, consulte a documentação de programabilidade do JavaScript.

## UDFs (Funções definidas pelo usuário)

Além dos tipos especificados acima, a SQL do Banco de Dados de Documentos oferece suporte a UDFs (funções definidas pelo usuário). Em particular, são suportadas UDFs escalares nas quais os desenvolvedores podem passar zero ou muitos argumentos e retornar um resultado com um único argumento. Cada um desses argumentos é verificado quanto a serem valores JSON legais.

A gramática da SQL do Banco de Dados de Documentos é estendida para dar suporte à lógica de aplicativos personalizados usando essas Funções definidas pelo usuário. As UDFs podem ser registradas com o Banco de Dados de Documentos do Azure e referenciadas como parte de uma consulta SQL. De fato, as UDFs são projetadas de maneira especial para serem invocadas por consultas. Como consequência dessa escolha, as UDFs não têm acesso ao objeto de contexto que outros tipos de JavaScript (Procedimentos armazenados, Gatilhos) têm. Como as consultas são executadas como somente leitura, elas podem ser executadas em réplicas primárias ou secundárias. Portanto, as UDFs foram criadas para serem executadas em réplicas secundárias, diferente de outros tipos de JavaScript.

Abaixo há um exemplo de como uma UDF pode ser registrada no banco de dados do Banco de Dados de Documentos, especificamente em uma coleção de documento.

       UserDefinedFunction sqrtUdf = new UserDefinedFunction
       {
           Name = "SQRT",
           Body = @"function(number) { 
                       return Math.sqrt(number);
                   };",
       };
       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           collectionSelfLink/* link of the parent collection*/, 
           sqrtUdf).Result;  
                                                                             

O exemplo acima cria uma UDF cujo nome é `SQRT`. Ela aceita um único valor JSON `number` e calcula a raiz quadrada do número usando a biblioteca de matemática.

Agora, podemos usar esta UDF em uma consulta em uma projeção.

**Consulta**

    SELECT SQRT(c.grade)
    FROM c IN Families.children

**Resultados**

    [
      {
        "$1": 2.23606797749979
      }, 
      {
        "$1": 1
      }, 
      {
        "$1": 2.8284271247461903
      }
    ]

A UDF também pode ser usada dentro de um filtro, como mostrado no exemplo abaixo:

**Consulta**

    SELECT c.grade
    FROM c IN Familes.children
    WHERE SQRT(c.grade) = 1

**Resultados**

    [{
        "grade": 1
    }]

Basicamente, as UDFs são expressões escalares válidas e podem ser usadas em projeções e filtros.

Para expandir o poder das UDFs, vejamos outro exemplo com lógica condicional:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Name = "SEALEVEL",
           Body = @"function(city) {
                switch (city) {
                    case 'seattle':
                        return 520;
                    case 'NY':
                        return 410;
                    case 'Chicago':
                        return 673;
                    default:
                        return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(collection.SelfLink, seaLevelUdf);

Abaixo há um exemplo que aplica a UDF.

**Consulta**

    SELECT f.address.city, SEALEVEL(f.address.city) AS seaLevel
    FROM Families f 

**Resultados**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]

Como os exemplos acima demonstram, as UDFs integram o poder da linguagem JavaScript com a SQL do Banco de Dados de Documentos para fornecer uma rica interface programável para complexas realizar lógicas condicionais de procedimentos com a ajuda dos recursos de tempo de execução incorporados ao JavaScript.

A SQL do Banco de Dados de Documentos fornece os argumentos às UDFs para cada documento na fonte no estágio atual (cláusula WHERE ou cláusula SELECT) de processamento das UDFs. O resultado é incorporado perfeitamente ao pipeline de execução geral. Se as propriedades referidas aos parâmetros das UDFs não estiverem disponíveis no valor JSON, o parâmetro é considerado Indefinido e a invocação das UDFs é totalmente ignorada. De maneira semelhante, se o resultado das UDFs for Indefinido, ele não será incluído nos resultados.

Em resumo, as UDFs são ótimas ferramentas para realizar lógicas de negócios complexas como parte da consulta.

## Avaliação de operador

O Banco de Dados de Documentos, em virtude se ser um banco de dados JSON, tem paralelos com operadores JavaScript em sua semântica de avaliação. Embora o Banco de Dados de Documentos tente preservar a semântica do JavaScript em termos de suporte ao JSON, a avaliação da operação desvia em alguns casos.

Na linguagem de consulta da SQL do Banco de Dados de Documentos, diferente da SQL tradicional, os tipos de valores frequentemente não são conhecidos até que os valores sejam recuperados do banco de dados. Para executar consultas com eficiência, a maioria dos operadores tem requisitos restritos de tipo.

A SQL do Banco de Dados de Documentos não realiza conversões implícitas, diferente do JavaScript. Por exemplo: uma consulta como `SELECT * FROM Person p WHERE p.Age = 21` corresponderá a documentos que contiverem a propriedade Age com valor 21. Qualquer outro documento cuja propriedade Age corresponder a “21” — ou
a outras variações potencialmente infinitas como “021”, “21,0”, “0021”, “00021” etc. — não será correspondido.
Isso ocorre em oposição ao JavaScript, que os valores das cadeias de caracteres são convertidos implicitamente em números (baseado em operador, como, por exemplo: ==). Esta escolha é fundamental para uma correspondência eficiente de índices na SQL do Banco de Dados de Documentos.

# LINQ para SQL do Banco de Dados de Documentos

O LINQ é um modelo de programação .NET que expressa a computação como consultas em fluxos de objetos. O Banco de Dados de Documentos oferece uma biblioteca cliente para realizar a interface com o LINQ facilitando a conversão entre objetos JSON e .NET e mapeando por meio de um subconjunto de consultas do LINQ para consultas do Banco de Dados de Documentos.

A imagem abaixo mostra a arquitetura do suporte a consultas do LINQ usando o Banco de Dados de Documentos. Usando o cliente do Banco de Dados de Documentos, os desenvolvedores podem criar um objeto **IQueryable** que direcionaria a consulta ao provedor de consulta do Banco de Dados de Documentos que, por sua vez, traduz a consulta do LINQ para uma consulta do Banco de Dados de Documentos. A consulta é, então, passada ao servidor do Banco de Dados de Documentos para recuperar um conjunto de resultados no formato JSON. Os resultados retornados são desserializados em um fluxo de objetos .NET no lado do cliente.

![][]

## Mapeamento .NET e JSON

O mapeamento entre objetos .NET e documentos JSON é natural - cada campo de membro de dados é mapeado para um objeto JSON, em que o nome do campo é mapeado para a parte “chave” do objeto e a parte do “valor” é mapeada recursivamente para a parte de valor do objeto. Considere o exemplo abaixo. O objeto Família criado é mapeado para o documento JSON conforme mostrado abaixo. E vice-versa, o documento JSON é mapeado para um objeto .NET.

**Classe C\#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // create a Family object
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };

**JSON**

    {
        "id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam", 
                "givenName": "Jesse", 
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };

## Tradução de LINQ para SQL

O provedor de consultas do Banco de Dados de Documentos realiza um mapeamento de melhor esforço de uma consulta do LINQ para uma consulta da SQL do Banco de Dados de Documentos. Na descrição a seguir, presumimos uma familiaridade básica do leitor com o LINQ.

Primeiro, para o sistema de tipos, oferecemos suporte para todos os tipos de JSON primitivos - tipos numéricos, boolianos, cadeia de caracteres e nulo. Somente esses tipos de JSON são suportados. As expressões escalares são suportadas.

-   Valores constantes – incluem valores constantes dos tipos de dados primitivos no momento em que a consulta é avaliada.

-   Expressões de índice de propriedade/matriz – essas expressões se referem à propriedade de um objeto ou um elemento de matriz.

        family.Id;
        family.children[0].familyName;
        family.children[0].grade;
        family.children[n].grade; //n is an int variable

-   Expressões aritméticas - Incluem expressões aritméticas comuns em valores numéricos e boolianos. Para obter uma lista completa, consulte a especificação da SQL acima.

        2 * family.children[0].grade;
        x + y;

-   Expressão de comparação de cadeias de caracteres - incluem a comparação de um valor de cadeia de caracteres a um valor constante de cadeia de caracteres.

        mother.familyName == "Smith";
        child.givenName == s; //s is a string variable

-   Expressão de criação de objeto/matriz - estas expressões retornam um objeto do tipo de valor composto ou tipo anônimo ou uma matriz desses objetos. Esses valores podem ser aninhados.

        new Parent { familyName = "Smith", givenName = "Joe" };
        new { first = 1, second = 2 }; //an anonymous type with 2 fields              
        new int[] { 3, child.grade, 5 };

## Operadores de consulta

Aqui, temos alguns exemplos que ilustram como alguns dos operadores de consulta padrão do LINQ são traduzidos para consultas do Banco de Dados de Documentos.

### Operador Select

A sintaxe é `input.Select(x => f(x))`, em que `f` é uma expressão escalar.

**Expressão Lambda do LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL**

    SELECT VALUE f.parents[0].familyName
    FROM Families f

**Expressão Lambda do LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable

**SQL**

    SELECT VALUE f.children[0].grade + c
    FROM Families f 

**Expressão Lambda do LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });

**SQL**

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f

### Operador SelectMany

A sintaxe é `input.SelectMany(x => f(x))`, em que `f` é uma expressão escalar que retorna um tipo de coleção.

**Expressão Lambda do LINQ**

    input.SelectMany(family => family.children);

**SQL**

    SELECT VALUE child
    FROM child IN Families.children

### Operador Where

A sintaxe é `input.Where(x => f(x))`, em que `f` é uma expressão escalar que retorna um valor booliano.

**Expressão Lambda do LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 

**Expressão Lambda do LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3

## Consultas compostas

Os operadores acima podem ser compostos para formar consultas mais poderosas. Como o Banco de Dados de Documentos oferece suporte para coleções aninhadas, tal composição pode ser concatenada ou aninhada.

### Concatenação

A sintaxe é `input(.|.SelectMany())(.Select()|.Where())*`. Uma consulta concatenada pode iniciar com uma consulta `SelectMany` opcional seguida por operadores `Select` ou `Where` múltiplos.

**Expressão Lambda do LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"

**Expressão Lambda do LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL**

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3

**Expressão Lambda do LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);
            

**SQL**

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)

**Expressão Lambda do LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL**

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"

### Aninhamento

A sintaxe é `input.SelectMany(x=>x.Q())`, em que Q é um operador `Select`, `SelectMany` ou `Where`.

Em uma consulta aninhada, a consulta interior é aplicada a cada elemento da coleção externa. Um recurso importante é que a consulta interna pode se referir aos campos dos elementos na coleção exterior, como autojunções.

**Expressão Lambda do LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL**

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents

**Expressão Lambda do LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));
            

**SQL**

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"

**Expressão Lambda do LINQ**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL**

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName

# Executando consultas

O Banco de Dados de Documentos do Azure expõe recursos via API REST que podem ser chamados por meio de qualquer linguagem capaz de realizar solicitações HTTP/HTTPS. Além disso, o Banco de Dados de Documentos do Azure oferece bibliotecas de programação para várias linguagens populares, como .NET, Node.js, JavaScript e Python. A API REST e as diversas bibliotecas suportam a consulta por meio de SQL. O SDK .NET oferece suporte para consultas no LINQ além da SQL.

Os exemplos a seguir mostram como criar uma consulta e enviá-la a uma conta de banco de dados do Banco de Dados de Documentos.

## API REST

O Banco de Dados de Documentos oferece um modelo de programação RESTful em vez do HTTP. As contas do banco de dados podem ser provisionadas usando uma assinatura do Azure. O modelo de recurso do Banco de Dados de Documentos consiste de um conjunto de recursos em uma conta do banco de dados, cada um acessível por meio de um URI lógico e estável. Um conjunto de recursos é referido como um feed neste documento. Uma conta do banco de dados é formada por um conjunto de bancos de dados, cada um contendo diversas coleções, cada uma delas, por sua vez, contendo documentos, UDFs e outros tipos de recursos.

O modelo de interação básico com esses recursos é por meio dos verbos HTTP GET, PUT, POST e DELETE, com sua interpretação padrão. O verbo POST é usado para criação de um novo recurso, para executar um procedimento armazenado ou para emitir uma consulta do Banco de Dados de Documentos. As consultas sempre são operações somente leitura, sem efeitos colaterais.

Os exemplos a seguir mostram o POST para uma consulta do Banco de Dados de Documentos realizada em uma coleção que contém os dois documentos de amostra revisados até agora. A consulta tem um filtro simples na propriedade do nome JSON. Observe que o uso de cabeçalhos `x-ms-documentdb-isquery` e Content-Type: `application/sql` denota que a operação é uma consulta.

**Solicitação**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/sql

    SELECT * FROM Families f WHERE f.id = "AndersenFamily"

**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }

O segundo exemplo mostra uma consulta mais complexa que retorna múltiplos resultados da junção.

**Solicitação**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/sql

    SELECT 
         f.id AS familyName, 
         c.givenName AS childGivenName, 
         c.firstName AS childFirstName, 
         p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p in c.pets

**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }

Se os resultados de uma consulta não couberem em uma única página de resultados, a API REST retornará um token de continuação por meio do cabeçalho de resposta `x-ms-continuation-token`. Os clientes podem paginar os resultados incluindo o cabeçalho nos resultados subsequentes. O número de resultados por página também pode ser controlado por meio do cabeçalho de número `x-ms-max-item-count`.

Para gerenciar a política de consistência de dados para consultas, use o cabeçalho `x-ms-consistency-level` como todas as solicitações à API REST. Para consistência da sessão, é preciso ecoar o cabeçalho de cookie `x-ms-session-token` mais recente na solicitação de consulta. Observe que a política de indexação da coleção consultada também pode influenciar a consistência dos resultados da consulta. Com as configurações da política de indexação padrão, para as coleções o índice sempre estará atualizado com o conteúdo dos documentos e os resultados das consultas corresponderão à consistência escolhida para os dados. Se a política de indexação for relaxada para Lenta, as consultas poderão retornar resultados obsoletos. Para obter mais informações, consulte [Níveis de consistência do Banco de Dados de Documentos][].

Se a política de indexação configurada na coleção não puder suportar a consulta especificada, o servidor do Banco de Dados de Documentos retorna um 400, “Solicitação Incorreta". Este código é retornado para consultas de intervalo em caminhos configurados para pesquisas hash (igualdade), e para caminhos excluídos explicitamente da indexação. O cabeçalho `x-ms-documentdb-query-enable-scan` poderá ser especificado para permitir que a consulta realize uma verificação quando um índice não estiver disponível.

## SDK C\# (.NET)

O SDK .NET suporta consultas LINQ e SQL. O exemplo a seguir mostra como realizar a consulta de filtro simples mencionada no início deste documento.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }

Esta amostra compara duas propriedades quanto à igualdade dentro de cada documento e usa projeções anônimas.

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }

A amostra seguinte mostra junções, expressas por meio do LINQ SelectMany.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }

O cliente .NET itera automaticamente em todas as páginas dos resultados de pesquisa nos blocos foreach, conforme mostrado acima. As opções de consulta apresentadas nesta seção sobre a API REST também estão disponíveis no SDK .NET usando as classes `FeedOptions` e `FeedResponse` no método CreateDocumentQuery. O número de páginas pode ser controlado usando a configuração `MaxItemCount`.

Os desenvolvedores também podem controlar explicitamente as páginas criando `IDocumentQueryable` — usando objeto `IQueryable` —, lendo os valores`ResponseContinuationToken` e transmitindo-os como `RequestContinuationToken` nas `FeedOptions`. `EnableScanInQuery` pode ser configurado para permitir verificações quando a consulta não puder ser suportada pela política de indexação configurada.

Consulte [amostras Banco de Dados de Documentos .NET] ([http://code.msdn.microsoft.com/Azure-Banco de Dados de Documentos-NET-Code-6b3da8af\#content][]) para obter mais amostras de consultas.

## API do servidor JavaScript

O Banco de Dados de Documentos um modelo de programação para executar a lógica de aplicativos baseados em JavaScript diretamente nas coleções em termos de procedimentos armazenados e gatilhos. A lógica de JavaScript registrada no nível da coleção pode então emitir operações do banco de dados nas operações dos documentos da coleção determinada. Essas operações são encapsuladas em transações ACID ambiente.

O exemplo a seguir mostra como usar o queryDocuments na API do servidor do JavaScript para realizar consultas de dentro de procedimentos e gatilhos armazenados.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

# Referências

1.  [Introdução ao Banco de Dados de Documentos do Azure][]
2.  [Especificação da linguagem SQL do Banco de Dados de Documentos] (<http://go.microsoft.com/fwlink/p/?LinkID=510612>)
3.  [Amostras .NET do Banco de Dados de Documentos] ([http://code.msdn.microsoft.com/Azure-Banco de Dados de Documentos-NET-Code-6b3da8af\#content][])
4.  [Níveis de consistência do Banco de Dados de Documentos][]
5.  ANSI SQL 2011 - [][]<http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681></a>
6.  JSON [][1]<http://json.org/></a>
7.  Especificação Javascript [][2]<http://www.ecma-international.org/publications/standards/Ecma-262.htm></a>
8.  LINQ [][3]<http://msdn.microsoft.com/en-us/library/bb308959.aspx></a>
9.  Técnicas de avaliação de consulta para bancos de dados grandes [][4]<http://dl.acm.org/citation.cfm?id=152611></a>
10. Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13. G. Graefe. The Cascades framework for query optimization. IEEE Data Eng. Bull., 18(3): 1995.

  [http://code.msdn.microsoft.com/Azure-Banco de Dados de Documentos-NET-Code-6b3da8af\#content]: http://code.msdn.microsoft.com/Azure-DocumentDB-NET-Code-6b3da8af#content
  []: ./media/documentdb-sql-query/sql-query1.png
  [Níveis de consistência do Banco de Dados de Documentos]: ../documentdb-consistency-levels
  [Introdução ao Banco de Dados de Documentos do Azure]: ../documentdb-introduction
  []: http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681
  [1]: http://json.org/
  [2]: http://www.ecma-international.org/publications/standards/Ecma-262.htm
  [3]: http://msdn.microsoft.com/en-us/library/bb308959.aspx
  [4]: http://dl.acm.org/citation.cfm?id=152611
