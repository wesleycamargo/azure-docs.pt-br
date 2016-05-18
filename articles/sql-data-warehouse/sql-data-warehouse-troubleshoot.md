<properties
   pageTitle="Solução de problemas | Microsoft Azure"
   description="Solução de problemas do SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/20/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Solucionar problemas
O tópico a seguir lista alguns dos problemas mais comuns com os quais os clientes se deparam com o Azure SQL Data Warehouse.

## Conectividade
Conectar-se ao Azure SQL Data Warehouse pode falhar por dois motivos comuns:

- Regras de firewall não definidas
- Uso de ferramentas/protocolos sem suporte

### Regras de firewall
Bancos de Dados SQL do Azure são protegidos pelo servidor e firewalls de nível de banco de dados para garantir que somente os endereços IP conhecidos possam acessar os bancos de dados. Os firewalls são seguros por padrão – que significa que você deve permitir o acesso do endereço IP antes que possa se conectar.

Para configurar seu firewall para acesso, siga as etapas a [Configurar o acesso ao servidor de firewall para o IP do cliente][] seção o [provisionar][] página.

### Uso de ferramentas/protocolos sem suporte
O SQL Data Warehouse dá suporte a [Visual Studio 2013/2015][] como ambientes de desenvolvimento e [SQL Server Native Client (ODBC) 10/11][] para conectividade de cliente.

Consulte nossas páginas [Conectar][] para saber mais.

## Desempenho de consulta

### Estatísticas

[Estatísticas][] são objetos que contêm informações sobre o intervalo e a frequência de valores em uma coluna de banco de dados. O mecanismo de consulta usa essas estatísticas para otimizar a execução de consulta e melhorar o desempenho de consulta. Diferentemente do SQL Server ou do Banco de Dados SQL, o SQL Data Warehouse não cria ou atualiza as estatísticas automaticamente. As estatísticas devem ser manualmente mantidas em todas as tabelas.

Você pode usar a consulta a seguir para determinar a última vez que suas estatísticas foram atualizadas em cada tabela.

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

### Qualidade do segmento columnstore clusterizado

A qualidade do segmento columnstore clusterizado é importante para o desempenho ideal da consulta em tabelas de columnstore clusterizadas. A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado. A consulta a seguir identifica as tabelas com baixa integridade de segmento de índice columnstore e gera o T-SQL para recriar o índice columnstore nessas tabelas. A primeira coluna do resultado de consulta dá a você o T-SQL para recriar cada índice. A segunda coluna fornece uma recomendação para a classe de recurso mínimo a usar na otimização da compactação.
 
**ETAPA 1:** executar essa consulta em cada banco de dados do SQL Data Warehouse para identificar todos os índices columnstore de cluster abaixo do ideal. Se nenhuma linha for retornada, a regressão não afetará você e nenhuma outra ação será necessária.

```sql
SELECT 
     'ALTER INDEX ALL ON ' + s.name + '.' + t.NAME + ' REBUILD;' AS [T-SQL to Rebuild Index]
    ,CASE WHEN n.nbr_nodes < 3 THEN 'xlargerc' WHEN n.nbr_nodes BETWEEN 4 AND 6 THEN 'largerc' ELSE 'mediumrc' END AS [Resource Class Recommendation]
    ,s.name AS [Schema Name]
    ,t.name AS [Table Name]
    ,AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) AS [Ave Rows in Compressed Row Groups]
FROM 
    sys.pdw_nodes_column_store_row_groups rg
    JOIN sys.pdw_nodes_tables pt 
        ON rg.object_id = pt.object_id AND rg.pdw_node_id = pt.pdw_node_id AND pt.distribution_id = rg.distribution_id
    JOIN sys.pdw_table_mappings tm 
        ON pt.name = tm.physical_name
    INNER JOIN sys.tables t 
        ON tm.object_id = t.object_id
INNER JOIN sys.schemas s
    ON t.schema_id = s.schema_id
CROSS JOIN (SELECT COUNT(*) nbr_nodes  FROM sys.dm_pdw_nodes WHERE type = 'compute') n
GROUP BY 
    n.nbr_nodes, s.name, t.name
HAVING 
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000
ORDER BY 
    s.name, t.name
```
 
**ETAPA 2:** aumentar a classe de recurso de um usuário que tem permissões para recriar o índice nessa tabela na classe de recurso recomendada da coluna 2 da consulta acima.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  O LoadUser acima deve ser um usuário válido criado para executar a instrução ALTER INDEX. A classe de recurso do usuário db\_owner não pode ser alterada. Mais informações sobre classes de recursos e como criar um novo usuário podem ser encontradas no link abaixo.

 
**Etapa 3:** fazer logon como o usuário da etapa 2 (por exemplo "LoadUser"), que agora está usando uma classe de recurso mais alta, e executar as instruções ALTER INDEX geradas pela consulta na ETAPA 1. Verifique se esse usuário tem a permissão ALTER para as tabelas identificadas na consulta da ETAPA 1.
 
**ETAPA 4:** executar novamente a consulta da etapa 1. Se os índices foram criados com eficiência, nenhuma linha deve retornar nessa consulta. Se nenhuma linha for retornada, você estará liberado. Se você tem vários bancos de dados do SQL DW, repita esse processo em cada um dos seus bancos de dados. Se houver retorno de linhas, continue na etapa 5.
 
**Etapa 5:** se linhas são retornadas quando você executa novamente a consulta da etapa 1, talvez você tenha tabelas com linhas muito largas que precisam de grande quantidade de memória para construir os índices columnstore clusterizados de forma ideal. Se esse for o caso, repita o processo para a tabela usando a classe xlargerc. Para alterar a classe do recurso, repita a etapa 2 usando xlargerc. Em seguida, repita a etapa 3 para as tabelas que ainda estão com índices de qualidade inferior. Se você estiver usando um DW100 - DW300 e já usou o xlargerc, poderá optar por deixar os índices como estão ou aumentar temporariamente a DWU a fim de fornecer mais memória para esta operação.
 
**Etapas finais:** a classe de recurso designada acima é a classe de recurso mínima recomendada para criar os índices columnstore de maior qualidade. Recomendamos que você mantenha essa configuração para o usuário que carrega os dados. No entanto, se você quiser desfazer a alteração da etapa 2, poderá fazer isso com o comando a seguir.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```


A orientação para a classe de recurso mínima para carregamentos de uma tabela CCI é usar xlargerc para DW100 DW300, largerc para DW400 DW600 e mediumrc para qualquer coisa em DW1000 ou acima. Este guia é uma prática recomendada para a maioria das cargas de trabalho. O objetivo é fornecer a cada operação de criação de índice 400 MB ou mais de memória. No entanto, uma solução não serve para todas. A memória necessária para otimizar um índice columnstore depende dos dados carregados, que são basicamente influenciados pelo tamanho da linha. Tabelas com linhas mais estreitas precisam de menos memória, e mais largas precisam de mais memória. Se você quiser experimentar, poderá usar a consulta da etapa 1 para ver se consegue obter os índices columnstore ideais em alocações de memória menores. No mínimo, o ideal é ter em média mais de 100 mil linhas por grupo de linhas. Acima de 500 mil é ainda melhor. O máximo que você verá é 1 milhão de linhas por grupo de linhas. Para obter detalhes sobre como gerenciar recursos de classes e simultaneidade, confira o link abaixo.


### Principais conceitos de desempenho

Consulte os artigos a seguir que ajudam a entender alguns dos principais conceitos adicionais de desempenho e escala:

- [desempenho e escala][]
- [modelo de simultaneidade][]
- [criando tabelas][]
- [escolher uma chave de distribuição de hash para sua tabela][]

## Próximas etapas
Consulte o artigo [visão geral de desenvolvimento][] para obter algumas diretrizes sobre a criação da sua solução no SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[desempenho e escala]: sql-data-warehouse-performance-scale.md
[modelo de simultaneidade]: sql-data-warehouse-develop-concurrency.md
[criando tabelas]: sql-data-warehouse-develop-table-design.md
[escolher uma chave de distribuição de hash para sua tabela]: sql-data-warehouse-develop-hash-distribution-key
[visão geral de desenvolvimento]: sql-data-warehouse-overview-develop.md
[provisionar]: sql-data-warehouse-get-started-provision.md
[Configurar o acesso ao servidor de firewall para o IP do cliente]: sql-data-warehouse-get-started-provision.md/#step-4-configure-server-firewall-access-for-your-client-ip
[Visual Studio 2013/2015]: sql-data-warehouse-get-started-connect.md
[Conectar]: sql-data-warehouse-get-started-connect.md
[Estatísticas]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client (ODBC) 10/11]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0427_2016-->