---
title: Habilitar o Stretch Database para uma tabela | Microsoft Docs
description: Saiba como configurar uma tabela para o Banco de Dados de Stretch.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 63724114-82c1-4b00-ac50-c3ade6a69d47
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c9aa9a1abb4f436cb44e67d236682ff58a92531


---
# <a name="enable-stretch-database-for-a-table"></a>Habilitar o Banco de Dados de Stretch para uma tabela
Para configurar uma tabela para o Stretch Database, escolha **Stretch | Habilitar** para uma tabela no SQL Server Management Studio a fim de abrir o assistente **Habilitar Tabela para Stretch**. Também é possível usar o Transact\-SQL para habilitar o Stretch Database em uma tabela existente ou para criar uma nova tabela com o Stretch Database habilitado.

* Se armazenar os dados inativos em uma tabela separada, você poderá migrar toda a tabela.
* Se sua tabela contiver dados ativos e inativos, você poderá especificar uma função de filtro para selecionar as linhas para migrar.

**Pré-requisitos**. Se você selecionar **Stretch | Habilitar** para uma tabela e ainda não tiver habilitado o Stretch Database para o banco de dados, o assistente primeiro configurará o banco de dados para o Stretch Database. Execute as etapas em [Get started by running the Enable Database for Stretch Wizard](sql-server-stretch-database-wizard.md) (Comece executando o assistente para habilitar o banco de dados para Stretch) em vez das etapas contidas neste tópico.

**Permissões**. A habilitação do Banco de Dados de Stretch em um banco de dados ou tabela exige permissões db\_owner. A habilitação do Stretch Database em uma tabela também exige permissões ALTERAR na tabela.

> [!NOTE]
> Mais tarde, se você desabilitar o Stretch Database, lembre-se de que desabilitar o Stretch Database para uma tabela ou para um banco de dados não exclui o objeto remoto. Se deseja excluir a tabela remota ou o banco de dados remoto, você deve descartá-la(o) usando o Portal de Gerenciamento do Azure. Os objetos remotos continuarão gerando custos no Azure até você excluí-los manualmente.
> 
> 

## <a name="a-nameenablewizardtableause-the-wizard-to-enable-stretch-database-on-a-table"></a><a name="EnableWizardTable"></a>Usar o assistente para habilitar o Stretch Database em uma tabela
**Iniciar o assistente**

1. No SQL Server Management Studio, no Pesquisador de Objetos, selecione a tabela na qual você deseja habilitar o Stretch.
2. Clique com o botão direito do mouse, escolha **Stretch** e **Habilitar** para iniciar o assistente.

**Introdução**

Examine o objetivo do assistente e os pré-requisitos.

**Selecionar tabelas de banco de dados**

Confirme se a tabela que você deseja habilitar está sendo exibida e foi selecionada.

Você pode migrar uma tabela inteira ou especificar uma função de filtro simples no assistente. Se você quiser usar um tipo diferente de função de filtro para selecionar as linhas a serem migradas, faça um destes procedimentos.

* Saia do assistente e execute a instrução ALTER TABLE para habilitar o Stretch para a tabela e especificar uma função de filtro.
* Execute a instrução ALTER TABLE para especificar uma função de filtro depois de sair do assistente. Para conhecer as etapas necessárias, confira [Add a filter function after running the Wizard (Adicionar uma função de filtro após executar o assistente)](sql-server-stretch-database-predicate-function.md#addafterwiz).

A sintaxe ALTER TABLE é descrita posteriormente neste tópico.

**Resumo**

Examine os valores inseridos e as opções selecionadas no assistente. Em seguida, escolha **Concluir** para habilitar o Stretch.

**Resultados**

Revise os resultados.

## <a name="a-nameenabletsqltableause-transact-sql-to-enable-stretch-database-on-a-table"></a><a name="EnableTSQLTable"></a>Usar o Transact\-SQL para habilitar o Stretch Database em uma tabela
É possível habilitar o Stretch Database em uma tabela existente ou criar uma nova tabela com o Stretch Database habilitado usando o Transact-SQL.

### <a name="options"></a>Opções
Use as seguintes opções ao executar CREATE TABLE ou ALTER TABLE para habilitar o Stretch Database em uma tabela.

* Como opção, use a cláusula `FILTER_PREDICATE = <function>` para especificar uma função a fim de selecionar as linhas para migrar se a tabela contiver dados ativos e inativos. O predicado deve chamar uma função embutida com valor de tabela. Para saber mais, confira [Select rows to migrate by using a filter function (Selecionar linhas para migração usando uma função de filtro)](sql-server-stretch-database-predicate-function.md). Se você não especificar uma função de filtro, toda a tabela será migrada.
  
  > [!NOTE]
  > Se você fornecer uma função de filtro que apresente um desempenho ruim, a migração de dados também terá um desempenho ruim. O Stretch Database aplica a função de filtro à tabela usando o operador CROSS APPLY.
  > 
  > 
* Especifique `MIGRATION_STATE = OUTBOUND` para iniciar a migração de dados imediatamente ou `MIGRATION_STATE = PAUSED` para adiar o início da migração de dados.

### <a name="enable-stretch-database-for-an-existing-table"></a>Habilitar o Stretch Database para uma tabela existente
Para configurar uma tabela existente para o Stretch Database, execute o comando ALTER TABLE.

Veja um exemplo que migra toda a tabela e começa a migração de dados imediatamente.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```
Veja um exemplo que migra apenas as linhas identificadas pela função embutida com valor de tabela `dbo.fn_stretchpredicate` e adia a migração de dados. Para obter mais informações sobre a função de filtro, consulte [Selecionar linhas para migrar usando uma função de filtro](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
 GO
```

Para saber mais, confira [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### <a name="create-a-new-table-with-stretch-database-enabled"></a>Criar uma nova tabela com o Stretch Database habilitado
Para criar uma nova tabela com o Stretch Database habilitado, execute o comando CREATE TABLE.

Veja um exemplo que migra toda a tabela e começa a migração de dados imediatamente.

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```

Veja um exemplo que migra apenas as linhas identificadas pela função embutida com valor de tabela `dbo.fn_stretchpredicate` e adia a migração de dados. Para obter mais informações sobre a função de filtro, consulte [Selecionar linhas para migrar usando uma função de filtro](sql-server-stretch-database-predicate-function.md).

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
GO  
```

Para saber mais, confira [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).

## <a name="see-also"></a>Confira também
[ALTERAR TABELA (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)




<!--HONumber=Nov16_HO3-->


