<properties
	pageTitle="Desabilitar Banco de Dados de Stretch e retornar dados remotos | Microsoft Azure"
	description="Saiba como desabilitar o Banco de Dados de Stretch para uma tabela e opcionalmente retornar os dados remotos."
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

# Desabilitar Banco de Dados de Stretch e retornar dados remotos

Para desabilitar o Banco de Dados de Stretch de uma tabela, escolha **Stretch** para uma tabela no SQL Server Management Studio. Então, selecione uma das seguintes opções.

-   **Desabilitar | Retornar dados do Azure**. Copie os dados remotos para a tabela do Azure de volta para o SQL Server, então, desabilite o Banco de Dados de Stretch para a tabela. Essa operação incorre em custos de transferência de dados e não pode ser cancelada.

-   **Desabilitar | Deixar dados no Azure**. Desabilite o Banco de Dados de Stretch para a tabela. Abandone os dados remotos para a tabela no Azure.

Depois de desabilitar o Banco de Dados de Stretch de uma tabela, a migração de dados é interrompida e os resultados da consulta já não incluem os resultados da tabela remota.

Você também pode usar o Transact-SQL para desabilitar o Banco de Dados de Stretch para uma tabela ou um banco de dados.

Se apenas quiser pausar a migração de dados, consulte [Pausar e retomar o Banco de Dados de Stretch](sql-server-stretch-database-pause.md).

>   [AZURE.NOTE] Desabilitar o Stretch Database para uma tabela ou para um banco de dados não exclui o objeto remoto. Se deseja excluir a tabela remota ou o banco de dados remoto, você deve descartá-la(o) usando o Portal de Gerenciamento do Azure. Os objetos remotos continuam a incorrer em custos de armazenamento do Azure até você excluí-los. Para obter mais informações, consulte [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Desabilitar o Banco de Dados de Stretch para uma tabela

### Usar o SQL Server Management Studio para desabilitar o Banco de Dados de Stretch para uma tabela

1.  No SQL Server Management Studio, no Pesquisador de Objetos, selecione a tabela para a qual você deseja habilitar o Banco de Dados de Stretch.

2.  Clique com o botão direito do mouse, selecione **Ampliar** e selecione uma das seguintes opções.

    -   **Desabilitar | Recuperar dados do Azure**. Copie os dados remotos para a tabela do Azure de volta para o SQL Server, então, desabilite o Banco de Dados de Stretch para a tabela. Este comando não pode ser cancelado.

        >   [AZURE.NOTE] Copiar os dados remotos para a tabela do Azure novamente para o SQL Server incorre em custos de transferência de dados. Para obter mais informações, consulte [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

        Depois de todos os dados remotos terem sido copiados do Azure de volta para o SQL Server, o Stretch será desabilitado para a tabela.

    -   **Desabilitar | Deixar dados no Azure**. Desabilite o Banco de Dados de Stretch para a tabela. Abandone os dados remotos para a tabela no Azure.

    >   [AZURE.NOTE] Desabilitar o Stretch Database para uma tabela não exclui os dados remotos ou a tabela remota. Se deseja excluir a tabela remota, você deve descartá-la usando o Portal de Gerenciamento do Azure. A tabela remota continua a incorrer em custos de armazenamento do Azure até você excluí-la. Para obter mais informações, consulte [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Usar o Transact-SQL para desabilitar o Banco de Dados de Stretch para uma tabela

-   Para desabilitar o Stretch para uma tabela e copiar os dados remotos para a tabela do Azure de volta para o SQL Server, execute o comando a seguir. Este comando não pode ser cancelado.

    ```tsql
    ALTER TABLE <table name>
       SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
    ```
    >   [AZURE.NOTE] Copiar os dados remotos para a tabela do Azure novamente para o SQL Server incorre em custos de transferência de dados. Para obter mais informações, consulte [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).

    Depois de todos os dados remotos terem sido copiados do Azure de volta para o SQL Server, o Stretch será desabilitado para a tabela.

-   Para desabilitar o Stretch de uma tabela e abandonar os dados remotos, execute o comando a seguir.

    ```tsql
    ALTER TABLE <table_name>
       SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
    ```

>   [AZURE.NOTE] Desabilitar o Stretch Database para uma tabela não exclui os dados remotos ou a tabela remota. Se deseja excluir a tabela remota, você deve descartá-la usando o Portal de Gerenciamento do Azure. A tabela remota continua a incorrer em custos de armazenamento do Azure até você excluí-la. Para obter mais informações, consulte [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Desabilitar o Banco de Dados de Stretch para um banco de dados
Antes de desabilitar o Banco de Dados de Stretch para um banco de dados, você precisa desabilitar o Banco de Dados de Stretch nas tabelas individuais habilitadas para Stretch no banco de dados.

### Usar o SQL Server Management Studio para desabilitar o Banco de Dados de Stretch para um banco de dados

1.  No SQL Server Management Studio, no Pesquisador de Objetos, selecione o banco de dados para o qual você deseja desabilitar o Banco de Dados de Stretch.

2.  Clique com o botão direito do mouse, selecione **Tarefas** e, em seguida, selecione **Ampliar** e **Desabilitar**.

>   [AZURE.NOTE] Desabilitar o Stretch Database para um banco de dados não exclui o banco de dados remoto. Se deseja excluir o banco de dados remoto, você deve descartá-la usando o Portal de Gerenciamento do Azure. O banco de dados remoto continua a incorrer em custos de armazenamento do Azure até você excluí-lo. Para obter mais informações, consulte [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

### Usar o Transact-SQL para desabilitar o Banco de Dados de Stretch para um banco de dados
Execute o comando a seguir.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

>   [AZURE.NOTE] Desabilitar o Stretch Database para um banco de dados não exclui o banco de dados remoto. Se deseja excluir o banco de dados remoto, você deve descartá-la usando o Portal de Gerenciamento do Azure. O banco de dados remoto continua a incorrer em custos de armazenamento do Azure até você excluí-lo. Para obter mais informações, consulte [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).

## Consulte também

[Opções ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Pausar e retomar o Banco de Dados de Stretch](sql-server-stretch-database-pause.md)

<!-----------HONumber=AcomDC_0330_2016-->