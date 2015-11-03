<properties
	pageTitle="Use o OLTP Na Memória para melhorar o desempenho transacional do Azure SQL | Microsoft Azure"
	description="Adote o OLTP Na Memória para melhorar o desempenho transacional em um banco de dados SQL existente."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="10/28/2015"
	ms.author="jodebrui"/>


# Usar Na Memória (Visualização) para melhorar o desempenho do aplicativo Azure SQL

Siga estas etapas para otimizar o desempenho transacional do Banco de Dados SQL do Azure Premium usando [Na Memória](sql-database-in-memory.md).

Por comparação, escolha uma carga de trabalho semelhante à sua carga de trabalho de produção com um número semelhante de conexões simultâneas e taxa de leitura/gravação. Para minimizar a latência de rede, recomendamos executar sua carga de trabalho de teste na mesma região do Azure como o banco de dados.

## Etapa 1: Copiar os dados para um novo Banco de Dados Premium
1.	Exporte seu banco de dados de produção para um bacpac usando:

	R. A funcionalidade de exportação do [portal](https://portal.azure.com/) ou

	B. A funcionalidade Exportar Aplicativo da Camada de Dados no SQL Server Management Studio

2.	Importe o bacpac para um novo banco de dados Premium em um servidor V12:

	R. No portal: navegue até o servidor e selecione a opção Importar Banco de Dados. Selecione uma tipo de preço Premium.

	B. No SQL Server Management Studio (SSMS): conecte-se ao servidor, clique com o botão direito do mouse no nó Bancos de Dados e selecione Importar Aplicativo da Camada de Dados.


## Etapa 2: Identificar os objetos para migrar para o OLTP Na Memória
O SQL Server Management Studio (SSMS) inclui um relatório de análise de desempenho de transação que pode ser executado em um banco de dados com uma carga de trabalho ativa para identificar tabelas e procedimentos armazenados candidatos à migração para OLTP Na Memória. Confira [Determinando se uma tabela ou um procedimento armazenado deve ser movido para o OLTP Na Memória](https://msdn.microsoft.com/library/dn205133.aspx) para obter mais detalhes.

1.	Conecte-se ao banco de dados de produção usando o SSMS. Como alternativa, se você tiver uma carga de trabalho em execução no novo banco de dados de teste, poderá se conectar a ela também.
2.	Clique com o botão direito do mouse no banco de dados e selecione Relatórios -> Relatórios Padrão -> Relatório de Análise de Desempenho de Transação. O relatório permitirá que você identifique tabelas e procedimentos armazenados que podem se beneficiar do OLTP na memória, com base no uso.


## Etapa 3: Migrar tabelas
1.	Conecte-se ao novo banco de dados de teste usando o SSMS. Para evitar a necessidade de usar a opção WITH (SNAPSHOT) em consultas, é recomendável configurar a opção MEMORY\_OPTIMIZED\_ELEVATE\_TO\_SNAPSHOT do banco de dados.
2.	Uma vez conectado ao novo banco de dados de teste, execute:

   	    ALTER DATABASE CURRENT SET MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT=ON

3.	Migre a tabela baseada em disco para otimização de memória usando qualquer uma destas abordagens:

	R. Assistente de Otimização de Memória SSMS: quando conectado ao banco de dados de teste, clique com o botão direito do mouse na tabela e selecione o Supervisor de Otimização de Memória. Use o supervisor para determinar se a tabela tem algum recurso que não tenha suporte com otimização de memória. Caso contrário, o supervisor poderá realizar a migração do esquema e dos dados reais. Examine o [tópico do supervisor de otimização de memória no MSDN](https://msdn.microsoft.com/library/dn284308.aspx) para obter mais detalhes.

	B. Migração Manual: conecte-se ao novo banco de dados de teste usando o SSMS.

Siga estas etapas para migrar uma tabela:

1.	Crie a tabela usando um script ao clicar com o botão direito do mouse na tabela e selecione Script de Tabela como -> CREATE To -> Nova Janela de Consulta.
2.	Altere o índice CLUSTERIZADO para NONCLUSTERED e adicione a opção WITH (MEMORY\_OPTIMIZED = ON).
3.	Se a tabela usar qualquer recurso sem suporte, implemente soluções alternativas. O MSDN documenta como lidar com [recursos sem suporte comuns](https://msdn.microsoft.com/library/dn247639.aspx).
4.	Renomeie a tabela existente usando sp\_rename.
5.	Crie a nova tabela com otimização de memória executando o script CREATE TABLE.
6.	Copie os dados executando a seguinte instrução: ``INSERT INTO <new_memory_optimized_table> SELECT * FROM <old_disk_based_table>

## Etapa 4 (opcional): Migrar procedimentos armazenados

Conecte-se ao novo banco de dados de teste usando a versão de setembro de 2015 Preview do [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx) ou posterior.

Identifique todos os recursos sem suporte em procedimentos armazenados compilados nativamente executando o [Supervisor de Compilação Nativo](https://msdn.microsoft.com/library/dn284308.aspx) no procedimento anterior. As soluções alternativas para recursos sem suporte comuns são documentadas no [MSDN](https://msdn.microsoft.com/library/dn296678.aspx).

Duas coisas a serem consideradas ao migrar de um procedimento armazenado para nativo:

- Os módulos nativos exigem as seguintes opções:

	- NATIVE\_COMPILATION
	- SCHEMABINDING



- Os módulos nativos usam [blocos ATOMIC](https://msdn.microsoft.com/library/dn452281.aspx) para gerenciamento de transações; as instruções BEGIN TRAN/COMMIT/ROLLBACK explícitas não são necessárias.

Um procedimento armazenado compilado nativamente típico se parece com este:


   	    CREATE PROCEDURE schemaname.procedurename
   		@param1 type1, …
   		WITH NATIVE_COMPILATION, SCHEMABINDING
   		AS
   		BEGIN ATOMIC WITH (TRANSACTION ISOLATION LEVEL = SNAPSHOT, LANGUAGE = N'your_language')

   		…

   		END



Observe que embora SNAPSHOT seja o nível de isolamento mais comumente usado com a tabela com otimização de memória, REPEATABLE READ e SERIALIZABLE também têm suporte.

##### Siga estas etapas para migrar um procedimento armazenado:

1.	Crie um script para o procedimento armazenado clicando com o botão direito do mouse no procedimento e selecionando Script de Procedimento como -> CREATE To -> Nova Janela de Consulta.
2.	Reescreva o cabeçalho de procedimento usando o modelo acima e remova qualquer instrução BEGIN TRAN/ROLLBACK/COMMIT.
3.	Se o procedimento armazenado usar qualquer recurso sem suporte, implemente soluções alternativas. O MSDN documenta como lidar com [recursos sem suporte comuns](https://msdn.microsoft.com/library/dn296678.aspx).
4.	Descarte o procedimento usando DROP ou renomeie o procedimento antigo usando sp\_rename.
5.	Crie o novo procedimento armazenado compilado nativamente executando o script CREATE PROCEDURE.

## Etapa 5: Executar sua carga de trabalho
Execute sua carga de trabalho de teste nas tabelas com otimização de memória e nos procedimentos armazenados compilados nativamente e meça o ganho de desempenho.

## Próximas etapas

[Monitorar o armazenamento Na Memória](https://azure.microsoft.com/documentation/articles/sql-database-in-memory-oltp-monitoring/).

[Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)

<!---HONumber=Nov15_HO1-->