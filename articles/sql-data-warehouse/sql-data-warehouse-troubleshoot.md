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
   ms.date="05/15/2016"
   ms.author="mausher;sonyama;barbkess"/>

# Solucionar problemas
O tópico a seguir lista alguns dos problemas mais comuns com os quais os clientes se deparam com o Azure SQL Data Warehouse.

## Conectividade
Os problemas de conectividade mais comuns incluem:

- Regras de firewall não definidas
- Uso de ferramentas/protocolos sem suporte

### Regras de firewall
Bancos de Dados SQL do Azure são protegidos pelo servidor e firewalls de nível de banco de dados para garantir que somente os endereços IP conhecidos possam acessar um bancos de dados. Os firewalls são seguros por padrão, o que significa que você deve habilitar explicitamente um endereço IP ou um intervalo de endereços antes de se conectar. Para configurar seu firewall para acesso, siga as etapas na seção [Configurar o acesso ao servidor de firewall para o IP do cliente][] da página [instruções de provisionamento][].

### Uso de ferramentas/protocolos sem suporte
O SQL Data Warehouse recomenda o uso do [Visual Studio 2013 ou 2015][] para consultar os dados. Para conectividade de cliente, [SQL Server Native Client (ODBC) 10/11][] são recomendados. O SSMS (SQL Server Management Studio) ainda não tem suporte e, embora funcione parcialmente, a árvore do Explorador de Objetos não funciona com o SQL Data Warehouse e a consulta poderá funcionar depois de você ignorar algumas mensagens de erro.

## Desempenho de consulta

Há várias coisas simples que você pode fazer em seu design de banco de dados para garantir que obtenha o desempenho ideal de consulta do SQL Data Warehouse. Um bom lugar para começar a entender o desempenho das suas consultas é o artigo sobre [aprender a monitorar suas consultas][]. Às vezes, a solução para fazer uma consulta ser executada mais rapidamente é simplesmente adicionar mais poder de computação às suas consultas ao [dimensionar seu SQL Datawarehouse][]. Para encontrar muitas dessas otimizações em um único lugar, dê uma olhada no artigo [Práticas recomendadas do SQL Data Warehouse][].

A seguir, algumas das causas mais comuns de consulta dos problemas de desempenho que vimos.

### Estatísticas

As [estatísticas][] em suas tabelas contêm informações sobre o intervalo e a frequência de valores em uma coluna de banco de dados. O mecanismo de consulta usa essas estatísticas para otimizar a execução de consulta e melhorar o desempenho de consulta. Ao contrário do SQL Server ou do SQL DB, o SQL Data Warehouse não cria ou atualiza as estatísticas automaticamente. As estatísticas devem ser manualmente mantidas em todas as tabelas. Para saber como gerenciar as estatísticas e identificar as tabelas que precisam de estatísticas, dê uma olhada no artigo [Gerenciar estatísticas no SQL Data Warehouse][].

### Design da tabela

Uma das opções mais importantes feitas no SQL Data Warehouse é [escolher a chave de distribuição de hash correta para sua tabela][] e o [design da tabela][]. Ao mudar da introdução para a obtenção de um desempenho mais rápido do seu SQL Data Warehouse, compreenda os conceitos destes artigos.

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
    AVG(CASE WHEN rg.State = 3 THEN rg.Total_rows ELSE NULL END) < 100000 OR
    AVG(CASE WHEN rg.State = 0 THEN rg.Total_rows ELSE NULL END) < 100000

ORDER BY 
    s.name, t.name
```
 
**ETAPA 2:** aumentar a classe de recurso de um usuário que tem permissões para recriar o índice nessa tabela na classe de recurso recomendada da coluna 2 da consulta acima.

```sql
EXEC sp_addrolemember 'xlargerc', 'LoadUser'
```

> [AZURE.NOTE]  O LoadUser acima deve ser um usuário válido criado para executar a instrução ALTER INDEX. A classe de recurso do usuário db\_owner não pode ser alterada. Mais informações sobre classes de recursos e como criar um novo usuário podem ser encontradas no link abaixo.

 
**ETAPA 3:** fazer logon como o usuário da etapa 2 (por exemplo "LoadUser"), que agora está usando uma classe de recurso mais alta, e executar as instruções ALTER INDEX geradas pela consulta na ETAPA 1. Verifique se esse usuário tem a permissão ALTER para as tabelas identificadas na consulta da ETAPA 1.
 
**ETAPA 4:** executar novamente a consulta da etapa 1. Se os índices foram criados com eficiência, nenhuma linha deve retornar nessa consulta. Se nenhuma linha for retornada, você estará liberado. Se você tem vários bancos de dados do SQL DW, repita esse processo em cada um dos seus bancos de dados. Se houver retorno de linhas, continue na etapa 5.
 
**ETAPA 5:** se linhas são retornadas quando você executa novamente a consulta da etapa 1, talvez você tenha tabelas com linhas muito largas que precisam de grande quantidade de memória para construir os índices columnstore clusterizados de forma ideal. Se esse for o caso, repita o processo para a tabela usando a classe xlargerc. Para alterar a classe do recurso, repita a etapa 2 usando xlargerc. Em seguida, repita a etapa 3 para as tabelas que ainda estão com índices de qualidade inferior. Se você estiver usando um DW100 - DW300 e já usou o xlargerc, poderá optar por deixar os índices como estão ou aumentar temporariamente a DWU a fim de fornecer mais memória para esta operação.
 
**ETAPAS FINAIS:** a classe de recurso designada acima é a classe de recurso mínima recomendada para criar os índices columnstore de maior qualidade. Recomendamos que você mantenha essa configuração para o usuário que carrega os dados. No entanto, se você quiser desfazer a alteração da etapa 2, poderá fazer isso com o comando a seguir.

```sql
EXEC sp_droprolemember 'smallrc', 'LoadUser'
```

A orientação para a classe de recurso mínima para carregamentos de uma tabela CCI é usar xlargerc para DW100 DW300, largerc para DW400 DW600 e mediumrc para qualquer coisa em DW1000 ou acima. Este guia é uma prática recomendada para a maioria das cargas de trabalho. O objetivo é fornecer a cada operação de criação de índice 400 MB ou mais de memória. No entanto, uma solução não serve para todas. A memória necessária para otimizar um índice columnstore depende dos dados carregados, que são basicamente influenciados pelo tamanho da linha. Tabelas com linhas mais estreitas precisam de menos memória, e mais largas precisam de mais memória. Se você quiser experimentar, poderá usar a consulta da etapa 1 para ver se consegue obter os índices columnstore ideais em alocações de memória menores. No mínimo, o ideal é ter em média mais de 100 mil linhas por grupo de linhas. Acima de 500 mil é ainda melhor. O máximo que você verá é 1 milhão de linhas por grupo de linhas. Para obter detalhes sobre como gerenciar recursos de classes e simultaneidade, confira o link abaixo.


## Próximas etapas
Veja o artigo [Práticas recomendadas do SQL Data Warehouse][] para saber mais sobre como otimizar sua solução do SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[dimensionar seu SQL Datawarehouse]: ./sql-data-warehouse-overview-scalability.md
[design da tabela]: ./sql-data-warehouse-develop-table-design.md
[escolher a chave de distribuição de hash correta para sua tabela]: ./sql-data-warehouse-develop-hash-distribution-key
[development overview]: ./sql-data-warehouse-overview-develop.md
[aprender a monitorar suas consultas]: ./sql-data-warehouse-manage-monitor.md
[Gerenciar estatísticas no SQL Data Warehouse]: ./sql-data-warehouse-develop-statistics.md
[instruções de provisionamento]: ./sql-data-warehouse-get-started-provision.md
[Configurar o acesso ao servidor de firewall para o IP do cliente]: ./sql-data-warehouse-get-started-provision.md/#create-a-new-azure-sql-server-level-firewall
[Visual Studio 2013 ou 2015]: ./sql-data-warehouse-get-started-connect.md
[Práticas recomendadas do SQL Data Warehouse]: ./sql-data-warehouse-best-practices.md
[estatísticas]: ./sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[SQL Server Native Client (ODBC) 10/11]: https://msdn.microsoft.com/library/ms131415.aspx

<!--Other web references-->

<!---HONumber=AcomDC_0518_2016-->