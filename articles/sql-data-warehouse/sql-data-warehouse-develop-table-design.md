<properties
   pageTitle="Design de tabela no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para projetar tabelas no SQL Data Warehouse do Azure para desenvolvimento de soluções."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/22/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Design da tabela no SQL Data Warehouse #
SQL Data Warehouse é um sistema de banco de dados distribuído de processamento extremamente paralelo (MPP). Ele armazena dados em vários locais diferentes, conhecidos como **distribuições**. Cada **distribuição** é como um bucket, armazenando um único subconjunto de dados no data warehouse. Ao distribuir os dados e a funcionalidade de processamento em vários nós, o SQL Data Warehouse pode oferecer uma enorme escalabilidade, muito além de qualquer sistema individual.

Quando uma tabela é criada no SQL Data Warehouse, na verdade ela é espalhada por todas as distribuições.

Este artigo abordará os seguintes tópicos:

- Tipos de dados com suporte
- Princípios da distribuição de dados
- Distribuição round robin
- Distribuição de hash
- Particionamento de tabela
- Estatísticas
- Recursos sem suporte

## Tipos de dados com suporte
O SQL Data Warehouse oferece suporte aos tipos comuns de dados corporativos:

- **bigint**
- **binary**
- **bit**
- **char**
- **date**
- **datetime**
- **datetime2**
- **datetimeoffset**
- **decimal**
- **float**
- **int**
- **money**
- **nchar**
- **nvarchar**
- **real**
- **smalldatetime**
- **smallint**
- **smallmoney**
- **time**
- **tinyint**
- **varbinary**
- **varchar**

É possível identificar colunas no seu data warehouse que contenham tipos incompatíveis usando a seguinte consulta:

```
SELECT  t.[name]
,       c.[name]
,       c.[system_type_id]
,       c.[user_type_id]
,       y.[is_user_defined]
,       y.[name]
FROM sys.tables  t
JOIN sys.columns c on t.[object_id]    = c.[object_id]
JOIN sys.types   y on c.[user_type_id] = y.[user_type_id]
WHERE y.[name] IN
                (   'geography'
                ,   'geometry'
                ,   'hierarchyid'
                ,   'image'
                ,   'ntext'
                ,   'numeric'
                ,   'sql_variant'
                ,   'sysname'
                ,   'text'
                ,   'timestamp'
                ,   'uniqueidentifier'
                ,   'xml'
                )

OR  (   y.[name] IN (  'nvarchar','varchar','varbinary')
    AND c.[max_length] = -1
    )
OR  y.[is_user_defined] = 1
;

```

A consulta inclui todos os tipos de dados definidos pelo usuário que não têm suporte.

Veja abaixo algumas alternativas que você pode usar no lugar de tipos de dados sem suporte.

Em vez de:

- **geometry**, use um tipo varbinary
- **geography**, use um tipo varbinary
- **hierarchyid**, tipo CLR não nativo
- **image**, **text**, **ntext** quando tiver base em texto, use varchar/nvarchar (quanto menor, melhor)
- **nvarchar(max)**, use nvarchar(4000) ou menor para um melhor desempenho
- **numeric**, use decimais
- **sql\_variant**, divida a coluna em várias colunas fortemente tipadas
- **sysname**, use nvarchar(128)
- **table**, converta em tabelas temporárias
- **timestamp**, retrabalhe o código para usar datetime2 e a função `CURRENT_TIMESTAMP`. Observe que não é possível ter current\_timestamp como uma restrição padrão, e o valor não será atualizado automaticamente. Se você precisar migrar valores de rowversion de uma coluna tipada como timestamp, use BINARY(8) ou VARBINARY(8) para valores de versão de linha NOT NULL ou NULL.
- **varchar(max)**, use varchar(8000) ou menor para obter o melhor desempenho
- **uniqueidentifier**, use varbinary(8)
- **tipos definidos pelo usuário**, converta de volta para os tipos nativos sempre que possível
- **xml**, use um varchar(8000) ou menor para obter o melhor desempenho. Divida entre colunas, se for necessário.

Suporte parcial:

- As restrições padrão oferecem suporte apenas a literais e constantes. Não há suporte para expressões ou funções não determinísticas como `GETDATE()` ou `CURRENT_TIMESTAMP`.

> [AZURE.NOTE]Defina as tabelas para que o tamanho máximo possível da linha, incluindo o comprimento total das colunas de tamanho variável, não exceda 32.767 bytes. Embora seja possível definir uma linha com dados de tamanho variável que possam exceder esse número, não será possível inserir dados na tabela. Além disso, tente limitar o tamanho de suas colunas de tamanho variável para obter uma taxa de transferência ainda melhor na execução de consultas.

## Princípios da distribuição de dados

Há duas opções de distribuição de dados no SQL Data Warehouse:

1. Distribuir os dados uniformemente, mas aleatoriamente 
2. Distribuir os dados com base nos valores de hash de uma única coluna

A distribuição de dados é decidida ao nível da tabela. Todas as tabelas são distribuídas. Você atribuirá a distribuição a cada tabela no banco de dados do SQL Data Warehouse.

A primeira opção é conhecida como distribuição do tipo **round robin**, às vezes conhecida como hash aleatório. Pense nela como a opção padrão ou mais segura.

A segunda opção é conhecida como distribuição de **hash**. Você pode considerá-la uma forma aprimorada de distribuição de dados. Essa opção é preferível quando clusters de tabelas compartilham critérios comuns de junção e/ou agregação.

## Distribuição round robin

A distribuição round robin é um método de propagação de dados de máxima uniformidade em todas as distribuições. Os buffers que contêm linhas de dados são alocados em turnos (daí o nome round robin, algo como “por turnos”) para cada distribuição. O processo é repetido até que todos os buffers de dados sejam alocados. Em nenhum estágio os dados são classificados ou ordenados em uma tabela distribuída por round robin. Uma distribuição round robin é chamada às vezes de hash aleatório por esse motivo. Os dados são distribuídos com o máximo de uniformidade possível entre as distribuições.

Veja abaixo um exemplo de tabela distribuída por round robin:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

Este também é um exemplo de uma tabela distribuída por round robin:

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
)
;
```

> [AZURE.NOTE]Observe que o segundo exemplo acima não faz menção à chave de distribuição. Round robin é o padrão e, portanto, não é absolutamente necessário. No entanto, ser explícito é uma prática recomendada, pois assim seus colegas estarão cientes de suas intenções ao examinar o design da tabela.

Normalmente, esse tipo de tabela é usado quando não há uma coluna de chave óbvia para basear o hash dos dados. Ela também pode ser usada por tabelas menores ou menos importantes nas quais o custo do movimento talvez não seja tão bom.

O carregamento de dados em uma tabela distribuída por round robin tende a ser mais rápido do que o carregamento em uma tabela distribuída por hash. Com uma tabela distribuída por round robin, não há a necessidade de entender os dados ou executar o hash antes do carregamento. Por esse motivo, as tabelas round robin normalmente são bons destinos de carregamento.

> [AZURE.NOTE]Quando são distribuídos por round robin, os dados são alocados para a distribuição no nível do *buffer*.

### Recomendações

Considere usar a distribuição round robin para a sua tabela nos seguintes cenários:

- Quando não houver uma chave de junção óbvia
- Se uma chave de distribuição de hash candidata não for conhecida
- Se a tabela não compartilhar uma chave de junção comum com outras tabelas
- Se a junção for menos significativa do que outras junções na consulta
- Quando a tabela for uma tabela de carregamento inicial

## Distribuição hash

A distribuição hash usa uma função interna para distribuir um conjunto de dados entre as distribuições por meio do hash de uma única coluna. Quando os dados passam por hash, não têm uma ordem explícita de alocação em uma distribuição. No entanto, o hash por si só é um processo determinístico. Isso torna os resultados do hash previsíveis. Por exemplo, a aplicação de hash em uma coluna de inteiros contendo o valor 10 sempre produzirá o mesmo valor de hash. Isso significa que ***qualquer*** coluna de inteiros com hash contendo o valor 10 acabaria sendo alocada para a mesma distribuição. Isso ocorre mesmo entre tabelas.

A previsibilidade do hash é extremamente importante. Isso significa que os dados distribuídos por hash podem gerar aprimoramentos de desempenho ao ler dados e unir tabelas.

Você verá abaixo que a distribuição de hash pode ser muito eficiente para otimização da consulta. Por isso, é considerada uma forma otimizada de distribuição de dados.

> [AZURE.NOTE]Lembre-se! O hash não tem base no valor dos dados, mas no tipo dos dados que estão passando por hash.

Veja abaixo uma tabela que foi distribuída por hash, por ProductKey.

```
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
)
;
```

> [AZURE.NOTE]Quando são distribuídos por hash, os dados são alocados para a distribuição no nível da linha.

## Partições de tabela
As partições de tabela recebem suporte e são fáceis de definir.

Exemplo de comando `CREATE TABLE` particionado do SQL Data Warehouse :

```
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

Observe que não há nenhuma função ou esquema de particionamento na definição. Tudo isso é resolvido no momento de criação da tabela. Tudo o que você precisa fazer é identificar os pontos limite da coluna que será a chave de particionamento.

## Estatísticas

O SQL Data Warehouse usa um otimizador de consulta distribuída para criar o plano de consulta apropriado quando os usuários consultam tabelas. Após a criação, o plano de consulta fornece a estratégia e o método usado pelo banco de dados para acessar os dados e atender à solicitação do usuário. O otimizador de consulta do SQL Data Warehouse tem base no custo. Em outras palavras, ele compara várias opções (planos) com base em seu custo relativo e escolhe o plano mais eficiente disponível. Consequentemente, o SQL Data Warehouse precisa de muitas informações para tomar decisões informadas e baseadas no custo. Ele contém informações estatísticas sobre a tabela (para o tamanho da tabela) e em objetos de banco de dados conhecidos como `STATISTICS`.

As estatísticas ocorrem com base em uma ou várias colunas de índices ou tabelas. Elas fornecem informações importantes sobre a cardinalidade e a seletividade dos valores ao otimizador baseado em custo. Isso é particularmente interessante quando o otimizador precisa avaliar cláusulas JOIN, GROUP BY, HAVING e WHERE em uma consulta. Portanto, é muito importante que as informações contidas nesses objetos de estatísticas reflitam *com precisão* o estado atual da tabela. É fundamental entender que a precisão do custo é importante. Se as estatísticas refletirem com precisão o estado da tabela, os planos poderão ser comparados para obter o menor custo. Se elas não forem precisas, o SQL Data Warehouse poderá escolher o plano errado.

As estatísticas no nível da coluna no SQL Data Warehouse são definidas pelo usuário.

Em outras palavras, precisamos criá-las sozinhos. Como acabamos de aprender, isso não é algo a ser ignorado. Essa é uma diferença importante entre o SQL Server e o SQL Data Warehouse. O SQL Server criará automaticamente as estatísticas quando as colunas forem consultadas. Por padrão, o SQL Server atualizará automaticamente essas estatísticas. No entanto, no SQL Data Warehouse, as estatísticas precisam ser criadas e gerenciadas manualmente.

### Recomendações

Aplique as seguintes recomendações para gerar estatísticas:

1. Crie estatísticas de coluna única em colunas usadas em cláusulas `WHERE`, `JOIN`, `GROUP BY`, `ORDER BY` e `DISTINCT`
2. Gere estatísticas de várias colunas em cláusulas compostas
3. Atualize as estatísticas periodicamente. Lembre-se de que isso não é feito automaticamente!

>[AZURE.NOTE]É comum que o SQL Server Data Warehouse confie exclusivamente em `AUTOSTATS` para manter as estatísticas de coluna atualizadas. Essa não é uma prática recomendada nem para data warehouses do SQL Server. As `AUTOSTATS` são disparadas por uma taxa de 20% de alteração, algo que talvez não seja suficiente para grandes tabelas de fatos contendo milhões ou bilhões de linhas. Portanto, convém manter-se sempre bem informado sobre as atualizações de estatísticas, a fim de garantir que elas reflitam com precisão a cardinalidade da tabela.

## Recursos sem suporte
O SQL Data Warehouse não usa ou oferece suporte a estes recursos:

- chaves primárias
- chaves estrangeiras
- restrições de verificação
- restrições exclusivas
- índices exclusivos
- colunas computadas
- colunas esparsas
- tipos definidos pelo usuário
- exibições indexadas
- identidades
- sequências
- gatilhos
- sinônimos


## Próximas etapas
Para obter mais dicas de desenvolvimento, consulte [Visão geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Visão geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=Oct15_HO3-->