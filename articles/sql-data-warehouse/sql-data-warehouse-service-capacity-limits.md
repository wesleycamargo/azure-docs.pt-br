<properties
   pageTitle="Limites de capacidade do SQL Data Warehouse | Microsoft Azure"
   description="Valores máximos de bancos de dados, tabelas, conexões e consultas para o SQL Data Warehouse."
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
   ms.date="04/12/2016"
   ms.author="sonyama;barbkess;jrj"/>

# Limites de capacidade do SQL Data Warehouse

Valores máximos para dar suporte às cargas de trabalho de análise mais exigentes, ao mesmo tempo que garante que cada consulta individual tem os recursos necessários para um desempenho ideal.

## O Barramento de

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Banco de dados | Tamanho máximo | 60 TB compactados<br/><br/>O SQL Data Warehouse permite até 60 TB de espaço em disco bruto por banco de dados. O espaço em disco é o tamanho compactado para tabelas permanentes. Esse espaço é independente do espaço de tempdb ou de log e, portanto, esse espaço é dedicado a tabelas permanentes. A compactação columnstore clusterizada é estimada em 5 vezes, o que significa que o tamanho descompactado do banco de dados pode crescer até aproximadamente 300 TB quando todas as tabelas são columnstore clusterizadas (o tipo de tabela padrão). O limite de 60 TB aumentará para 240 TB no final da preview pública, que deve permitir que a maioria dos bancos de dados aumente para mais de 1 PB de dados não compactados.|
| Banco de dados | Sessões abertas simultâneas | 1\.024<br/><br/>Damos suporte a um máximo de 1.024 conexões ativas que podem enviar solicitações para cada banco de dados do SQL Data Warehouse ao mesmo tempo. Observe que há limites no número de consultas que podem ser, de fato, executadas simultaneamente. Quando um limite for excedido, a solicitação irá para uma fila interna onde aguardará seu processamento.|
| Conexão de banco de dados | Memória máxima para instruções preparadas | 20 MB |
| Gerenciamento de carga de trabalho | Máximo de consultas simultâneas | 32<br/><br/> O SQL Data Warehouse tem 32 unidades de simultaneidade chamadas slots de simultaneidade.<br/><br/>Se todas as consultas forem executadas com a alocação de recurso padrão de um slot de simultaneidade, será possível ter 32 consultas de usuário simultâneas. Na prática, o máximo de consultas simultâneas depende do SLO (objetivo de nível de serviço) e os requisitos de recursos para cada consulta. Quando não houver recursos disponíveis, consultas esperam em uma fila interna. Para obter mais informações, consulte [Gerenciamento de simultaneidade e carga de trabalho][].|
| Gerenciamento de carga de trabalho | Máximo de slots de simultaneidade por SLO (objetivo de nível de serviço) |Este é o número de slots de simultaneidade que cada nível de serviço pode usar para executar consultas que exigem recursos adicionais de CPU e memória. Para o gerenciamento de carga de trabalho, você pode usar classes de recursos internas para aumentar os recursos de CPU e memória para uma consulta. Executar com mais recursos significa que a consulta requer mais slots de simultaneidade.<br/><br/>Algumas consultas não são executadas em classes de recursos. Essas consultas usam uma unidade de simultaneidade e não consomem nenhum dos slots de simultaneidade listados abaixo. Para obter uma lista de consultas que o SQL Data Warehouse executa (e não executa) dentro de classes de recursos, consulte [Gerenciamento de simultaneidade e carga de trabalho][].<br/><br/>DWU100 = 4<br/><br/>DWU200 = 8<br/><br/>DWU300 = 12<br/><br/>DWU400 = 16<br/><br/>DWU500 = 20<br/><br/>DWU600 = 24<br/><br/>DWU1000 = 40<br/><br/>DWU1200 = 48<br/><br/>DWU1500 = 60 |


## Objetos de banco de dados

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Tabela | Tamanho máx. | 60 TB compactados em disco |
| Tabela | Tabelas por banco de dados | 2 bilhões |
| Tabela | Colunas por tabela | 1024 colunas |
| Tabela | Bytes por coluna | 8000 bytes |
| Tabela | Bytes por linha, tamanho definido | 8\.060 bytes<br/><br/>O número de bytes por linha é calculado da mesma maneira que para o SQL Server com a opção de compactação de página ativada. Como o SQL Server, o SQL Data Warehouse dá suporte ao armazenamento de estouro de linha que permite que colunas de comprimento variável sejam empurradas para fora da linha. Somente uma raiz de 24 bytes é armazenada no registro principal para colunas de comprimento variável empurradas para fora da linha. Para obter mais informações, veja o tópico [Dados de estouro de linha excedendo 8 KB](https://msdn.microsoft.com/library/ms186981.aspx) nos Manuais Online do SQL Server.<br/><br/>Para obter uma lista dos tamanhos de tipo de dados do SQL Data Warehouse, veja [CREATE TABLE (Azure SQL Data Warehouse)](https://msdn.microsoft.com/library/mt203953.aspx) (CRIAR TABELA (SQL Data Warehouse do Azure)). |
| Tabela | Bytes por linha, tamanho do buffer interno para a movimentação de dados | 32\.768<br/><br/>OBSERVAÇÃO: esse limite existe atualmente, mas será removido em breve.<br/><br/>O SQL Data Warehouse usa um buffer interno para mover as linhas no sistema distribuído do SQL Data Warehouse. O serviço que move linhas é chamado de DMS (Serviço de Movimentação de Dados) e armazena linhas em um formato diferente do SQL Server.<br/><br/>Se uma linha não couber no buffer interno, você obterá um erro de compilação de consulta ou um erro de movimentação de dados interno. Para evitar esse problema, veja [Detalhes sobre o tamanho de buffer do DMS](#details-about-the-dms-buffer-size).|
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


## Consultas

| Categoria | Descrição | Máximo |
| :---------------- | :------------------------------------------- | :----------------- |
| Consultar | Consultas em fila em tabelas de usuário. | 1000 |
| Consultar | Consultas simultâneas em exibições do sistema. | 100 |
| Consultar | Consultas em fila em exibições do sistema | 1000 |
| Consultar | Máximo de parâmetros | 2098 |
| Batch | Tamanho máximo | 65\.536*4096 |
| Resultados de SELECT | Colunas por linha | 4\.096<br/><br/>Nunca será possível ter mais de 4.096 colunas por linha no resultado de SELECT. Não há garantia de que você sempre terá 4096. Se o plano de consulta exigir uma tabela temporária, poderão ser aplicadas no máximo 1024 colunas por tabela.|
| SELECIONAR | Subconsultas aninhadas | 32<br/><br/>Nunca será possível ter mais de 32 subconsultas aninhadas em uma instrução SELECT. Não há garantia de que você sempre terá 32. Por exemplo, JOIN pode introduzir uma subconsulta no plano de consulta. O número de subconsultas também pode ser limitado pela memória disponível.|
| SELECIONAR | Colunas por JOIN | 1\.024 colunas<br/><br/>Nunca será possível ter mais de 1.024 colunas em JOIN. Não há garantia de que você sempre terá 1024. Se o plano JOIN exigir uma tabela temporária com mais colunas do que o resultado de JOIN, o limite de 1024 se aplicará à tabela temporária. |
| SELECIONAR | Bytes por colunas GROUP BY. | 8\.060<br/><br/>As colunas na cláusula GROUP BY podem ter, no máximo, 8.060 bytes.|
| SELECIONAR | Bytes por colunas ORDER BY | 8\.060 bytes.<br/><br/>As colunas na cláusula ORDER BY podem ter, no máximo, 8.060 bytes.|
| Identificadores e constantes por instrução | O número de identificadores referenciados e constantes. | 65\.535<br/><br/>O SQL Data Warehouse limita o número de identificadores e de constantes que podem ser contidos em uma única expressão de uma consulta. Esse limite é de 65.535. Exceder esse número resulta no erro 8632 do SQL Server. Para obter mais informações, veja [Erro interno: foi atingido um limite de serviços de expressão](http://support.microsoft.com/kb/913050/).|



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


## Detalhes sobre o tamanho de buffer do DMS

O SQL Data Warehouse usa um buffer interno para mover linhas entre os nós de computação de back-end. O serviço que move linhas é chamado de DMS (Serviço de Movimentação de Dados) e usa um formato de armazenamento diferente do SQL Server.

Para melhorar o desempenho de consulta paralela, o DMS preenche todos os dados de comprimento variável com o tamanho máximo definido pelo banco de dados SQL. Por exemplo, o valor “hello” para um `nvarchar(2000) NOT NULL`, na verdade, usará 4.002 bytes no buffer do DMS. Ele usa dois bytes para cada um dos 2000 caracteres, mais dois bytes para o terminador NULL.

> [AZURE.NOTE] Um erro interno ocorrerá quando o DMS tentar mover uma linha que exceda o tamanho de buffer do DMS de 32.768 bytes. Se o tamanho da linha exceder o tamanho de buffer do DMS, você precisará revisar a definição da tabela para que a linha caiba no buffer do DMS.

### Como determinar o tamanho de linha para o buffer do DMS
O exemplo descreve os tamanhos definidos pelo DMS para dados de comprimento variável e mostra como calcular o tamanho do buffer do DMS para uma linha.
 
No buffer do DMS, o tamanho de dados de comprimento variável é a soma dos seguintes itens:

- O tamanho definido para caracteres.
- Os tipos NULL usam oito bytes para o indicador NULL.
- Os tipos ASCII usam um caractere para o terminador NULL.
- Os tipos Unicode usam dois caracteres para o terminador NULL.
             
| Tipo de Dados | Tamanho de buffer do DMS |
| :---------------------- | :-------------------------- |                
| char(1000) NULL | 1009 bytes = 1000 + 8 + 1 |
| char(1000) NOT NULL | 1001 bytes = 1000 + 1 |
| nchar (1000 NULL | 2010 bytes = 2000 + 8 + 2 |
| varchar(1000) NULL | 1009 bytes = 1000 + 8 + 1 |
| varchar(1000) NOT NULL | 1009 bytes = 1000 + 1 |
| nvarchar (1000) NULL | 2010 bytes = 2000 + 8 + 2 |
| nvarchar(1000) NOT NULL | 2010 bytes = 2000 + 2 |
                
No buffer do DMS, as colunas de largura fixa usam o tamanho nativo do SQL Server. Se o tipo for anulável, o DMS exigirá mais 8 bytes. Para o tamanho do SQL Server, confira o campo max\_length em sys.types.

Por exemplo:

| Tipo de dados de largura fixa | Tamanho de buffer do DMS |
| :-------------------- | :----------------- |
| int NULL | 12 bytes = 4 + 8 |
| int NOT NULL | 4 bytes = 4 + 0 | 
                
Ao juntar tudo, o tamanho definido pelo buffer do DMS para a linha a seguir é de 31.134 bytes, que caberão no buffer do DMS.

| Coluna | Tipo de Dados | Tamanho da Coluna |
| :----- | :------------------ | :------------------------ |
| col1 | datetime2 (7) NULL | 20 bytes = 8 + 8 |
| col2 | float (4) NULL | 12 bytes = 4 + 8 |
| col3 | nchar (6) NULL | 22 bytes = 12 + 8 + 2 |
| col4 | char (7000) NULL | 7009 bytes = 7000 + 8 + 1 |
| col5 | nvarchar (4000) | 8002 bytes = 8000 + 2. |
| col6 | varchar (8000) NULL | 8009 bytes = 8000 + 8 + 1 |
| col7 | varbinary (8000) | 8000 bytes |
| col8 | binary (60) | 60 bytes |
                
              
### Exemplo de tamanho de buffer do DMS excedido

Este exemplo mostra como você poderia inserir uma linha no SQL Data Warehouse com êxito, mas gera um erro de excedente de DMS quando precisa mover a linha para uma junção incompatível de distribuição. A lição aqui é aprender a criar linhas menores que caberão no buffer do DMS.

No exemplo a seguir, criamos a tabela T1. O tamanho máximo possível da linha quando todas as variáveis nvarchar tiverem 4000 caracteres Unicode é maior que 40.000 bytes, o que excede o tamanho máximo de buffer do DMS.

Como o tamanho real definido de uma nvarchar usa 26 bytes, a definição de linha é menor que 8060 bytes e pode caber em uma página do SQL Server. Portanto, a instrução CREATE TABLE é bem-sucedida, mesmo que o DMS venha a falhar ao tentar carregar essa linha no seu buffer.

```sql
CREATE TABLE T1
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (c0) )
;
```

A próxima etapa mostra que podemos usar INSERT para inserir dados na tabela com êxito. Essa instrução carrega os dados diretamente para o SQL Server sem usar DMS e, portanto, não causa uma falha de excedente de buffer DMS. Os Serviços de Integração também carregarão essa linha com êxito.</para>

```sql
--The INSERT operation succeeds because the row is inserted directly into SQL Server without requiring DMS to buffer the row.
INSERT INTO T1
VALUES (
    25,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```

Para preparar a demonstração de movimentação de dados, este exemplo cria uma segunda tabela com CustomerKey para a coluna de distribuição.

```sql
--This second table is distributed on CustomerKey. 
CREATE TABLE T2
  (
    c0 int NOT NULL,
    CustomerKey int NOT NULL,
    c1 nvarchar(4000),
    c2 nvarchar(4000),
    c3 nvarchar(4000),
    c4 nvarchar(4000),
    c5 nvarchar(4000)
  )
WITH ( DISTRIBUTION = HASH (CustomerKey) )
;

INSERT INTO T2
VALUES (
    32,
    429817,
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.',
    N'Each row must fit into the DMS buffer size of 32,768 bytes.'
  )
```
Como nenhuma das tabelas é distribuída em CustomerKey, uma união entre T1 e T2 em CustomerKey é incompatível com a distribuição. O DMS precisará carregar pelo menos uma linha e copiá-la para uma distribuição diferente.

```sql
SELECT * FROM T1 JOIN T2 ON T1.CustomerKey = T2.CustomerKey;
```

Como se esperava, o DMS falha ao executar a junção porque a linha, quando todas as colunas nvarchar forem preenchidas, será maior do que o tamanho do buffer do DMS de 32.768 bytes. A mensagem de erro a seguir é exibida.

```sql
Msg 110802, Level 16, State 1, Line 126

An internal DMS error occurred that caused this operation to fail. Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Workers.DmsSqlNativeException, Message: SqlNativeBufferReader.ReadBuffer, error in OdbcReadBuffer: SqlState: , NativeError: 0, 'COdbcReadConnection::ReadBuffer: not enough buffer space for one row | Error calling: pReadConn-&gt;ReadBuffer(pBuffer, bufferOffset, bufferLength, pBytesRead, pRowsRead) | state: FFFF, number: 81, active connections: 8', Connection String: Driver={SQL Server Native Client 11.0};APP=DmsNativeReader:P13521-CMP02\sqldwdms (4556) - ODBC;Trusted_Connection=yes;AutoTranslate=no;Server=P13521-SQLCMP02,1500
```


## Próximas etapas
Para obter mais informações de referência, consulte [Visão geral de referência do SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->
[Visão geral de referência do SQL Data Warehouse]: sql-data-warehouse-overview-reference.md
[Gerenciamento de simultaneidade e carga de trabalho]: sql-data-warehouse-develop-concurrency.md

<!--MSDN references-->

<!---HONumber=AcomDC_0413_2016-->