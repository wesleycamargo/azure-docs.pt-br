<properties
	pageTitle="Habilitar o Banco de Dados de Stretch para uma tabela | Microsoft Azure"
	description="Saiba como configurar uma tabela para o Banco de Dados de Stretch."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Habilitar o Banco de Dados de Stretch para uma tabela

Para configurar uma tabela para o Banco de dados de Stretch, escolha **Stretch | Habilitar** para uma tabela no SQL Server Management Studio a fim de abrir o assistente **Habilitar Tabela para Stretch**. Também é possível usar o Transact-SQL para habilitar o Stretch Database em uma tabela existente ou para criar uma nova tabela com o Stretch Database habilitado.

-   Se você armazenar dados históricos em uma tabela separada, poderá migrar toda a tabela.

-   Se a sua tabela contiver dados históricos e atuais, você poderá especificar um predicado de filtro para selecionar as linhas para migração.

**Pré-requisitos**. Se você selecionar **Stretch | Habilitar** para uma tabela e ainda não tiver habilitado o Stretch Database para o banco de dados, o assistente primeiro configurará o banco de dados para o Stretch Database. Execute as etapas em [Get started by running the Enable Database for Stretch Wizard](sql-server-stretch-database-wizard.md) (Comece executando o assistente para habilitar o banco de dados para Stretch) em vez das etapas contidas neste tópico.

**Permissões**. A habilitação do Banco de Dados de Stretch em um banco de dados ou tabela exige permissões db\_owner. A habilitação do Banco de Dados de Stretch em uma tabela também exige permissões ALTERAR na tabela.

## <a name="EnableWizardTable"></a>Usar o assistente para habilitar o Stretch Database em uma tabela
**Iniciar o assistente**

1.  No SQL Server Management Studio, no Pesquisador de Objetos, selecione a tabela na qual você deseja habilitar o Stretch.

2.  Clique com o botão direito do mouse e selecione **Stretch** e, em seguida, selecione **Habilitar** para iniciar o assistente.

**Introdução**

Examine o objetivo do assistente e os pré-requisitos.

**Selecionar tabelas de banco de dados**

Confirme se a tabela que você deseja habilitar está sendo exibida e foi selecionada.

Você pode migrar uma tabela inteira ou especificar um predicado de filtro simples no assistente. Se você quiser usar um tipo diferente de predicado de filtro para selecionar as linhas a serem migradas, siga um destes procedimentos.

-   Saia do assistente e execute a instrução ALTER TABLE para habilitar o Stretch para tabela e especificar um predicado.

-   Execute a instrução ALTER TABLE para especificar um predicado depois que você sair do assistente. Para conhecer as etapas necessárias, confira [Adicionar um predicado de filtro após executar o assistente](sql-server-stretch-database-predicate-function.md#addafterwiz).

A sintaxe ALTER TABLE é descrita posteriormente neste tópico.

**Resumo**

Examine os valores inseridos e as opções selecionadas no assistente. Em seguida, escolha **Concluir** para habilitar o Stretch.

**Resultados**

Revise os resultados.

## <a name="EnableTSQLTable"></a>Usar o Transact-SQL para habilitar o Stretch Database em uma tabela
É possível habilitar o Stretch Database em uma tabela existente ou criar uma nova tabela com o Stretch Database habilitado usando o Transact-SQL.

### Opções
Use as seguintes opções ao executar CREATE TABLE ou ALTER TABLE para habilitar o Stretch Database em uma tabela.

-   Opcionalmente, use a cláusula `FILTER_PREDICATE = <predicate>` para especificar um predicado a fim de selecionar as linhas para migração se a tabela contiver dados históricos e atuais. O predicado deve chamar uma função embutida com valor de tabela. Para obter mais informações, consulte [Selecionar linhas para migrar pelo uso de um predicado de filtro](sql-server-stretch-database-predicate-function.md). Se você não especificar um predicado de filtro, toda a tabela será migrada.

    >   [AZURE.NOTE] Se você fornecer um predicado de filtro que apresente um desempenho ruim, a migração de dados também terá um desempenho ruim. O Banco de Dados de Stretch aplica o predicado de filtro à tabela usando o operador CROSS APPLY.

-   Especifique `MIGRATION_STATE = OUTBOUND` para iniciar a migração de dados imediatamente ou `MIGRATION_STATE = PAUSED` para adiar o início da migração de dados.

### Habilitar o Stretch Database para uma tabela existente
Para configurar uma tabela existente para o Stretch Database, execute o comando ALTER TABLE.

Veja um exemplo que migra toda a tabela e começa a migração de dados imediatamente.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
Veja um exemplo que migra apenas as linhas identificadas pela função embutida com valor de tabela `dbo.fn_stretchpredicate` e adia a migração de dados. Para obter mais informações sobre o predicado de filtro, confira [Selecionar linhas para migrar usando um predicado de filtro](sql-server-stretch-database-predicate-function.md).

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

Para obter mais informações, consulte [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### Criar uma nova tabela com o Stretch Database habilitado
Para criar uma nova tabela com o Stretch Database habilitado, execute o comando CREATE TABLE.

Veja um exemplo que migra toda a tabela e começa a migração de dados imediatamente.

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
Veja um exemplo que migra apenas as linhas identificadas pela função embutida com valor de tabela `dbo.fn_stretchpredicate` e adia a migração de dados. Para obter mais informações sobre o predicado de filtro, confira [Selecionar linhas para migrar usando um predicado de filtro](sql-server-stretch-database-predicate-function.md).

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

Para obter mais informações, consulte [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).


## Consulte também

[ALTERAR TABELA (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)

<!---HONumber=AcomDC_0608_2016-->