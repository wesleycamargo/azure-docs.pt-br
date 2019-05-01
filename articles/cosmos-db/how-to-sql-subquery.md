---
title: Subconsultas SQL do Azure Cosmos DB
description: Saiba mais sobre subconsultas SQL e seus casos de uso comuns no Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/19/2019
ms.author: tisande
ms.openlocfilehash: 3ba547aea9034777fe76f3c911efd2648f6184fa
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514793"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exemplos de subconsulta SQL do Azure Cosmos DB

Uma subconsulta é uma consulta aninhada em outra consulta. Uma subconsulta também é chamada de uma consulta interna ou seleção interna e a instrução que contém uma subconsulta é normalmente chamada de uma consulta externa.

Há dois tipos de subconsultas:

* Correlacionado - uma subconsulta correlacionado é uma subconsulta que faz referência a valores da consulta externa. A subconsulta é avaliada uma vez para cada linha que é processada pela consulta externa.

* Não correlacionados - a subconsulta correlacionada A não é uma subconsulta que é independente da consulta externa e ele pode ser executado em seu próprio sem depender da consulta externa.

> [!NOTE]
> O Azure Cosmos DB dá suporte a subconsultas correlacionadas apenas.

## <a name="types-of-subqueries"></a>Tipos de subconsultas

Subconsultas podem ser classificadas ainda mais com base no número de linhas e colunas que elas retornam. Há três tipos diferentes:
1.  **Tabela**: Retorna várias linhas e várias colunas
2.  **Com vários valores**: Retorna uma única coluna e várias linhas
3.  **Scalar**: Retorna uma única linha e coluna única

> [!NOTE]
> O Azure Cosmos DB dá suporte a subconsultas com vários valores e escalar

Consultas de SQL do Cosmos DB do Azure sempre retornam uma única coluna (um valor simples ou um documento complexo). Portanto, apenas com vários valores e escalar subconsultas acima são aplicáveis no Azure Cosmos DB. Uma subconsulta com vários valores só pode ser usada na cláusula FROM como uma expressão relacional, enquanto uma subconsulta escalar pode ser usada como uma expressão escalar em SELECT ou na cláusula WHERE ou como uma expressão relacional na cláusula FROM.


## <a name="multi-value-subqueries"></a>Subconsultas com vários valores

Subconsultas com vários valores retornam um conjunto de documentos e são sempre usadas dentro da cláusula FROM. Elas são usadas para:

* Otimizando a expressões de junção 
* Avaliar expressões caras de uma vez e fazer referência a várias vezes

### <a name="optimize-join-expressions"></a>Otimizar expressões de junção

Subconsultas com vários valores podem otimizar expressões de junção, envio de predicados após cada expressão select-muitos em vez de após todas as junções cruzadas na cláusula WHERE.

Considere a seguinte consulta:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

Para essa consulta, o índice corresponderá a qualquer documento que tem uma marca com a nome 'infant fórmula', um item nutrient com um valor entre 0 e 10 e um item de serviço com uma quantidade maior que 1. No entanto, a expressão de junção executará o produto cruzado de todos os itens de marcas, nutrients e porções matrizes para cada documento correspondente antes de qualquer filtro é aplicado. A cláusula WHERE, em seguida, aplicará a filtro predicado em cada < c, t, n, s > tupla. Por exemplo, se um documento correspondente tiver 10 itens em cada uma das três matrizes, ela será expandida para 1 x 10 x 10 x 10 (ou seja, 1.000) tuplas. Usando subconsultas aqui, pode ajudar a filtragem de itens de matriz unida antes de unir a próxima expressão.

Essa consulta é equivalente à mostrado acima, mas utiliza subconsultas:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Supondo que apenas um item na matriz de marcas correspondentes ao filtro e cinco itens para nutrients e matrizes porções, as expressões de junção será expandido para 1 x 1 x 5 x 5 = 25 itens em vez de 1.000 itens na primeira consulta.

### <a name="evaluate-once-and-reference-many-times"></a>Avaliar uma vez e referência muitas vezes

Subconsultas podem ajudar a otimizar as consultas com expressões caras, como funções definidas pelo usuário (UDF) ou a cadeia de caracteres complexa ou expressões aritméticas. Você pode usar uma subconsulta, juntamente com uma expressão de junção para avaliar a expressão de uma vez mas referenciá-lo muitas vezes.

A consulta a seguir executa o UDF GetMaxNutritionValue duas vezes:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Aqui está uma consulta equivalente que executa o UDF somente uma vez:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Considerando o comportamento de produto cruzado das expressões de junção, se a expressão de UDF pode ser avaliada como indefinida, você deve garantir que a expressão de junção sempre produz uma única linha, retornando um objeto da subconsulta, em vez do valor diretamente.
>

Aqui está um exemplo semelhante que retorna um objeto em vez de um valor:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

A abordagem não é limitada a UDFs, mas em vez disso, a qualquer expressão potencialmente cara. Por exemplo, podemos pode levar a mesma abordagem com a média de função matemática:

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

### <a name="mimic-join-with-external-reference-data"></a>Imitar a junção com dados de referência externa

Muitas vezes, precisamos fazer referência a dados estáticos que raramente são alterados, como unidades de medidas ou códigos de país. Para esses dados, é preferível não duplicá-lo para cada documento. Evitar essa duplicação salvará no armazenamento e melhorar o desempenho de gravação, mantendo o tamanho do documento menores. Uma subconsulta pode ser usada aqui para imitar a semântica de associação interna com um conjunto de dados de referência.
Por exemplo, considere este conjunto de dados de referência.

| **Unidade** | **Nome**            | **Multiplicador** | **Unidade base** |
| -------- | ------------------- | -------------- | ------------- |
| ng       | Nanogram            | 1.00E-09       | Grama          |
| µg       | Microgram           | 1.00E-06       | Grama          |
| mg       | Miligrama           | 1.00E-03       | Grama          |
| g        | Grama                | 1.00E + 00       | Grama          |
| kg       | Kilogram            | 1.00E + 03       | Grama          |
| Mg       | Megagram            | 1.00E + 06       | Grama          |
| Gg       | Gigagram            | 1.00E + 09       | Grama          |
| nJ       | Nanojoule           | 1.00E-09       | Joule         |
| µJ       | Microjoule          | 1.00E-06       | Joule         |
| mJ       | Millijoule          | 1.00E-03       | Joule         |
| J        | Joule               | 1.00E + 00       | Joule         |
| kJ       | Kilojoule           | 1.00E + 03       | Joule         |
| MJ       | Megajoule           | 1.00E + 06       | Joule         |
| GJ       | Gigajoule           | 1.00E + 09       | Joule         |
| cal      | Calorias             | 1.00E + 00       | calorie       |
| kcal     | Calorias             | 1.00E + 03       | calorie       |
| IU       | Unidades internacionais |                |               |


A consulta a seguir imita unindo-se com esses dados para que podemos adicionar o nome da unidade para a saída:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Subconsultas escalares

Uma expressão de subconsulta escalar é uma subconsulta que é avaliada como um único valor. O valor da expressão de subconsulta escalar é o valor da projeção (cláusula SELECT) da subconsulta.  Uma expressão de subconsulta escalar pode ser usada em muitos lugares uma expressão escalar é válida. Por exemplo, uma subconsulta escalar pode ser usada em qualquer expressão em ambos os SELECT e cláusulas WHERE.
No entanto, usar uma subconsulta escalar não sempre ajuda a otimizar. Por exemplo, passando uma subconsulta escalar como um argumento para um sistema ou funções definidas pelo usuário não oferece nenhum benefício no consumo de RU ou a latência.

Subconsultas escalares podem ser ainda mais classificadas como:
* Subconsultas escalares de expressão simples
* Agregadas subconsultas escalares

### <a name="simple-expression-scalar-subqueries"></a>Subconsultas escalares de expressão simples

Uma subconsulta escalar de expressão simples é uma subconsulta correlacionada que tem uma cláusula SELECT que não contém quaisquer expressões de agregação. Essas subconsultas não fornecem benefícios nenhuma otimização porque o compilador converte-os em uma expressão simple maior. Não há nenhum contexto correlacionado entre a consulta interna e externa.

Veja alguns exemplos:

**Exemplo 1**

```sql
SELECT 1 AS a, 2 AS b
```

Essa consulta poderia ser reescrita, usando uma subconsulta escalar de expressão simples para:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Ambas as consultas produzem essa saída:

```json
[
  { "a": 1, "b": 2 }
]
```

**Exemplo 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Essa consulta poderia ser reescrita, usando uma subconsulta escalar de expressão simples para:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Saída da consulta:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Exemplo 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Essa consulta poderia ser reescrita, usando uma subconsulta escalar de expressão simples para:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Saída da consulta:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

## <a name="aggregate-scalar-subqueries"></a>Agregadas subconsultas escalares

Uma subconsulta escalar de agregação é uma subconsulta que tem uma função de agregação em sua projeção ou um filtro que é avaliada como um único valor.

**Exemplo 1:**

Aqui está uma subconsulta com uma expressão de função de agregação único em sua projeção:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Saída da consulta:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Exemplo 2**

Uma subconsulta com várias expressões de função de agregação:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Saída da consulta:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Exemplo 3**

Uma consulta com uma subconsulta agregação na projeção e o filtro:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Saída da consulta:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Uma maneira melhor de escrever essa consulta é unir a subconsulta e fazer referência a subconsulta alias em ambos os SELECT e cláusulas WHERE. Essa consulta é mais eficiente, porque será necessário executar a subconsulta somente dentro da instrução de junção e não na projeção e o filtro.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

### <a name="exists-expression"></a>Expressão EXISTS

O Azure Cosmos DB dá suporte a expressões EXISTS. Essa é uma subconsulta escalar de agregação incorporada a API de SQL do Azure Cosmos DB. EXISTS é uma expressão booleana que usa uma expressão de subconsulta e retorna true se a subconsulta não retorna nenhuma linha; Caso contrário, retorna false.
Uma vez que a API de SQL do Azure Cosmos DB não diferenciar entre expressões Boolianas e quaisquer outras expressões escalares, EXISTS pode ser usado em ambos os SELECT e cláusulas WHERE. Isso é diferente de T-SQL, onde uma expressão booliana (por exemplo, EXISTS, BETWEEN e IN) é restrita ao filtro.

Se a subconsulta EXISTS retornar um único valor é indefinido, EXISTS será avaliada como falsa. Por exemplo, considere a seguinte consulta que é avaliada como false:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


No entanto, se a palavra-chave do valor na subconsulta acima for omitida, em seguida, a consulta será avaliada como verdadeira:
```sql
SELECT EXISTS (SELECT undefined) 
```

A subconsulta abrangerá a lista de valores na lista de seleção em um objeto. Se a lista selecionada não tem valores, a subconsulta retornará o valor único '{}' que é definido e existe, portanto, é avaliada como true.

### <a name="example-rewriting-arraycontains-and-join-as-exists"></a>Exemplo: Reconfiguração ARRAY_CONTAINS e junção Exists

É um caso de uso comum de ARRAY_CONTAINS filtrar um documento pela existência de um item em uma matriz. Nesse caso, estamos verificando se a matriz de marcas contém uma item chamada laranja.

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

A mesma consulta poderia ser reescrita para usar EXISTS:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Além disso, ARRAY_CONTAINS só é capaz de verificar se um valor é igual a qualquer elemento dentro de uma matriz. Se os filtros mais complexos são necessários nas propriedades de matriz, uma junção é necessária.

Considere a seguinte consulta que filtra com base nas unidades e nutritionValue propriedades na matriz: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

Para cada um dos documentos na coleção, um produto cruzado é executado com seus elementos de matriz. Esta operação de junção torna possível filtrar em propriedades dentro da matriz. No entanto, o consumo de RU dessa consulta será significativo. Por exemplo, se 1.000 documentos tinham 100 itens em cada matriz, ela será expandida para 1.000 x 100 (ou seja, 100.000) tuplas.

Usando `EXISTS` pode ajudar a evitar esse produto cruzado caro:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

Nesse caso, vamos filtrar elementos de matriz dentro da subconsulta EXISTS. Se um elemento de matriz corresponde ao filtro, então, o projeto e `EXISTS` for avaliada como true.

Podemos também alias EXISTS e referenciá-lo na projeção:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Saída da consulta:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

### <a name="array-expression"></a>Expressão de matriz

O `ARRAY` expressão pode ser usada para projetar os resultados de uma consulta como uma matriz. Esta expressão pode ser usada somente dentro da cláusula SELECT da consulta.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Saída da consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Assim como acontece com outras subconsultas, filtra com o `ARRAY` expressão são possíveis.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Saída da consulta:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Expressões de matriz também podem vir após a cláusula FROM em subconsultas.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Saída da consulta:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Próximas etapas

- [Exemplos de consulta SQL](how-to-sql-query.md)
- [Amostras do .NET no Azure Cosmos DB](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Dados de documento de modelo](modeling-data.md)
