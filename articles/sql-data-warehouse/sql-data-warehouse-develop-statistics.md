<properties
   pageTitle="Gerenciar estatísticas no SQL Data Warehouse | Microsoft Azure"
   description="Dicas para gerenciar estatísticas no SQL Data Warehouse do Azure para desenvolvimento de soluções."
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
   ms.date="12/11/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Gerenciar estatísticas no SQL Data Warehouse
 O SQL Data Warehouse usa estatísticas para avaliar o custo de diferentes maneiras de executar uma consulta distribuída. Quando as estatísticas são precisas, o otimizador de consulta pode gerar planos de consulta de alta qualidade que melhoram o desempenho da consulta.

A criação e atualização das estatísticas é importante para obter o desempenho de consultas que o SQL Data Warehouse foi projetado para fornecer. Este guia fornece uma visão geral sobre as estatísticas e mostra como:

- Criar estatísticas como parte do design do banco de dados
- Atualizar estatísticas como parte da manutenção do banco de dados
- Exibir estatísticas com funções e exibições do sistema

## Apresentação das estatísticas

As estatísticas de coluna única são objetos que contêm informações sobre o intervalo e a frequência de valores em uma única coluna. O otimizador de consulta usa esse histograma para estimar o número de linhas no resultado da consulta. Isso afeta diretamente as decisões sobre a otimização da consulta.

Estatísticas de várias colunas são estatísticas criadas em uma lista de colunas. Elas incluem estatísticas sobre uma única coluna na primeira coluna da lista, além de algumas informações de correlação entre colunas chamadas de densidades. As estatísticas de várias colunas podem melhorar o desempenho de consulta de algumas operações como junções compostas e agrupar por.

Para obter mais detalhes, confira [DBCC SHOW\_STATISTICS][] no MSDN.

## Por que as estatísticas são necessárias?
Sem estatísticas adequadas, você não conseguirá o desempenho para o qual o SQL Data Warehouse foi desenvolvido. O SQL Data Warehouse não gera automaticamente estatísticas para tabelas e colunas, portanto você precisa criá-las por conta própria. Convém criá-las durante a criação da tabela e atualizá-las após o preenchimento.

> [AZURE.NOTE]Se você usar o SQL Server, talvez dependa do SQL Server para criar e atualizar estatísticas de coluna única conforme necessário. O SQL Data Warehouse é diferente nesse aspecto. Como os dados são distribuídos, o SQL Data Warehouse não agrega automaticamente as estatísticas em todos os dados distribuídos. Ele gerará somente as estatísticas agregadas ao criar e atualizar as estatísticas.

## Quando criar estatísticas
Um conjunto coerente de estatísticas atualizadas é uma parte importante do SQL Data Warehouse. Portanto, é importante criar estatísticas como parte da criação de tabelas.

A criação de estatísticas de coluna única em cada coluna é uma maneira fácil de começar a trabalhar com estatísticas. No entanto, sempre há compensações entre o desempenho e o custo para a criação e atualização das estatísticas. Se você criar estatísticas de coluna única em todas as colunas e descobrir posteriormente que está demorando muito tempo para atualizar todas elas, remova algumas estatísticas ou atualize algumas delas com mais frequência do que outras.

As estatísticas de várias colunas são usadas pelo otimizador de consulta somente quando as colunas estão em cláusulas composite joins ou group by. No momento, os filtros compostos não se beneficiam de estatísticas de várias colunas.

Portanto, ao iniciar o desenvolvimento do SQL Data Warehouse convém implementar o seguinte padrão: criar estatísticas de coluna única em todas as colunas de todas as tabelas - criar estatísticas de várias colunas nas colunas usadas pelas consultas em cláusulas de junção e agrupar por.

Refine esse modelo conforme entender o modo como deseja consultar os dados, especialmente quando as tabelas são amplas. Confira a seção [Implementando o gerenciamento de estatísticas] (## Implementando o gerenciamento de estatísticas) para obter uma abordagem mais avançada do método.

## Quando atualizar as estatísticas
É importante incluir a atualização de estatísticas na sua rotina de gerenciamento de banco de dados. Quando a distribuição dos dados no banco de dados é alterada, as estatísticas precisam ser atualizadas. Caso contrário, o desempenho ficará inferior ao ideal e talvez os esforços para solucionar o problema da consulta não tenham êxito.

Portanto, uma das primeiras perguntas a serem feitas durante a solução de uma consulta é, "As estatísticas estão atualizadas?"

Essa questão não pode ser respondida pela idade. Um objeto de estatísticas atualizado pode ser muito antigo. Quando o número de linhas muda ou ocorre uma mudança substancial na distribuição dos valores de uma determinada coluna, *é necessário* atualizar as estatísticas.

Por exemplo, as colunas de data em um data warehouse normalmente precisam de atualizações frequentes de estatísticas. Sempre que há um carregamento de novas linhas no data warehouse, novas datas de carga ou datas de transação são adicionadas. Isso muda a distribuição de dados e desatualiza as estatísticas.

Por outro lado, talvez as estatísticas em uma coluna de gênero de uma tabela de clientes nunca precisem ser atualizadas. Supondo que a distribuição seja constante entre os clientes, adicionar novas linhas à variação de tabela não alterará a distribuição dos dados. No entanto, se o data warehouse contiver apenas um gênero, e um novo requisito resultar em vários gêneros, será definitivamente necessário atualizar as estatísticas na coluna gênero.

Para obter mais explicações, confira [Estatísticas][] no MSDN.

## Implementação do gerenciamento de estatísticas

Geralmente, convém estender os processos de carregamento de dados a fim de garantir que as estatísticas estejam atualizadas ao final do carregamento. É no carregamento de dados que as tabelas frequentemente mudam de tamanho e/ou distribuição de valores. Portanto, esse é um momento lógico para implementar alguns processos de gerenciamento.

Veja abaixo alguns princípios importantes para atualizar as estatísticas durante o processo de carregamento:

- Certifique-se de que cada tabela carregada tenha pelo menos um objeto de estatísticas atualizado. Isso atualiza as informações de tamanho da tabela (contagem de linhas e contagem de páginas) como parte da atualização de estatísticas.
- Concentre-se em colunas que participam de cláusulas JOIN, GROUP BY, ORDER BY e DISTINCT
- Considere uma atualização mais frequentes das colunas de "chave crescente", por exemplo, datas de transação, pois esses valores não serão incluídos no histograma de estatísticas.
- Considere atualizar as colunas de distribuição estática com menos frequência.
- Lembre-se de que cada objeto de estatística é atualizado em série. Simplesmente implementar `UPDATE STATISTICS <TABLE_NAME>` talvez não seja ideal, especialmente para tabelas mais amplas com muitos objetos de estatísticas.

> [AZURE.NOTE]Para obter mais detalhes sobre [chave crescente], confira o white paper sobre modelo de estimativa de cardinalidade do SQL Server 2014.

Para obter mais explicações, confira [Estimativa de cardinalidade][] no MSDN.

## Exemplos: criar estatísticas

Estes exemplos mostram como usar várias opções para a criação de estatísticas. As opções usadas para cada coluna dependem das características dos dados e de como a coluna será usada em consultas.

### R. Criar estatísticas de coluna única com opções padrão

Para criar estatísticas em uma coluna, basta fornecer um nome para o objeto de estatísticas e o nome da coluna.

Esta sintaxe usa todas as opções padrão. Por padrão, o SQL Data Warehouse utiliza uma amostragem de 20 por cento da tabela ao criar estatísticas.

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### B. Criar estatísticas de coluna única examinando cada linha

A taxa de amostragem padrão de 20 por cento é suficiente para a maioria das situações. No entanto, você pode ajustar essa taxa de amostragem.

Para usar toda a tabela como amostragem, use a seguinte sintaxe:

```
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### C. Criar estatísticas de coluna única, especificando o tamanho da amostra

Como alternativa, você pode especificar o tamanho da amostra como uma porcentagem:

```
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### D. Criar estatísticas de coluna única em apenas algumas das linhas

Outra opção é criar estatísticas em uma parte das linhas na tabela. Isso é chamado de estatística filtrada.

Por exemplo, ao planejar consultar uma partição específica de uma grande tabela particionada, você pode usar as estatísticas filtradas. Ao criar estatísticas apenas nos valores de partição, a precisão das estatísticas melhora e, portanto, o desempenho da consulta também.

Este exemplo cria estatísticas em um intervalo de valores. Os valores podem ser facilmente definidos de acordo com o intervalo de valores em uma partição.

```
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE]Para que o otimizador de consulta considere usar estatísticas filtradas ao escolher o plano de consulta distribuída, a consulta deve ser adequada à definição do objeto de estatísticas. Usando o exemplo anterior, a cláusula WHERE da consulta precisa especificar valores col1 entre 2000101 e 20001231.

### E. Criar estatísticas de coluna única com todas as opções

Obviamente, você pode combinar as opções. O exemplo abaixo cria um objeto de estatísticas filtradas com um tamanho de amostra personalizado:

```
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para obter a referência completa, confira [CRIAR ESTATÍSTICAS][] no MSDN.

### F. Criar estatísticas de várias colunas

Para criar estatísticas de várias colunas, basta usar os exemplos anteriores, mas especificar mais colunas.

> [AZURE.NOTE]O histograma, que é usado para estimar o número de linhas no resultado da consulta, só está disponível para a primeira coluna listada na definição do objeto da estatística.

Neste exemplo, o histograma está em *product\_category*. As estatísticas entre colunas são calculadas em *product\_category* e *product\_sub\_c\\ategory*:

```
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Como há uma correlação entre *product\_category* e *product\_sub\_category*, uma estatística de várias colunas pode ser útil se essas colunas forem acessadas ao mesmo tempo.

### G. Criar estatísticas em todas as colunas em uma tabela

É uma maneira de criar estatísticas é emitir comandos CREATE STATISTICS depois de criar a tabela.

```
CREATE TABLE dbo.table1 
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### H. Use um procedimento armazenado para criar estatísticas em todas as colunas em um banco de dados

O SQL Data Warehouse não tem um procedimento armazenado no sistema equivalente a [sp\_create\_stats][] no SQL Server. Esse procedimento armazenado cria um objeto de estatísticas de coluna única em todas as colunas do banco de dados que ainda não tenham estatísticas.

Isso ajudará você a começar com o design do banco de dados. Fique à vontade para adaptá-lo às suas necessidades.

```
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
	DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN	sys.[external_tables] e	ON	e.[object_id]		= t.[object_id]
WHERE       l.[object_id] IS NULL
AND			e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para criar estatísticas em todas as colunas na tabela com esse procedimento, simplesmente chame o procedimento.

```
prc_sqldw_create_stats;
```

## Exemplos: atualizar as estatísticas

Para atualizar as estatísticas, você pode:

1. Atualizar um objeto de estatísticas. Especifique o nome do objeto de estatísticas que você deseja atualizar.
2. Atualizar todos os objetos de estatísticas em uma tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específico.


### R. Atualizar um objeto de estatísticas específico ###
Use a sintaxe a seguir para atualizar um objeto de estatísticas específico:

```
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos de estatísticas específicos, você pode minimizar o tempo e os recursos necessários para o gerenciamento de estatísticas. No entanto, isso exige um pouco de planejamento para escolher os melhores objetos de estatísticas para atualizar.


### B. Atualizar todas as estatísticas em uma tabela ###
O código abaixo mostra um método simples para atualizar todos os objetos de estatísticas em uma tabela.

```
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```
UPDATE STATISTICS dbo.table1;
```

Esta instrução é fácil de usar. Lembre-se de que isso atualizará todas as estatísticas na tabela e, portanto, pode executar mais trabalho do que o necessário. Se o desempenho não for um problema, essa é definitivamente a maneira mais fácil e completa de garantir a atualização das estatísticas.

> [AZURE.NOTE]Ao atualizar todas as estatísticas em uma tabela, o SQL Data Warehouse realiza uma verificação da tabela para coletar amostragens para cada estatística. Se a tabela for grande, tiver muitas colunas e estatísticas, talvez seja mais eficiente para atualizar estatísticas individuais com base na necessidade.

Para a implementação de um procedimento `UPDATE STATISTICS`, confira o artigo [tabelas temporárias]. O método de implementação é ligeiramente diferente para o procedimento `CREATE STATISTICS` acima, mas o resultado final é o mesmo.

Para obter a sintaxe completa, confira [Atualizar estatísticas][] no MSDN.

## Metadados de estatísticas
Há várias funções e exibições do sistema que você pode usar para localizar informações sobre estatísticas. Por exemplo, você pode ver se um objeto de estatísticas está desatualizado usando a função stats-date para ver quando as estatísticas foram criadas ou atualizadas pela última vez.

### Exibições de catálogo para as estatísticas
Essas exibições do sistema fornecem informações sobre estatísticas:

| Exibição de catálogo | Descrição |
| :----------- | :---------- |
| [sys.columns][] | Uma linha para cada coluna. |
| [sys.objects][] | Uma linha para cada objeto no banco de dados. | |
| [sys.schemas][] | Uma linha para cada esquema no banco de dados. | |
| [sys.stats][] | Uma linha para cada objeto de estatísticas. |
| [sys.stats\_columns][] | Uma linha para cada coluna no objeto de estatísticas. Conecta novamente a sys.columns. |
| [sys.tables][] | Uma linha para cada tabela (inclui tabelas externas). |
| [sys.table\_types][] | Uma linha para cada tipo de dados. |


### Funções de sistema para estatísticas
Essas funções de sistema são úteis para trabalhar com estatísticas:

| Função de sistema | Descrição |
| :-------------- | :---------- |
| [STATS\_DATE][] | Data da última atualização do objeto de estatísticas. |
| [DBCC SHOW\_STATISTICS][] | Fornece informações detalhadas e resumidas sobre a distribuição de valores, conforme entendido pelo objeto de estatísticas. |

### Combinar colunas de estatísticas e funções em uma exibição

Essa exibição une as colunas relacionadas às estatísticas e os resultados da função [STATS\_DATE()][].

```
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1 
AND     st.[user_created] = 1
;
```

## Exemplos de DBCC SHOW\_STATISTICS()

DBCC SHOW\_STATISTICS() mostra os dados contidos em um objeto de estatísticas. Esses dados estão divididos em três partes.

1. Cabeçalho
2. Vetor de densidade
3. Histograma

Os metadados de cabeçalho sobre as estatísticas. O histograma exibe a distribuição de valores na primeira coluna de chave do objeto de estatísticas. O vetor de densidade mede a correlação entre colunas. O SQLDW calcula as estimativas de cardinalidade com os dados no objeto de estatísticas.

### Mostrar cabeçalho, densidade e histograma

Este exemplo simples mostra as três partes de um objeto de estatísticas.

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### Mostrar uma ou mais partes de DBCC SHOW\_STATISTICS();

Se você só estiver interessado em ver partes específicas, use a cláusula `WITH` e especifique quais partes deseja ver:

```
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## Diferenças do DBCC SHOW\_STATISTICS()
DBCC SHOW\_STATISTICS() é implementado mais estritamente no SQL Data Warehouse comparado ao SQL Server.

1. Não há suporte para recursos não documentados
- Não é possível usar Stats\_stream
- Não é possível associar os resultados para subconjuntos específicos de dados de estatísticas, por exemplo, (STAT\_HEADER JOIN DENSITY\_VECTOR)
2. NO\_INFOMSGS não pode ser definido para a supressão de mensagem
3. Não é possível usar colchetes em nomes de estatísticas
4. Não é possível usar nomes de coluna para identificar objetos de estatísticas
5. Não há suporte para o erro personalizado 2767


## Próximas etapas
Para obter mais dicas de desenvolvimento, confira [Visão geral sobre o desenvolvimento no SQL Data Warehouse][].

<!--Image references-->

<!--Link references--In actual articles, you only need a single period before the slash.-->
[Visão geral sobre o desenvolvimento no SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[tabelas temporárias]: ./sql-data-warehouse-develop-temporary-tables.md

<!-- External Links -->
[Estimativa de cardinalidade]: https://msdn.microsoft.com/library/dn600374.aspx
[CRIAR ESTATÍSTICAS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW\_STATISTICS]: https://msdn.microsoft.com/library/ms174384.aspx
[Estatísticas]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS\_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats\_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table\_types]: https://msdn.microsoft.com/library/bb510623.aspx
[Atualizar estatísticas]: https://msdn.microsoft.com/library/ms187348.aspx

<!---HONumber=AcomDC_1217_2015-->