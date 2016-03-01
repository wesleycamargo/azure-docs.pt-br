<properties
   pageTitle="Características de serviço do SQL Data Warehouse | Microsoft Azure"
   description="Valores máximos para conexões, consultas, Transact-SQL DDL e DML e exibições do sistema para SQL Data Warehouse."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/11/2016"
   ms.author="barbkess;jrj;sonyama"/>

# Características de serviço do SQL Data Warehouse

Essas características incluem valores máximos que o SQL Data Warehouse do Azure estabeleceu para dar suporte às cargas de trabalho de análise mais exigentes enquanto também garante que cada consulta individual tenha os recursos necessários para otimizar o desempenho.

## Conexões

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Session | Sessões abertas simultâneas | 1024<br/><br/>Damos suporte a 1024 conexões simultâneas que podem enviar solicitações simultâneas para o data warehouse. Observe que há limites no número de consultas que podem ser executadas simultaneamente. Quando um limite for excedido, a solicitação irá para uma fila interna onde aguardará seu processamento.|
| Session | Memória máxima para instruções preparadas | 20 MB |
| Logons | Logons por SQL Server. | 500\.000 |


## Processamento de consulta

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Consultar | Consultas simultâneas em tabelas de usuário. | 32<br/><br/>Consultas adicionais irão para uma fila interna e aguardarão seu processamento. Independentemente do número de consultas em execução ao mesmo tempo, cada consulta é otimizada para fazer uso total da arquitetura do processamento extremamente paralelo.|
| Consultar | Consultas em fila em tabelas de usuário. | 1000 |
| Consultar | Consultas simultâneas em exibições do sistema. | 100 |
| Consultar | Consultas em fila em exibições do sistema | 1000 |
| Consultar | Máximo de parâmetros | 2098 |
| Batch | Tamanho máximo | 65\.536*4096 |


## DDL (Linguagem de Definição de Dados)

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabela | Tabelas por banco de dados | 2 bilhões |
| Tabela | Colunas por tabela | 1024 colunas |
| Tabela | Bytes por coluna | 8000 bytes |
| Tabela | Bytes por linha, tamanho definido | 8060 bytes<br/><br/>O número de bytes por linha é calculado da mesma maneira que o SQL Server com a opção Compactação de página ativada. Ela não pode ter mais de 8060 bytes. Para estimar o tamanho de linha, confira os cálculos de tamanho de linha em [Estimar o tamanho de um índice agrupado](https://msdn.microsoft.com/library/ms178085.aspx) no MSDN.<br/><br/>Para obter uma lista dos tamanhos de tipo de dados do SQL Data Warehouse, confira [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx). |
| Tabela | Partições por tabela | 15\.000<br/><br/>Para alto desempenho, recomendamos minimizar o número de partições necessárias e, ao mesmo tempo, dar suporte aos seus requisitos de negócios. À medida que o número de partições vai aumentando, a sobrecarga de operações de DDL e de DML também aumenta e faz com que o desempenho fique mais lento.|
| Tabela | Caracteres por valor de limite de partição.| 4000 |
| Índice | Índices não clusterizados por tabela. | 999<br/><br/>Aplica-se somente a tabelas rowstore.|
| Índice | Índices clusterizados por tabela. | 1<br><br/>Aplica-se a tabelas rowstore e columnstore.|
| Índice | Linhas em um grupo de linhas do índice columnstore | 1\.024<br/><br/>Cada índice columnstore é implantado como vários índices columnstore. Observe que se você inserir 1.024 linhas em um índice columnstore do SQL Data Warehouse, as linhas não irão para o mesmo grupo de linhas.|
| Índice | Compilações simultâneas de índices columnstore clusterizados. | 32<br/><br/>Aplica-se quando todos os índices columnstore clusterizados estão sendo criados em tabelas diferentes. Apenas uma compilação de índice columnstore clusterizado é permitida por tabela. As solicitações adicionais aguardarão em uma fila.|
| Índice | Tamanho da chave de índice. | 900 bytes.<br/><br/>Aplica-se somente a índices rowstore.<br/><br/>Índices em colunas varchar com um tamanho máximo de mais de 900 bytes poderão ser criados se os dados existentes nas colunas não excederem 900 bytes quando o índice for criado. No entanto, as ações INSERT ou UPDATE posteriores nas colunas que excederem os 900 bytes de tamanho total falharão.|
| Índice | Colunas de chave por índice. | 16<br/><br/>Aplica-se somente a índices rowstore. Os índices columnstore clusterizados incluem todas as colunas.|
| Estatísticas | O tamanho dos valores de coluna combinados. | 900 bytes. |
| Estatísticas | As colunas por objeto de estatísticas. | 32 |
| Estatísticas | As estatísticas criadas em colunas por tabela. | 30\.000 |
| Procedimentos Armazenados | Os níveis máximos de aninhamento. | 8 |
| Visualizar | Colunas por exibição | 1\.024 |


## DML (Linguagem de Manipulação de Dados)

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Resultados de SELECT | Colunas por linha | 4096<br/><br/>Você nunca poderá ter mais de 4096 colunas por linha no resultado de SELECT. Não há garantia de que você sempre terá 4096. Se o plano de consulta exigir uma tabela temporária, poderão ser aplicadas no máximo 1024 colunas por tabela.|
| Resultados de SELECT | Bytes por linha | >8060<br/><br/>O número de bytes por linha no resultado de SELECT pode ser maior do que o máximo de 8060 bytes permitido para as linhas da tabela. Se o plano de consulta da instrução SELECT exigir uma tabela temporária, poderá ser aplicado o máximo de tabela de 8060 bytes.|
| Resultados de SELECT | Bytes por coluna | >8000<br/><br/>O número de bytes por coluna no resultado SELECT poderá ser maior do que o máximo de 8000 bytes permitido para colunas de tabela. Se o plano de consulta para a instrução SELECT exigir uma tabela temporária, poderá ser aplicado o máximo de tabela de 8000 bytes.|
| SELECIONAR | Subconsultas aninhadas | 32<br/><br/>Você nunca poderá ter mais de 32 subconsultas aninhadas em uma instrução SELECT. Não há garantia de que você sempre terá 32. Por exemplo, JOIN pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível.|
| SELECIONAR | Colunas por JOIN | 1024 colunas<br/><br/>Você nunca poderá ter mais de 1024 colunas em JOIN. Não há garantia de que você sempre terá 1024. Se o plano JOIN exigir uma tabela temporária com mais colunas do que o resultado de JOIN, o limite de 1024 se aplicará à tabela temporária. |
| SELECIONAR | Bytes por colunas GROUP BY. | 8060<br/><br/>As colunas na cláusula GROUP BY podem ter no máximo 8060 bytes.|
| SELECIONAR | Bytes por colunas ORDER BY | 8060 bytes.<br/><br/>As colunas na cláusula ORDER BY podem ter no máximo 8060 bytes.|
| INSERT | Bytes por coluna, largura fixa e variável. | 8000 bytes. As tentativas de inserir mais bytes do que definido para a coluna resultarão em erro.|
| INSERT | Bytes por linha, colunas de largura variável. | >8060 bytes. Os dados além dos 8060 bytes são colocados na área de armazenamento de excedente.|
| UPDATE | Bytes por coluna, largura fixa e variável. | 8000 bytes. As tentativas de atualizar uma coluna com um valor que exija mais bytes do que definido para a coluna resultarão em erro.|
| Identificadores e constantes por instrução | O número de identificadores referenciados e constantes. | 65\.535<br/><br/>O SQL Data Warehouse limita o número de identificadores e de constantes que podem ser contidos em uma única expressão de uma consulta. Esse limite é de 65.535. Exceder esse número resulta no erro 8632 do SQL Server. Para saber mais, confira [Erro interno: limite de expressão dos serviços atingido](http://support.microsoft.com/kb/913050/).|

## Exibições do sistema

| Exibição do sistema | Máximo de linhas |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | O número total de trabalhos DMS para as 1000 solicitações de SQL mais recentes. |
| sys.dm\_pdw\_dms\_worker\_pairs | 10\.000 |
| sys.dm\_pdw\_errors | 10\.000 |
| sys.dm\_pdw\_exec\_requests | 10\.000 |
| sys.dm\_pdw\_exec\_sessions | 10\.000 |
| sys.dm\_pdw\_request\_steps | O número total de etapas para as 1000 solicitações de SQL mais recentes armazenadas em sys.dm\_pdw\_exec\_requests. |
| sys.dm\_pdw\_os\_event\_logs | 10\.000 |
| sys.dm\_pdw\_sql\_requests | As 1000 solicitações de SQL mais recentes armazenadas em sys.dm\_pdw\_exec\_requests. |

## Próximas etapas
Para obter mais informações de referência, consulte [Visão geral de referência do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Visão geral de referência do SQL Data Warehouse]: sql-data-warehouse-overview-reference.md

<!--MSDN references-->

<!---HONumber=AcomDC_0218_2016-->