---
title: Desabilitar Stretch Database e retornar dados remotos | Microsoft Docs
description: Saiba como desabilitar o Banco de Dados de Stretch para uma tabela e opcionalmente retornar os dados remotos.
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 08291748-0dfd-4a7d-a6a4-a5618e9c248d
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: a140e89058f6a246e80b71248b82d0f6767337cb


---
# <a name="disable-stretch-database-and-bring-back-remote-data"></a>Desabilitar Banco de Dados de Stretch e retornar dados remotos
Para desabilitar o Banco de Dados de Stretch de uma tabela, escolha **Stretch** para uma tabela no SQL Server Management Studio. Então, selecione uma das seguintes opções.

* **Desabilitar | Recuperar dados do Azure**. Copie os dados remotos para a tabela do Azure de volta para o SQL Server, então, desabilite o Banco de Dados de Stretch para a tabela. Essa operação incorre em custos de transferência de dados e não pode ser cancelada.
* **Desabilitar | Deixar dados no Azure**. Desabilite o Banco de Dados de Stretch para a tabela.  Abandone os dados remotos para a tabela no Azure.

Você também pode usar o Transact\-SQL para desabilitar o Stretch Database para uma tabela ou um banco de dados.

Depois de desabilitar o Banco de Dados de Stretch de uma tabela, a migração de dados é interrompida e os resultados da consulta já não incluem os resultados da tabela remota.

Se quiser apenas pausar a migração de dados, confira [Pause and resume Stretch Database (Pausar e retomar o Stretch Database)](sql-server-stretch-database-pause.md).

> [!NOTE]
> Desabilitar o Stretch Database para uma tabela ou para um banco de dados não exclui o objeto remoto. Se deseja excluir a tabela remota ou o banco de dados remoto, você deve descartá-la(o) usando o Portal de Gerenciamento do Azure. Os objetos remotos continuarão gerando custos no Azure até você excluí-los. Para saber mais, confira [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

## <a name="disable-stretch-database-for-a-table"></a>Desabilitar o Banco de Dados de Stretch para uma tabela
### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-table"></a>Usar o SQL Server Management Studio para desabilitar o Banco de Dados de Stretch para uma tabela
1. No SQL Server Management Studio, no Pesquisador de Objetos, selecione a tabela para a qual você deseja habilitar o Banco de Dados de Stretch.
2. Clique com o botão direito do mouse, escolha **Ampliar** e selecione uma das seguintes opções.
   
   * **Desabilitar | Recuperar dados do Azure**. Copie os dados remotos para a tabela do Azure de volta para o SQL Server, então, desabilite o Banco de Dados de Stretch para a tabela. Este comando não pode ser cancelado.
     
     > [!NOTE]
     > Copiar os dados remotos para a tabela do Azure novamente para o SQL Server incorre em custos de transferência de dados. Para saber mais, confira [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).
     > 
     > 
     
     Depois de todos os dados remotos terem sido copiados do Azure de volta para o SQL Server, o Stretch será desabilitado para a tabela.
   * **Desabilitar | Deixar dados no Azure**. Desabilite o Banco de Dados de Stretch para a tabela.  Abandone os dados remotos para a tabela no Azure.
   
   > [!NOTE]
   > Desabilitar o Stretch Database para uma tabela não exclui os dados remotos ou a tabela remota. Se deseja excluir a tabela remota, você deve descartá-la usando o Portal de Gerenciamento do Azure. A tabela remota continuará gerando custos no Azure até você excluí-la. Para saber mais, confira [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
   > 
   > 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-table"></a>Usar o Transact\-SQL para desabilitar o Stretch Database para uma tabela
* Para desabilitar o Stretch para uma tabela e copiar os dados remotos para a tabela do Azure de volta para o SQL Server, execute o comando a seguir. Depois de todos os dados remotos terem sido copiados do Azure de volta para o SQL Server, o Stretch será desabilitado para a tabela.
  
  Este comando não pode ser cancelado.
  
  ```tsql
  USE <Stretch-enabled database name>;
  GO
  ALTER TABLE <Stretch-enabled table name>  
     SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;
  GO
  ```
  > [!NOTE]
  > Copiar os dados remotos para a tabela do Azure novamente para o SQL Server incorre em custos de transferência de dados. Para saber mais, confira [Detalhes de preços de transferências de dados](https://azure.microsoft.com/pricing/details/data-transfers/).
  > 
  > 
* Para desabilitar o Stretch de uma tabela e abandonar os dados remotos, execute o comando a seguir.
  
  ```tsql
  ALTER TABLE <table_name>
     SET ( REMOTE_DATA_ARCHIVE = OFF_WITHOUT_DATA_RECOVERY ( MIGRATION_STATE = PAUSED ) ) ;
  ```

> [!NOTE]
> Desabilitar o Stretch Database para uma tabela não exclui os dados remotos ou a tabela remota. Se deseja excluir a tabela remota, você deve descartá-la usando o Portal de Gerenciamento do Azure. A tabela remota continuará gerando custos no Azure até você excluí-la. Para saber mais, confira [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

## <a name="disable-stretch-database-for-a-database"></a>Desabilitar o Banco de Dados de Stretch para um banco de dados
Antes de desabilitar o Stretch Database para um banco de dados, você precisa desabilitá-lo nas tabelas individuais habilitadas para Stretch no banco de dados.

### <a name="use-sql-server-management-studio-to-disable-stretch-database-for-a-database"></a>Usar o SQL Server Management Studio para desabilitar o Banco de Dados de Stretch para um banco de dados
1. No SQL Server Management Studio, no Pesquisador de Objetos, selecione o banco de dados para o qual você deseja desabilitar o Banco de Dados de Stretch.
2. Clique com o botão direito do mouse, escolha **Tarefas** e **Stretch** e **Desabilitar**.

> [!NOTE]
> Desabilitar o Stretch Database para um banco de dados não exclui o banco de dados remoto. Se deseja excluir o banco de dados remoto, você deve descartá-la usando o Portal de Gerenciamento do Azure. O banco de dados remoto continuará gerando custos no Azure até você excluí-lo. Para saber mais, confira [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

### <a name="use-transact-sql-to-disable-stretch-database-for-a-database"></a>Usar o Transact\-SQL para desabilitar o Banco de Dados de Stretch para um banco de dados
Execute o comando a seguir.

```tsql
ALTER DATABASE <database name>
    SET REMOTE_DATA_ARCHIVE = OFF ;
```

> [!NOTE]
> Desabilitar o Stretch Database para um banco de dados não exclui o banco de dados remoto. Se deseja excluir o banco de dados remoto, você deve descartá-la usando o Portal de Gerenciamento do Azure. O banco de dados remoto continuará gerando custos no Azure até você excluí-lo. Para saber mais, confira [Preço do SQL Server Stretch Database](https://azure.microsoft.com/pricing/details/sql-server-stretch-database/).
> 
> 

## <a name="see-also"></a>Confira também
[Opções ALTER DATABASE SET (Transact-SQL)](https://msdn.microsoft.com/library/bb522682.aspx)

[Pausar e retomar o Banco de Dados de Stretch](sql-server-stretch-database-pause.md)




<!--HONumber=Nov16_HO3-->


