<properties
   pageTitle="Limites de capacidade do SQL Data Warehouse | Microsoft Azure"
   description="Valores máximos para conexões, bancos de dados, tabelas e consultas para o SQL Data Warehouse."
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
   ms.date="05/13/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Limites de capacidade do SQL Data Warehouse

As tabelas abaixo contêm os valores máximos permitidos para vários componentes do SQL Data warehouse do Azure.


## Gerenciamento de carga de trabalho

| Categoria | Descrição | Máximo |
| :------------------ | :------------------------------------------- | :----------------- |
| DWU (Unidades de Data Warehouse)| Computação, memória e recursos de E/S | 2000 |
| Conexão de banco de dados | Sessões abertas simultâneas | 1024<br/><br/>Damos suporte a um máximo de 1024 conexões ativas, e cada uma pode enviar solicitações para um banco de dados do SQL Data Warehouse ao mesmo tempo. Observe que há limites no número de consultas que podem ser, de fato, executadas simultaneamente. Quando o limite de simultaneidade for excedido, a solicitação irá para uma fila interna onde aguardará seu processamento.|
| Conexão de banco de dados | Memória máxima para instruções preparadas | 20 MB |
| Gerenciamento de carga de trabalho | Máximo de consultas simultâneas | 32<br/><br/> Por padrão, o SQL Data Warehouse executará até 32 consultas simultâneas e consultas restantes na fila.<br/><br/>O nível de simultaneidade pode diminuir quando os usuários são atribuídos a uma classe de recurso maior. Algumas consultas, como as consultas DMV, sempre têm permissão para executar. Para saber mais, consulte [Gerenciamento de simultaneidade e de carga de trabalho][].|


## Objetos de banco de dados

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Banco de dados | Tamanho máx. | 60 TB compactados em disco<br/><br/>O SQL Data Warehouse permite até 60 TB de espaço em disco bruto por banco de dados. O espaço em disco é o tamanho compactado para tabelas permanentes. Esse espaço é independente do espaço de tempdb ou de log e, portanto, esse espaço é dedicado a tabelas permanentes. A compactação columnstore clusterizada é estimada em 5 vezes, o que significa que o tamanho descompactado do banco de dados pode crescer até aproximadamente 300 TB quando todas as tabelas são columnstore clusterizadas (o tipo de tabela padrão). O limite de 60 TB aumentará para 240 TB no final da visualização pública, que permitirá que a maioria dos bancos de dados aumente para mais de 1 PB de dados não compactados.|
| Tabela | Tamanho máx. | 60 TB compactados em disco |
| Tabela | Tabelas por banco de dados | 2 bilhões |
| Tabela | Colunas por tabela | 1024 colunas |
| Tabela | Bytes por coluna | 8000 bytes |
| Tabela | Bytes por linha, tamanho definido | 8\.060 bytes<br/><br/>O número de bytes por linha é calculado da mesma maneira que para o SQL Server com a opção de compactação de página ativada. Como o SQL Server, o SQL Data Warehouse dá suporte ao armazenamento de estouro de linha que permite que colunas de comprimento variável sejam empurradas para fora da linha. Somente uma raiz de 24 bytes é armazenada no registro principal para colunas de comprimento variável empurradas para fora da linha. Para saber mais, veja o tópico [Dados de estouro de linha excedendo 8 KB][] nos Manuais Online do SQL Server.<br/><br/>Para obter uma lista dos tamanhos de tipo de dados do SQL Data Warehouse, veja [CREATE TABLE (Azure SQL Data Warehouse)][]. |
| Tabela | Partições por tabela | 15\.000<br/><br/>Para alto desempenho, recomendamos minimizar o número de partições necessárias e, ao mesmo tempo, dar suporte aos seus requisitos de negócios. À medida que o número de partições aumenta, a sobrecarga de operações de DDL (Linguagem de Definição de Dados) e DML (Linguagem de Manipulação de Dados) também aumenta e faz com que o desempenho fique mais lento.|
| Tabela | Caracteres por valor de limite de partição.| 4000 |
| Índice | Índices não clusterizados por tabela. | 999<br/><br/>Aplica-se somente a tabelas rowstore.|
| Índice | Índices clusterizados por tabela. | 1<br><br/>Aplica-se a tabelas rowstore e columnstore.|
| Índice | Linhas em um grupo de linhas do índice columnstore | 1\.024<br/><br/>Cada índice columnstore é implementado como vários índices columnstore. Observe que se você inserir 1.024 linhas em um índice columnstore do SQL Data Warehouse, as linhas não irão para o mesmo grupo de linhas.|
| Índice | Compilações simultâneas de índices columnstore clusterizados. | 32<br/><br/>Aplica-se quando todos os índices columnstore clusterizados estão sendo criados em tabelas diferentes. Apenas um build de índice columnstore clusterizado é permitido por tabela. As solicitações adicionais aguardarão em uma fila.|
| Índice | Tamanho da chave de índice. | 900 bytes.<br/><br/>Aplica-se somente a índices rowstore.<br/><br/>Índices em colunas varchar com um tamanho máximo de mais de 900 bytes poderão ser criados se os dados existentes nas colunas não excederem 900 bytes quando o índice for criado. No entanto, as ações INSERT ou UPDATE posteriores nas colunas que excederem os 900 bytes de tamanho total falharão.|
| Índice | Colunas de chave por índice. | 16<br/><br/>Aplica-se somente a índices rowstore. Os índices columnstore clusterizados incluem todas as colunas.|
| Estatísticas | O tamanho dos valores de coluna combinados. | 900 bytes. |
| Estatísticas | As colunas por objeto de estatísticas. | 32 |
| Estatísticas | As estatísticas criadas em colunas por tabela. | 30\.000 |
| Procedimentos Armazenados | Os níveis máximos de aninhamento. | 8 |
| Visualizar | Colunas por exibição | 1\.024 |


## Cargas

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Cargas de Polybase | Bytes por linha | 32\.768<br/><br/>As cargas Polybase estão limitadas a carregar as duas linhas com menos de 32K e não podem ser carregadas para VARCHR(MAX), NVARCHAR(MAX) ou VARBINARY(MAX). Embora esse limite exista hoje, ele será removida muito em breve.<br/><br/>


## Consultas

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Consultar | Consultas em fila em tabelas de usuário. | 1000 |
| Consultar | Consultas simultâneas em exibições do sistema. | 100 |
| Consultar | Consultas em fila em exibições do sistema | 1000 |
| Consultar | Máximo de parâmetros | 2098 |
| Batch | Tamanho máximo | 65\.536*4096 |
| Resultados de SELECT | Colunas por linha | 4\.096<br/><br/>Nunca será possível ter mais de 4.096 colunas por linha no resultado de SELECT. Não há garantia de que você sempre terá 4096. Se o plano de consulta exigir uma tabela temporária, poderão ser aplicadas no máximo 1024 colunas por tabela.|
| SELECIONAR | Subconsultas aninhadas | 32<br/><br/>Você nunca poderá ter mais de 32 subconsultas aninhadas em uma instrução SELECT. Não há garantia de que você sempre terá 32. Por exemplo, JOIN pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível.|
| SELECIONAR | Colunas por JOIN | 1024 colunas<br/><br/>Você nunca poderá ter mais de 1024 colunas em JOIN. Não há garantia de que você sempre terá 1024. Se o plano JOIN exigir uma tabela temporária com mais colunas do que o resultado de JOIN, o limite de 1024 se aplicará à tabela temporária. |
| SELECIONAR | Bytes por colunas GROUP BY. | 8060<br/><br/>As colunas na cláusula GROUP BY podem ter no máximo 8060 bytes.|
| SELECIONAR | Bytes por colunas ORDER BY | 8060 bytes.<br/><br/>As colunas na cláusula ORDER BY podem ter no máximo 8060 bytes.|
| Identificadores e constantes por instrução | O número de identificadores referenciados e constantes. | 65\.535<br/><br/>O SQL Data Warehouse limita o número de identificadores e de constantes que podem ser contidos em uma única expressão de uma consulta. Esse limite é de 65.535. Exceder esse número resulta no erro 8632 do SQL Server. Para saber mais, veja [Erro interno: foi atingido um limite de serviços de expressão][].|


## Metadados

| Exibição do sistema | Máximo de linhas |
| :--------------------------------- | :------------|
| sys.dm\_pdw\_component\_health\_alerts | 10\.000 |
| sys.dm\_pdw\_dms\_cores | 100 |
| sys.dm\_pdw\_dms\_workers | O número total de trabalhos DMS para as 1000 solicitações de SQL mais recentes. |
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
[Gerenciamento de simultaneidade e de carga de trabalho]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->
[Dados de estouro de linha excedendo 8 KB]: https://msdn.microsoft.com/library/ms186981.aspx
[CREATE TABLE (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[Erro interno: foi atingido um limite de serviços de expressão]: https://support.microsoft.com/kb/913050

<!---HONumber=AcomDC_0518_2016-->