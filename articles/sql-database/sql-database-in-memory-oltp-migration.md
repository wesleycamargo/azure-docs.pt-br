<properties
	pageTitle="O OLTP Na Memória melhora o desempenho do txn SQL | Microsoft Azure"
	description="Adote o OLTP Na Memória para melhorar o desempenho transacional em um banco de dados SQL existente."
	services="sql-database"
	documentationCenter=""
	authors="jodebrui"
	manager="jeffreyg"
	editor="MightyPen"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/16/2015"
	ms.author="jodebrui"/>


# Usar Na Memória (visualização) para melhorar o desempenho do aplicativo no Banco de Dados SQL

Siga estas etapas para otimizar o desempenho transacional do Banco de Dados SQL do Azure [Premium](sql-database-service-tiers.md) existente usando o recurso [Na Memória](sql-database-in-memory.md).


## Etapa 1: Verifique se o banco de dados Premium dá suporte a Na Memória

Os bancos de dados Premium criados em novembro de 2015 ou posteriormente dão suporte ao recurso Na Memória. Você pode verificar se o seu banco de dados Premium oferece suporte ao recurso Na Memória ao executar a instrução Transact-SQL a seguir. Na Memória terá suporte se o resultado retornado for 1 (e não 0):

```
SELECT DatabasePropertyEx(Db_Name(), 'IsXTPSupported');
```

*XTP* significa *Processamento de Transação Extrema*

Se seu banco de dados existente tiver de ser movido para um novo banco de dados Premium V12, você poderá usar as técnicas a seguir para exportar e importar seus dados.

#### Etapas de exportação

Exporte seu banco de dados de produção para um bacpac usando:

- A funcionalidade [Exportar](sql-database-export.md) do [portal](https://portal.azure.com/).

- A funcionalidade **Exportar Aplicativo da Camada de Dados** em um [SSMS.exe atualizado](http://msdn.microsoft.com/library/mt238290.aspx) (SQL Server Management Studio).
 1. No **Pesquisador de Objetos**, expanda o nó **Bancos de Dados**.
 2. Clique com o botão direito do mouse no nó do seu banco de dados.
 3. Clique em **Tarefas** > **Exportar Aplicativo da Camada de Dados**.
 4. Opere a janela do assistente exibida.


#### Etapas de importação

Importe o bacpac para um novo banco de dados Premium.

1. No [portal](https://portal.azure.com/) do Azure,
 - Navegue até o servidor.
 - Selecione a opção [Importar Banco de Dados](sql-database-import.md).
 - Selecione uma camada de preços Premium.

2. Use o SSMS para importar o bacpac:
 - No **Pesquisador de Objetos**, clique com o botão direito do mouse no nó **Bancos de Dados**.
 - Clique em **Importar Aplicativo da Camada de Dados**.
 - Opere a janela do assistente exibida.


## Etapa 2: Identificar os objetos para migrar para o OLTP Na Memória

O SSMS inclui um relatório **Visão Geral da Análise do Desempenho da Transação** que pode ser executado em um banco de dados com uma carga de trabalho ativa. O relatório identifica as tabelas e os procedimentos armazenados candidatos à migração para OLTP Na Memória.

No SSMS, para gerar o relatório: - no **Pesquisador de Objetos**, clique com o botão direito do mouse no nó do seu banco de dados. - Clique em **Relatórios** > **Relatórios Padrão** > **Visão Geral da Análise do Desempenho da Transação**.

Para saber mais, confira [Determinando se uma tabela ou um procedimento armazenado deve ser transportado para o OLTP Na Memória](http://msdn.microsoft.com/library/dn205133.aspx).


## Etapa 3: Criar um banco de dados de teste comparável

Suponha que o relatório indique que o banco de dados tem uma tabela que pode se beneficiar do que está sendo convertido em uma tabela com otimização de memória. É recomendável que você primeiro teste para confirmar a indicação.

Você precisa de uma cópia de teste do seu banco de dados de produção. O banco de dados de teste deve estar no mesmo nível da camada de serviço que seu banco de dados de produção.

Para facilitar o teste, ajuste seu banco de dados de teste da seguinte maneira:

1. Conecte-se ao banco de dados de teste usando o SSMS.

2. Para evitar a necessidade da opção WITH (SNAPSHOT) em consultas, defina a opção de banco de dados como mostrado na seguinte instrução T-SQL: ```
ALTER DATABASE CURRENT
	SET
		MEMORY_OPTIMIZED_ELEVATE_TO_SNAPSHOT = ON;
```


## Etapa 4: migrar tabelas

Você deve criar e preencher uma cópia com otimização de memória da tabela que você deseja testar. Você pode criá-la usando:

- O Assistente de Otimização de Memória útil no SSMS.
- T-SQL Manual.


#### Assistente de Otimização de Memória no SSMS

Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste com o SSMS.

2. No **Pesquisador de Objetos**, clique com o botão direito do mouse na tabela e clique em **Supervisor de Otimização de Memória**.
 - O assistente **Supervisor Otimizador de Memória da Tabela** é exibido.

3. No assistente, clique em **Validação de migração** (ou no botão **Avançar**) para ver se a tabela tem algum recurso incompatível que não tenha suporte em tabelas com otimização de memória. Para obter mais informações, consulte:
 - A *lista de verificação de otimização de memória* no [Supervisor de Otimização de Memória](http://msdn.microsoft.com/library/dn284308.aspx).
 - [Construções Transact-SQL sem suporte do OLTP Na Memória](http://msdn.microsoft.com/library/dn246937.aspx).
 - [Migrando para o OLTP Na Memória](http://msdn.microsoft.com/library/dn247639.aspx).

4. Se a tabela não tiver recursos sem suporte, o supervisor poderá executar o esquema e a migração de dados reais para você.


#### T-SQL Manual

Para usar essa opção de migração:

1. Conecte-se ao banco de dados de teste usando o SSMS (ou um utilitário semelhante).

2. Obtenha o script T-SQL completo para a tabela e seus índices.
 - No SSMS, clique com o botão direito do mouse no nó da sua tabela.
 - Clique em **Script de Tabela como** > **CREATE Para** > **Nova Janela de Consulta**.

3. Na janela de script, adicione WITH (MEMORY\_OPTIMIZED = ON) à instrução CREATE TABLE.

4. Se houver um índice CLUSTERED, altere-o para NONCLUSTERED.

5. Renomeie a tabela existente usando SP\_RENAME.

6. Crie a nova cópia da tabela com otimização de memória executando o script CREATE TABLE editado.

7. Copie os dados para sua tabela com otimização de memória usando INSERT...SELECT * INTO:
	
```
INSERT INTO <new_memory_optimized_table>
		SELECT * FROM <old_disk_based_table>;
```


## Etapa 5 (opcional): migrar procedimentos armazenados

O recurso Na Memória também pode modificar um procedimento armazenado para melhorar o desempenho.


### Considerações sobre procedimentos armazenados compilados nativamente

Um procedimento armazenado compilado nativamente deve ter as seguintes opções na sua cláusula WITH T-SQL:

- NATIVE\_COMPILATION

- SCHEMABINDING: significa tabelas cujas definições de coluna o procedimento armazenado não pode alterar de alguma forma que afete o próprio procedimento armazenado, a menos que você descarte o procedimento armazenado.


Um módulo nativo deve usar grandes [blocos ATOMIC](http://msdn.microsoft.com/library/dn452281.aspx) para o gerenciamento de transações. Não há uma função para BEGIN TRANSACTION explícita ou para ROLLBACK TRANSACTION. Se seu código detectar uma violação de uma regra de negócio, poderá finalizar o bloco atômico com uma instrução [THROW](http://msdn.microsoft.com/library/ee677615.aspx).


### CREATE PROCEDURE típico para compilados nativamente

Normalmente, o T-SQL para criar um procedimento armazenado nativamente compilado é semelhante ao seguinte modelo:

```
CREATE PROCEDURE schemaname.procedurename
	@param1 type1, …
	WITH NATIVE_COMPILATION, SCHEMABINDING
	AS
		BEGIN ATOMIC WITH
			(TRANSACTION ISOLATION LEVEL = SNAPSHOT,
			LANGUAGE = N'your_language__see_sys.languages'
			)
		…
		END;
```

- Para TRANSACTION\_ISOLATION\_LEVEL, o SNAPSHOT é o valor mais comum para o procedimento armazenado nativamente compilado. No entanto, também há suporte para um subconjunto dos outros valores:
 - REPEATABLE READ
 - SERIALIZABLE


- O valor LANGUAGE deve estar presente na exibição sys.languages.


### Como migrar um procedimento armazenado

As etapas de migração são:


1. Obtenha o script CREATE PROCEDURE para o procedimento armazenado interpretado regular.

2. Reescreva o cabeçalho para que ele corresponda ao modelo anterior.

3. Verificar se o código T-SQL de procedimento armazenado usa os recursos sem suporte para procedimentos armazenados compilados nativamente. Implemente soluções alternativas, se necessário.
 - Para obter detalhes, consulte [Problemas de migração para procedimentos armazenados compilados nativamente](http://msdn.microsoft.com/library/dn296678.aspx).

4. Renomeie o antigo procedimento armazenado usando SP\_RENAME. Ou simplesmente descarte-o usando DROP.

5. Execute o script T-SQL CREATE PROCEDURE editado.


## Etapa 6: executar sua carga de trabalho no teste

Execute uma carga de trabalho em seu banco de dados de teste que seja semelhante à carga de trabalho executada em seu banco de dados de produção. Isso deve revelar o ganho de desempenho obtido com o uso do recurso Na Memória para tabelas e procedimentos armazenados.

Os principais atributos da carga de trabalho são:

- Número de conexões simultâneas.

- Taxa de leitura/gravação.


Para ajustar e executar a carga de trabalho de teste, considere usar a ferramenta útil ostress.exe, ilustrada [aqui](sql-database-in-memory.md).


Para minimizar a latência de rede, execute o teste na mesma região geográfica do Azure onde está o banco de dados.


## Etapa 7: Monitoramento pós-implementação

Considere monitorar os efeitos de desempenho de suas implementações de Na Memória em produção:

- [Monitorar o armazenamento na memória](sql-database-in-memory-oltp-monitoring.md).

- [Monitoramento de Banco de Dados SQL usando exibições de gerenciamento dinâmico](sql-database-monitoring-with-dmvs.md)


## Links relacionados

- [OLTP Na Memória (Otimização Na Memória)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Introdução aos procedimentos armazenados compilados nativamente](http://msdn.microsoft.com/library/dn133184.aspx)

- [Supervisor de Otimização de Memória](http://msdn.microsoft.com/library/dn284308.aspx)

<!---HONumber=AcomDC_0128_2016-->