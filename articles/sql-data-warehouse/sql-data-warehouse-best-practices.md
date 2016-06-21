<properties
   pageTitle="Práticas Recomendadas para o SQL Data Warehouse | Microsoft Azure"
   description="Recomendações e práticas recomendadas que você deve saber quando for desenvolver soluções para o SQL Data Warehouse do Azure. Isto irá ajudá-lo a alcançar o sucesso."
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
   ms.date="06/02/2016"
   ms.author="sonyama;barbkess"/>

# Práticas recomendadas para o Azure SQL Data Warehouse

Este artigo é um conjunto de práticas recomendadas que permitirá a você obter o melhor preço/desempenho do SQL Data Warehouse do Azure. Alguns conceitos neste artigo são muito básicos e fáceis de explicar, outros são mais avançados e podemos apenas abordar superficialmente neste artigo. A finalidade deste artigo é dar algumas orientações básicas e aumentar o reconhecimento dos pontos importantes a considerar ao compilar seu data warehouse. Cada seção apresentará um conceito e apontará para os artigos mais detalhados que abordam o conceito com maior profundidade.

Se você está apenas começando a usar o SQL Data Warehouse, não deixe que este artigo assuste você. A sequência de tópicos está, em sua maioria, na ordem de importância. Se você se concentrar apenas nos três primeiros inicialmente, ficará bem. Quando se familiarizar e se sentir confortável em utilizar o SQL Data Warehouse, volte e veja mais alguns tópicos. Não irá demorar muito para que tudo faça sentido. Pode parecer um exagero inicialmente, mas com o tempo e a experiência, esses tópicos se tornarão básicos para você.

## Reduzir custos com pausa e dimensionamento
Um recurso importante do SQL Data Warehouse é a capacidade de pausar quando você não está usando-o, o que interrompe a cobrança dos recursos de computação. Outro recurso importante é a capacidade de dimensionar os recursos. A pausa e o dimensionamento podem ser feitos por meio do Portal do Azure ou com os comandos do PowerShell. Familiarize-se com esses recursos, pois eles podem reduzir bastante o custo do data warehouse quando ele não está em uso. Se quiser seu data warehouse esteja sempre acessível, convém considerar dimensioná-lo para ter o menor tamanho, um DW100 em vez de pausar.

Consulte também [Pausar os recursos de computação][], [Retomar os recursos de computação][], [Dimensionar os recursos de computação][]


## Esvaziar as transações antes de pausar ou dimensionar 
Quando você pausa ou dimensiona o SQL Data Warehouse, internamente, sua instância do banco de dados é interrompida. Isso significa que todas as consultas em andamento serão canceladas. Cancelar uma simples consulta SELECT é uma operação rápida e quase não terá impacto sobre o tempo que leva para pausar ou dimensionar sua instância. No entanto, as consultas transacionais, que modificam os dados ou a estrutura dos dados, não conseguirão parar rapidamente. **As consultas transacionais devem terminar totalmente ou reverter suas alterações.** Reverter o trabalho concluído por uma consulta transacional pode levar tanto tempo, ou até mais, quanto a alteração original que a consulta estava aplicando. Por exemplo, se você cancelar uma consulta que estava excluindo linhas que já estava em execução por uma hora, poderá levar uma hora para o sistema inserir de volta as linhas que foram excluídas. Se você executar a pausa ou o dimensionamento enquanto as transações estiverem em andamento, a pausa ou o dimensionamento poderão demorar muito tempo porque eles têm que esperar a reversão ser concluída antes de prosseguir.

Consulte também [Transações no SQL Data Warehouse][], [Otimização de transações para o SQL Data Warehouse][]

## Manter as estatísticas
Ao contrário do SQL Server, que detecta e cria automaticamente ou atualiza as estatísticas em colunas que seriam um benefício, o SQL Data Warehouse requer uma manutenção manual das estatísticas. Embora estejamos planejando alterar isso no futuro, por enquanto você deve manter as estatísticas para assegurar que os planos do SQL Data Warehouse sejam otimizados. Os planos criados pelo otimizador são tão bons quanto as estatísticas disponíveis. **Criar estatísticas de exemplo em cada coluna é uma maneira fácil de começar a usar as estatísticas.** É igualmente importante atualizar as estatísticas quando ocorrem alterações significativas em seus dados. Pode ser uma abordagem conservadora atualizar as estatísticas diariamente ou após cada carga. Sempre há compensações entre o desempenho e o custo para a criação e a atualização das estatísticas. Se você achar que está demorando muito para manter todas as estatísticas, convém tentar ser mais seletivo sobre quais colunas têm estatísticas ou quais colunas precisam de uma atualização frequente. Por exemplo, convém atualizar as colunas de data, onde novos valores podem ser adicionados, diariamente. **Você terá mais benefícios se tiver estatísticas em colunas envolvidas em junções, colunas usadas na cláusula WHERE e colunas encontradas em GROUP BY.**

Consulte também [Gerenciar estatísticas no SQL Data Warehouse][], [CREATE STATISTICS (Transact-SQL)][], [UPDATE STATISTICS (Transact-SQL)][]

## Agrupar instruções INSERT em lotes
Uma única carga para uma pequena tabela com uma instrução INSERT ou mesmo uma recarga periódica de uma pesquisa pode ser útil para satisfazer suas necessidades com uma instrução como `INSERT INTO MyLookup VALUES (1, 'Type 1')`. No entanto, se você precisar carregar milhares ou milhões de linhas ao longo do dia, poderá achar que os INSERTS individuais poderão não dar conta. Em vez disso, desenvolva seus processos de modo que eles gravem em um arquivo e outro processo venha periodicamente e carregue esse arquivo.

Consulte também [Insert (Transact-SQL)][]
 
## Use o PolyBase para carregar e exportar dados rapidamente
O SQL Data Warehouse oferece suporte ao carregamento e exportação dos dados por meio de várias ferramentas, incluindo o Azure Data Factory, PolyBase e BCP. Para pequenas quantidades de dados em que o desempenho não é essencial, qualquer ferramenta poderá ser suficiente para satisfazer suas necessidades. No entanto, quando você estiver carregando ou exportando grandes volumes de dados ou um desempenho rápido for necessário, o PolyBase será a melhor opção. O PolyBase foi projetado para aproveitar a arquitetura MPP (Processamento Paralelo Massivo) do SQL Data Warehouse e, portanto, carregará e exportará grandes quantidades de dados mais rapidamente do que qualquer outra ferramenta. As cargas do PolyBase podem ser executadas usando CTAS ou INSERT INTO. **Usar o CTAS minimizará o registro em log das transações e será o modo mais rápido de carregar os dados.** O Azure Data Factory também oferece suporte para as cargas do PolyBase. O PolyBase oferece suporte a vários formatos de arquivo, incluindo os arquivos Gzip. **Para maximizar a taxa de transferência ao usar os arquivos de texto gzip, divida os arquivos em 60 ou mais arquivos para maximizar o paralelismo de sua carga.** Para ter uma taxa de transferência total mais rápida, considere carregar os dados simultaneamente.

Consulte também [Carregar dados no SQL Data Warehouse][], [Guia para usar o PolyBase no SQL Data Warehouse][], [Padrões e estratégias de carregamento do SQL Data Warehouse do Azure][], [Carregar os Dados com o Azure Data Factory][], [Mover dados de e para o SQL Data Warehouse do Azure usando o Azure Data Factory][], [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][], [CTAS (Create Table As Select) no SQL Data Warehouse][]

## Tabelas grandes com distribuição Hash
Por padrão, as tabelas são distribuídas pelo método Round Robin. Isso facilita para os usuários começar a criar tabelas sem ter de decidir sobre como as tabelas deverão ser distribuídas. Tabelas Round Robin podem ter um bom desempenho com algumas cargas de trabalho, mas, geralmente, o desempenho será muito melhor se uma coluna de distribuição for escolhida. O exemplo mais comum de quando uma tabela distribuída por uma coluna superará bastante uma tabela Round Robin ocorre quando duas tabelas de fatos grandes são unidas. Por exemplo, se você tiver uma tabela de pedidos, que é distribuída por order\_id, e uma tabela de transações, que também é distribuída por order\_id, quando você unir a tabela de pedidos e a tabela de transações em order\_id, a consulta se tornará uma consulta de passagem, o que significa que podemos eliminar as operações de movimentação de dados. Menos etapas significam uma consulta mais rápida. Menos movimento de dados também resulta em consultas mais rápidas. Essa explicação é bem superficial. Ao carregar uma tabela distribuída, verifique se os dados de entrada não estão classificados na chave de distribuição, pois isso reduzirá a velocidade das cargas. Consulte os links abaixo para obter muito mais detalhes sobre como escolher uma coluna de distribuição pode melhorar o desempenho e também como definir uma tabela distribuída na cláusula WITH da instrução CREATE TABLES.

Consulte também [Distribuição de hash e seu efeito no desempenho da consulta no SQL Data Warehouse][], [Escolher tabela distribuída de hash versus tabela distribuída pelo método Round Robin][], [CREATE TABLE (SQL Data Warehouse do Azure, Parallel Data Warehouse)][], [CREATE TABLE AS SELECT (SQL Data Warehouse do Azure)][]

## Não estender a partição
Embora o particionamento dos dados possa ser muito eficiente para manter seus dados na troca de partições ou otimizar as varreduras com a eliminação de partições, ter muitas partições pode reduzir a velocidade de suas consultas. Geralmente, uma estratégia de particionamento com alta granularidade que pode funcionar bem no SQL Server pode não funcionar bem no SQL Data Warehouse. Ter muitas partições também poderá reduzir a eficiência dos índices columnstore clusterizados se cada partição tiver menos de 1 milhão de linhas. Lembre-se que, internamente, o SQL Data Warehouse particiona os dados em 60 bancos de dados, portanto, se você criar uma tabela com 100 partições, isso realmente resultará em 6.000 partições. Cada carga de trabalho é diferente, assim, o melhor conselho é fazer experiências com o particionamento para ver o que funciona melhor para sua carga de trabalho. Considere uma granularidade menor do que pode ter funcionado para você no SQL Server. Por exemplo, considere usar partições semanais ou mensais, em vez de partições diárias.

Consulte também [Partições de tabela no SQL Data Warehouse][]

## Minimizar os tamanhos das transações
As instruções INSERT, UPDATE e DELETE são executadas em uma transação e quando falham, devem ser revertidas. Para minimizar a possibilidade de uma reversão longa, minimize os tamanhos das transações sempre que possível. Isso pode ser feito dividindo as instruções INSERT, UPDATE e DELETE em partes. Por exemplo, se você tiver uma instrução INSERT e esperar que ela demore uma hora, se possível, divida-a em quatro partes, sendo cada uma executada em 15 minutos. Aproveite os casos especiais de Registro em Log Mínimo, como CTAS, TRUNCATE, DROP TABLE ou INSERT, para esvaziar as tabelas, reduzindo o risco de reversão. Outra maneira de eliminar as reversões é usar as operações de Metadados Somente, como a troca de partição para o gerenciamento de dados. Por exemplo, em vez de executar uma instrução DELETE para excluir todas as linhas em uma tabela onde order\_date foi em outubro de 2001, você poderia particionar os dados mensalmente e, em seguida, trocar a partição com os dados por uma partição vazia a partir de outra tabela (consulte exemplos de ALTER TABLE). Para as tabelas não particionadas, considere usar um CTAS para gravar os dados que você deseja manter em uma tabela em vez de usar DELETE. Se um CTAS demorar a mesma quantidade de tempo, será uma operação muito mais segura de executar quando ele tiver um log de transação mínimo e puder ser cancelado rapidamente, se necessário.

Consulte também [Transações no SQL Data Warehouse][], [Otimização de transações para o SQL Data Warehouse][], [Partições de tabela no SQL Data Warehouse][], [TRUNCATE TABLE (Transact-SQL)][], [ALTER TABLE (Transact-SQL)][], [CTAS (Create Table As Select) no SQL Data Warehouse][]

## Use o menor tamanho de coluna possível
Ao definir a DDL, usar o menor tipo de dados que oferece suporte a seus dados melhorará o desempenho da consulta. Isso é especialmente importante para as colunas CHAR e VARCHAR. Se o maior valor em uma coluna for 25 caracteres, então, defina a coluna como VARCHAR(25). Evite definir todas as colunas de caractere para um tamanho grande padrão. Além disso, defina as colunas como VARCHAR quando isso for realmente necessário em vez de usar NVARCHAR.

Consulte também [CREATE TABLE (SQL Data Warehouse do Azure, Parallel Data Warehouse)][]

## Usar tabelas de heap temporárias para dados transitórios
Quando você estiver descarregando temporariamente os dados no SQL Data Warehouse, poderá achar que usar uma tabela de heap tornará o processo geral mais rápido. Se estiver carregando os dados apenas para prepará-los antes de executar mais transformações, carregar a tabela na tabela de heap será muito mais rápido que carregar os dados em uma tabela columnstore clusterizado. Além disso, o carregamento de dados em uma tabela temporária também carregará muito mais rapidamente do que carregar uma tabela em um armazenamento permanente. As tabelas temporárias começam por "#" e só podem ser acessadas pela sessão que as criou, portanto, elas podem não funcionar em alguns cenários. As tabelas de heap são definidas na cláusula WITH de CREATE TABLE. Se você usar uma tabela temporária, lembre-se também de criar estatísticas nela.

Consulte também [Tabelas temporárias no SQL Data Warehouse][], [CREATE TABLE (SQL Data Warehouse do Azure, Parallel Data Warehouse)][], [CREATE TABLE AS SELECT (SQL Data Warehouse do Azure)][]

## Otimizar tabelas columnstore clusterizadas
Os índices columnstore clusterizado são uma das maneiras mais eficientes de poder armazenar os dados no SQL Data Warehouse do Azure. Por padrão, as tabelas no SQL Data Warehouse são criadas como ColumnStore Clusterizado. É importante ter um segmento de boa qualidade para obter o melhor desempenho para as consultas nas tabelas columnstore. Quando as linhas são gravadas nas tabelas columnstore sob pressão da memória, a qualidade do segmento columnstore pode ficar prejudicada. A qualidade de segmento pode ser medida pelo número de linhas em um grupo de linhas compactado. Consulte a seção **Qualidade do Segmento Columnstore Clusterizado** na [Solução de problemas][] para obter instruções passo a passo sobre como detectar e melhorar a qualidade do segmento para as tabelas columnstore clusterizado. Uma vez que obter segmentos columnstore de boa qualidade é muito importante, geralmente é uma boa ideia criar ids de usuários especiais apenas para o carregamento que utiliza uma classe de recurso média ou grande. Quanto menos DWUs você usar, maior será a classe de recurso que você desejará atribuir ao usuário do carregamento.

Como as tabelas columnstore geralmente não enviam os dados em um segmento columnstore compactado até haver mais de 1 milhão de linhas por tabela e cada tabela do SQL Data Warehouse é particionada em 60 tabelas, como regra geral, as tabelas columnstore não aproveitarão uma consulta a menos que a tabela tenha mais de 60 milhões de linhas. Para a tabela com menos de 60 milhões de linhas, talvez não faça sentido ter um índice columnstore. Também pode não prejudicar. Além disso, se você particionar seus dados, desejará considerar que cada partição precisará ter 1 milhão de linhas para se beneficiar de um índice columnstore clusterizado. Se uma tabela tiver 100 partições, será necessário ter pelo menos 6 bilhões de linhas para se beneficiar de um columnstore clusterizado (60 distribuições * 100 partições * 1 milhão de linhas). Se a tabela não tiver as 6 bilhões de linhas neste exemplo, reduza o número de partições ou considere usar uma tabela de heap. Também pode valer a pena testar para ver se um melhor desempenho pode ser obtido com uma tabela de heap com índices secundários em vez de uma tabela columnstore. As tabelas columnStore ainda não dão suporte aos índices secundários.

Ao consultar uma tabela columnstore, as consultas serão executadas mais rapidamente se você selecionar apenas as colunas necessárias.

Consulte também [Solução de problemas][], [Gerenciar índices columnstore no SQL Data Warehouse do Azure][], [Guia de índices columnstore][]

## Usar uma classe maior de recursos para melhorar o desempenho da consulta
O SQL Data Warehouse usa grupos de recursos como uma maneira de alocar memória para as consultas. Para o uso imediato, todos os usuários são atribuídos à classe de recurso pequena, que concede 100 MB de memória por distribuição. Como sempre há 60 distribuições e cada distribuição recebe um mínimo de 100 MB, a alocação de memória total de todo o sistema é de 6.000 MB ou menos de 6 GB. Determinadas consultas, como junções grandes ou cargas para as tabelas columnstore clusterizado, se beneficiarão das alocações de memória maiores. Algumas consultas, como as varreduras puras, não terão qualquer benefício. Por outro lado, usar classes de recurso maiores afeta a simultaneidade, portanto, você deve levar isso em consideração antes de mover todos os usuários para uma classe de recurso grande.
 
Consulte também [Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse][]

## Usar uma Classe de Recurso Menor para Aumentar a Simultaneidade
Se você estiver percebendo que as consultas do usuário parecem atrasar muito, é possível que os usuários estejam executando classes de recurso maiores e consumindo muitos slots de simultaneidade, fazendo com que outras consultas entrem na fila. Para saber se as consultas dos usuários estão em fila, execute `SELECT * FROM sys.dm_pdw_waits` para ver se todas as linhas são retornadas.

Consulte também [Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse][], [sys.dm\_pdw\_waits (Transact-SQL)][]

## Usar DMVs para monitorar e otimizar suas consultas
O SQL Data Warehouse tem várias DMVs que podem ser usadas para monitorar a execução da consulta. O artigo de monitoramento abaixo apresenta instruções passo a passo sobre como analisar os detalhes de uma consulta em execução. Para encontrar rapidamente as consultas nessas DMVs, usar a opção LABEL com suas consultas poderá ajudar.

Consulte também [Monitorar sua carga de trabalho usando DMVs][], [Usar rótulos para consultas de instrumento no SQL Data Warehouse][], [Cláusula OPTION (Transact-SQL)][], [sys.dm\_exec\_sessions (Transact-SQL)][], [sys.dm\_pdw\_exec\_requests (Transact-SQL)][], [sys.dm\_pdw\_request\_steps (Transact-SQL)][], [sys.dm\_pdw\_sql\_requests (Transact-SQL)][], [sys.dm\_pdw\_dms\_workers (Transact-SQL)], [DBCC PDW\_SHOWEXECUTIONPLAN (Transact-SQL)][], [sys.dm\_pdw\_waits (Transact-SQL)][]

## Outros recursos
Há muitos lugares para obter informações sobre como usar o SQL Data Warehouse do Azure. Este artigo faz parte da documentação do Azure e tem muitos links para outros artigos do Azure, bem como artigos do MSDN. Monitoramos todos estes artigos para obter seus comentários e fazer atualizações frequentes. Se você achar um artigo útil, informe respondendo à pergunta: "Esta página foi útil?". Você pode fornecer comentários se responder Sim ou não. Se você achar um artigo útil, mas tiver comentários, clique em Sim e adicione seus comentários sobre qualquer coisa para que possamos melhorar o artigo. Se você não receber essa pergunta, ela sempre poderá ser encontrada no final de cada artigo do Azure e para os artigos do MSDN, use o link "Alguma sugestão?" no canto superior direito de cada página do MSDN. Valorizamos seus comentários e tomamos uma atitude para a maior parte deles.

Se você tiver **sugestões de recursos** para o SQL Data Warehouse, use a página [Comentários para o SQL Data Warehouse do Azure][]. Adicionar suas solicitações ou recomendar outras solicitações nos ajudará a priorizar recursos.

O [Fórum do MSDN do SQL Data Warehouse do Azure][] foi criado como um local para fazer perguntas a outros usuários e ao Grupo de Produtos do SQL Data Warehouse. Monitoramos ativamente esse fórum para garantir que sua pergunta seja respondida por outro usuário ou um de nós. Se você preferir fazer perguntas sobre Stack Overflow, também temos um [Fórum sobre Stack Overflow do SQL Data Warehouse do Azure][].

<!--Image references-->

<!--Article references-->
[create a support ticket]: sql-data-warehouse-get-started-create-support-ticket.md
[Gerenciamento de simultaneidade e carga de trabalho no SQL Data Warehouse]: sql-data-warehouse-develop-concurrency.md
[CTAS (Create Table As Select) no SQL Data Warehouse]: sql-data-warehouse-develop-ctas.md
[Guia para usar o PolyBase no SQL Data Warehouse]: sql-data-warehouse-load-polybase-guide.md
[Distribuição de hash e seu efeito no desempenho da consulta no SQL Data Warehouse]: sql-data-warehouse-develop-hash-distribution-key.md
[Carregar dados no SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Carregar os Dados com o Azure Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase in SQL Data Warehouse]: sql-data-warehouse-get-started-load-with-polybase.md
[Gerenciar índices columnstore no SQL Data Warehouse do Azure]: sql-data-warehouse-manage-columnstore-indexes.md
[Gerenciar estatísticas no SQL Data Warehouse]: sql-data-warehouse-develop-statistics.md
[Monitorar sua carga de trabalho usando DMVs]: sql-data-warehouse-manage-monitor.md
[Mover dados de e para o SQL Data Warehouse do Azure usando o Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[Otimização de transações para o SQL Data Warehouse]: sql-data-warehouse-develop-best-practices-transactions.md
[Pausar os recursos de computação]: sql-data-warehouse-manage-compute-overview.md#pause-compute-bk
[Retomar os recursos de computação]: sql-data-warehouse-manage-compute-overview.md#resume-compute-bk
[Dimensionar os recursos de computação]: sql-data-warehouse-manage-compute-overview.md#scale-performance-bk
[Table design in SQL Data Warehouse]: sql-data-warehouse-develop-table-design.md
[Partições de tabela no SQL Data Warehouse]: sql-data-warehouse-develop-table-partitions.md
[Tabelas temporárias no SQL Data Warehouse]: sql-data-warehouse-develop-temporary-tables.md
[Transações no SQL Data Warehouse]: sql-data-warehouse-develop-transactions.md
[Solução de problemas]: sql-data-warehouse-troubleshoot.md
[Usar rótulos para consultas de instrumento no SQL Data Warehouse]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[ALTER TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/ms190273.aspx
[Guia de índices columnstore]: https://msdn.microsoft.com/library/gg492088.aspx
[CREATE TABLE (SQL Data Warehouse do Azure, Parallel Data Warehouse)]: https://msdn.microsoft.com/library/mt203953.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE TABLE AS SELECT (SQL Data Warehouse do Azure)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE STATISTICS (Transact-SQL)]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC PDW\_SHOWEXECUTIONPLAN (Transact-SQL)]: https://msdn.microsoft.com/library/mt204017.aspx
[Insert (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[Cláusula OPTION (Transact-SQL)]: https://msdn.microsoft.com/library/ms190322.aspx
[sys.dm\_exec\_sessions (Transact-SQL)]: https://msdn.microsoft.com/library/ms176013.aspx
[sys.dm\_pdw\_exec\_requests (Transact-SQL)]: https://msdn.microsoft.com/library/mt203887.aspx
[sys.dm\_pdw\_request\_steps (Transact-SQL)]: https://msdn.microsoft.com/library/mt203913.aspx
[sys.dm\_pdw\_sql\_requests (Transact-SQL)]: https://msdn.microsoft.com/library/mt203889.aspx
[sys.dm\_pdw\_dms\_workers (Transact-SQL)]: https://msdn.microsoft.com/library/mt203878.aspx
[sys.dm\_pdw\_waits (Transact-SQL)]: https://msdn.microsoft.com/library/mt203893.aspx
[TRUNCATE TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/ms177570.aspx
[UPDATE STATISTICS (Transact-SQL)]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->
[Escolher tabela distribuída de hash versus tabela distribuída pelo método Round Robin]: https://blogs.msdn.microsoft.com/sqlcat/2015/08/11/choosing-hash-distributed-table-vs-round-robin-distributed-table-in-azure-sql-dw-service/
[Comentários para o SQL Data Warehouse do Azure]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Fórum do MSDN do SQL Data Warehouse do Azure]: https://social.msdn.microsoft.com/Forums/sqlserver/home?forum=AzureSQLDataWarehouse
[Fórum sobre Stack Overflow do SQL Data Warehouse do Azure]: http://stackoverflow.com/questions/tagged/azure-sqldw
[Padrões e estratégias de carregamento do SQL Data Warehouse do Azure]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/06/azure-sql-data-warehouse-loading-patterns-and-strategies

<!---HONumber=AcomDC_0608_2016-->