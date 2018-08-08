---
title: Notas de versão do SQL Data Warehouse do Azure maio de 2018 | Microsoft Docs
description: Notas de versão do SQL Data Warehouse do Azure.
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 01b571beba012ae0a1fa27d03f5e0e5454f62aa5
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 07/27/2018
ms.locfileid: "39324948"
---
# <a name="whats-new-in-azure-sql-data-warehouse-may-2018"></a>O que há de novo no SQL Data Warehouse do Azure? Maio de 2018 
O SQL Data Warehouse do Azure recebe melhorias continuamente. Este artigo descreve os novos recursos e alterações que foram introduzidas em maio de 2018. 

## <a name="gen-2-instances"></a>Instâncias do Gen 2
![alt](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/2528b41b-f09f-45b1-aa65-fc60d562d3bd.png) A camada Gen 2 de computação otimizada do SQL Data Warehouse do Azure define novos padrões de desempenho para o armazenamento de dados de nuvem. Os clientes agora obtém até cinco vezes melhor desempenho de consulta, quatro vezes mais simultaneidade e cinco vezes maior poder de computação em comparação com a geração atual. Agora pode servir 128 consultas simultâneas de um único cluster, o mais alto de qualquer serviço de armazenamento de dados de nuvem.

Consulte o comunicado de blog [Acelere análise de nuvem com o SQL Data Warehouse do Azure](https://azure.microsoft.com/blog/turbocharge-cloud-analytics-with-azure-sql-data-warehouse/) Rohan Kumar, vice-presidente corporativo, Dados do Azure.

## <a name="auto-statistics"></a>Estatísticas automáticas
As estatísticas são essenciais para otimizar a geração do plano de consulta no otimizador baseado no custo de moderl como mecanismo no SQL Data Warehouse. Quando todas as consultas são conhecidas com antecedência, determinar quais objetos de estatísticas precisam ser criados é uma tarefa viável. No entanto, quando o sistema está enfrentando consultas aleatórias e ad hoc, que é comum para as cargas de trabalho de armazenamento de dados, os administradores do sistema podem ter dificuldade para prever quais estatísticas precisam ser criadas levando a planos de execução de consulta de qualidade potencialmente inferior e aumentar os tempos de resposta das consultas. Uma forma de atenuar esse problema é criar objetos de estatísticas em todas as colunas de tabela com antecedência. No entanto, esse processo vem com uma penalidade como objetos de estatísticas que precisam ser mantidas durante o processo de carregamento da tabela, causando tempos de carregamento mais longos.

O SQL Data Warehouse agora dá suporte a criação automática de objetos de estatísticas, fornecendo maior flexibilidade, produtividade e facilidade de uso para os administradores de sistema e desenvolvedores, garantindo que o sistema continue a oferecer planos de execução de qualidade e melhores tempos de resposta.

Para ativar ou desativar a criação automática de estatísticas no SQL Data Warehouse, execute a seguinte instrução:
```sql
ALTER DATABASE { database_name } SET { AUTO_CREATE_STATISTICS { OFF | ON } } [;]
```

Como melhor prática e diretrizes, é recomendável definir a opção `AUTO_CREATE_STATISTICS` para `ON`.

> [!NOTE]
> A criação automática de estatísticas é *habilitada por padrão* para todos os novos data warehouses.
>  

Consulte o artigo [opções ALTER DATABASE SET](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql-set-options) para obter detalhes adicionais.

## <a name="rejected-row-support"></a>Suporte de linha de rejeitadas
Os clientes usam frequentemente [PolyBase (tabelas externas) para carregar dados](design-elt-data-loading.md) no SQL Data Warehouse, devido a natureza paralela de alto desempenho de carregamento de dados. PolyBase é o modelo de carregamento padrão ao carregar dados por meio de [do Azure Data Factory](http://azure.com/adf) também. 

O SQL Data Warehouse adiciona a capacidade de definir um local de linha rejeitada por meio do parâmetro `REJECTED_ROW_LOCATION` com a instrução [CRIAR TABELA EXTERNA](https://docs.microsoft.com/sql/t-sql/statements/create-external-table-transact-sql). Após a execução de um [CRIAR TABELA CONFORME SELECIONADO (CTAS)](https://docs.microsoft.com/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse) da tabela externa, todas as linhas que não puderam ser carregadas serão armazenadas em um arquivo próximo à fonte para investigação futura. 

Consulte o blog [Carregar com confiança com Local de Linhas Rejeitadas PolyBase do SQL Data Warehouse](https://azure.microsoft.com/blog/load-confidently-with-sql-data-warehouse-polybase-rejected-row-location/) para obter mais detalhes sobre o comportamento de linha rejeitada.

O exemplo a seguir mostra a nova sintaxe para especificar as Linhas Rejeitadas.

```sql
CREATE EXTERNAL TABLE [dbo].[Reject_Example]
(
    ...
)
WITH
(
    ...
    ,REJECTED_ROW_LOCATION=‘/Reject_Directory'
)
```

## <a name="alter-view"></a>ALTER VIEW
[ALTER VIEW](https://docs.microsoft.com/sql/t-sql/statements/alter-view-transact-sql) permite que um usuário modifique uma exibição criada anteriormente sem a necessidade de CRIAR/EXCLUIR o modo de exibição e reaplicar permissões. 

O exemplo a seguir modifica uma exibição criada anteriormente.
```sql
ALTER VIEW test_view AS SELECT 1 [data];
```

## <a name="concatws"></a>CONCAT_WS
A função [CONCAT_WS()](https://docs.microsoft.com/sql/t-sql/functions/concat-ws-transact-sql) retorna uma cadeia de caracteres resultante da concatenação de dois ou mais valores de uma maneira integrada. Ela separa os valores concatenados com o delimitador especificado no primeiro argumento. A função `CONCAT_WS` é útil para gerar a saída do CSV (Comma-Separated Value).

O exemplo a seguir mostra a concatenação de um conjunto de valores int com uma vírgula.
```sql
SELECT CONCAT_WS(',', 1, 2, 3) [result];
```
A instrução retorna o seguinte resultado:
```
result
---------
1,2,3
```
O exemplo a seguir mostra a concatenação de um conjunto de valores de tipo de dados mistos com uma vírgula.
```sql
SELECT CONCAT_WS(',', 1, 2, 'String', NEWID()) [result]
```
A instrução retorna o seguinte resultado:
```
result
---------
1,2,String,26E1F74D-5746-44DC-B47F-2FC1DA1B6E49
```

## <a name="spdatatypeinfo"></a>SP_DATATYPE_INFO
O procedimento armazenado do sistema [sp_datatype_info](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-datatype-info-transact-sql) retorna informações sobre os tipos de dados suportados pelo ambiente atual. Ele costuma ser usado pelas ferramentas que se conectam por meio de conexões ODBC para investigação de tipo de dados.

O exemplo a seguir recupera os detalhes de todos os tipos de dados suportados pelo SQL Data Warehouse.

```sql
EXEC sp_datatype_info
```

## <a name="select-into-with-order-by-behavior-change"></a>Alteração de comportamento de SELECT INTO com ORDER BY
O SQL Data Warehouse agora bloqueará consultas `SELECT INTO` que contenham uma cláusula `ORDER BY`. Anteriormente, essa operação teria sucesso ordenando primeiro os dados na memória e, em seguida, inserindo na tabela de destino reorganizando os dados para corresponder à forma de tabela.

### <a name="previous-behavior"></a>Comportamento anterior
A instrução a seguir teria sucesso com a sobrecarga de processamento adicional.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```

### <a name="current-behavior"></a>Comportamento atual
A instrução a seguir gerará um erro indicando que a cláusula `ORDER BY` não tem suporte em uma instrução `SELECT INTO`.
```sql
SELECT * INTO table2 FROM table1 ORDER BY 1;
```
A instrução de erro retornada:
```
Msg 104381, Level 16, State 1, Line 1
The ORDER BY clause is invalid in views, CREATE TABLE AS SELECT, INSERT SELECT, SELECT INTO, inline functions, derived tables, subqueries, and common table expressions, unless TOP or FOR XML is also specified.
```

## <a name="set-parseonly-on-query-status-behavior-change"></a>Status de consulta SET PARSEONLY ON (Alteração de comportamento)
Usar a sintaxe `SET PARSEONLY ON` permite que um usuário com o mecanismo do SQL Data Warehouse examine a sintaxe de cada instrução T-SQL e retorne mensagens de erro sem compilar ou executar a instrução. Anteriormente, na exibição do sistema [sys.dm_pdw_exec_requests](https://docs.microsoft.com/sql/relational-databases/system-dynamic-management-views/sys-dm-pdw-exec-requests-transact-sql), o status para essas instruções permaneceria no estado `Running`. A exibição `sys.dm_pdw_exec_requests` agora irá retornar o status como `Complete`.

## <a name="next-steps"></a>Próximas etapas
Agora que você conhece um pouco do SQL Data Warehouse, aprenda como [criar um SQL Data Warehouse][create a SQL Data Warehouse] rapidamente. Se você ainda não conhece o Azure, poderá achar o [Glossário do Azure][Azure glossary] útil à medida que encontrar nova terminologia. Ou, dê uma olhada em alguns desses outros Recursos do SQL Data Warehouse.  

* [Histórias de sucesso de clientes]
* [Blogs]
* [Solicitações de recursos]
* [Vídeos]
* [Blogs da Equipe Consultoria para Clientes]
* [Fórum Stack Overflow]
* [Twitter]


[Blogs]: https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/
[Blogs da Equipe Consultoria para Clientes]: https://blogs.msdn.microsoft.com/sqlcat/tag/sql-dw/
[Histórias de sucesso de clientes]: https://azure.microsoft.com/case-studies/?service=sql-data-warehouse
[Solicitações de recursos]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Twitter]: https://twitter.com/hashtag/SQLDW
[Vídeos]: https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse
[create a SQL Data Warehouse]: ./create-data-warehouse-portal.md
[Azure glossary]: ../azure-glossary-cloud-terminology.md