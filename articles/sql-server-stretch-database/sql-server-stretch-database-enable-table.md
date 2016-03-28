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
	ms.date="02/26/2016"
	ms.author="douglasl"/>

# Habilitar o Banco de Dados de Stretch para uma tabela

Para configurar uma tabela para o Banco de dados de Stretch, escolha **Stretch | Habilitar** para uma tabela no SQL Server Management Studio a fim de abrir o assistente **Habilitar Tabela para Stretch**. Você também pode usar o Transact-SQL para habilitar o Banco de Dados de Stretch em uma tabela.

-   Se você armazenar dados históricos em uma tabela separada, poderá migrar toda a tabela.

-   Se a sua tabela contiver dados históricos e atuais, você poderá especificar um predicado de filtro para selecionar as linhas para migração. No CTP 3.1, por meio do RC0, a opção para especificar um predicado de filtro não está disponível no assistente Habilitar Banco de Dados para o Stretch. Você precisa usar a instrução ALTERAR TABELA para configurar uma tabela para o Stretch Database com essa opção. Para saber mais, confira [ALTERAR TABELA (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

**Pré-requisitos**. Se você selecionar **Stretch | Habilitar** para uma tabela e ainda não tiver habilitado o Banco de Dados de Stretch para o banco de dados, o assistente primeiro configurará o banco de dados para o Banco de Dados de Stretch. Execute as etapas em [Assistente Habilitar Banco de Dados para o Banco de Dados de Stretch](sql-server-stretch-database-wizard.md) em vez das etapas deste tópico.

**Permissões**. A habilitação do Banco de Dados de Stretch em um banco de dados ou tabela exige permissões db\_owner. A habilitação do Banco de Dados de Stretch em uma tabela também exige permissões ALTERAR na tabela.

## <a name="EnableWizardTable"></a>Use o assistente para habilitar o Banco de Dados de Stretch em uma tabela
**Iniciar o assistente**
1.  No SQL Server Management Studio, no Pesquisador de Objetos, selecione a tabela na qual você deseja habilitar o Stretch.

2.  Clique com botão direito do mouse e selecione **Stretch** e, em seguida, escolha **Habilitar** para iniciar o assistente.

**Introdução** Confira o objetivo do assistente e os pré-requisitos.

**Selecionar tabelas do banco de dados** Confirme se a tabela que você deseja habilitar está sendo exibida e foi escolhida.

No CTP 3.1, por meio do RC0, só é possível migrar uma tabela inteira usando o assistente. Se você quiser especificar um predicado a fim de selecionar as linhas para migração em uma tabela que contém os dados atuais e históricos, execute a instrução ALTERAR TABELA para especificar um predicado depois de sair do assistente, ou saia do assistente e execute a instrução ALTERAR TABELA, conforme descrito mais adiante neste tópico.

**Resumo** Examine os valores inseridos e as opções selecionadas no assistente. Em seguida, escolha **Concluir** para habilitar o Stretch.

**Resultados** Revise os resultados.

## <a name="EnableTSQLTable"></a>Use o Transact-SQL para habilitar o Stretch Database em uma tabela
Para configurar uma tabela para o Banco de Dados de Stretch, execute o comando ALTERAR TABELA.

1.  Como opção, use a cláusula `FILTER_PREDICATE = <predicate>` para especificar um predicado a fim de selecionar as linhas migração, se a tabela contiver dados históricos e atuais. O predicado deve chamar uma função embutida com valor de tabela. Para saber mais, confira [Escrever uma Função embutida com valor de tabela a fim de selecionar linhas (Stretch Database)](sql-server-stretch-database-predicate-function.md). Se você não especificar um predicado de filtro, toda a tabela será migrada.

    > [IMPORTANTE] Se você fornecer um predicado de filtro que apresente um desempenho ruim, a migração de dados também terá um desempenho ruim. O Banco de Dados de Stretch aplica o predicado de filtro à tabela usando o operador CROSS APPLY.

    No CTP 3.1, por meio do RC0, essa opção não está disponível no assistente Habilitar Banco de Dados para o Stretch. Você precisa usar a instrução ALTERAR TABELA para configurar uma tabela para o Stretch Database com essa opção. Para saber mais, confira [ALTERAR TABELA (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx.

2.  Especifique `MIGRATION_STATE = OUTBOUND` para iniciar a migração de dados imediatamente ou `MIGRATION_STATE = PAUSED` para adiar o início da migração de dados.

Veja um exemplo que migra toda a tabela e começa a migração de dados imediatamente.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
GO;
```
Veja um exemplo que migra apenas as linhas identificadas pela função embutida com valor de tabela `dbo.fn_stretchpredicate` e adia a migração de dados. Para saber mais sobre o predicado de filtro, confira [Escrever uma Função embutida com valor de tabela a fim de selecionar linhas (Stretch Database)](sql-server-stretch-database-predicate-function.md).

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) )
```

## Consulte também

[ALTERAR TABELA (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

<!---HONumber=AcomDC_0316_2016-->