---
title: Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure | Microsoft Docs
description: Crie um Banco de Dados SQL do Azure importando um arquivo BACPAC existente.
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: cf9a9631-56aa-4985-a565-1cacc297871d
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 60bcd02d24e2084b9020ce56ef6a9f8268c6b1b5
ms.openlocfilehash: e77a3ba82e7620e23441d9296ebed46d41cb525c


---
# <a name="import-a-bacpac-file-to-create-an-azure-sql-database-using-the-azure-portal"></a>Importar um arquivo BACPAC para criar um Banco de Dados SQL do Azure usando o Portal do Azure

Este artigo fornece instruções para criar um Banco de Dados SQL do Azure por meio de um arquivo BACPAC usando o [Portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para importar um Banco de Dados SQL de um BACPAC, você precisará do seguinte:

* Uma assinatura do Azure. 
* Um servidor do Banco de Dados SQL V12 do Azure. Se você não tiver um servidor V12, crie um seguindo as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).
* Um arquivo .bacpac do banco de dados que você deseja importar em um contêiner de blob da [conta de Armazenamento do Azure (standard)](../storage/storage-create-storage-account.md) .

> [!IMPORTANT]
> Ao importar um BACPAC do armazenamento de blobs do Azure, use o armazenamento standard. Não há suporte para a importação de um BACPAC do armazenamento premium.
> 

## <a name="import-the-database"></a>Importar o banco de dados
Abra a folha do SQL Server:

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Clique em **Servidores SQL**.
3. Clique no servidor no qual restaurar o banco de dados.
4. Na folha SQL Server, clique em **Importar banco de dados** para abrir a folha **Importar banco de dados**:
   
   ![Importar banco de dados][1]
5. Clique em **Armazenamento**, selecione sua conta de armazenamento, o contêiner de blob, o arquivo .bacpac e clique em **OK**.
   
   ![configurar opções de armazenamento][2]
6. Selecione a camada de preços para o novo banco de dados e clique em **Selecionar**. Atualmente, não há suporte para a importação de um banco de dados diretamente para um pool elástico, mas você pode primeiro importar como um banco de dados autônomo e, em seguida, mover o banco de dados para um pool.
   
   ![selecionar camada de preços][3]
7. Insira um **NOME DE BANCO DE DADOS** para o banco de dados que será criado do arquivo BACPAC.
8. Escolha o tipo de autenticação e, em seguida, forneça as informações de autenticação para o servidor. 
9. Clique em **Criar** para criar o banco de dados do BACPAC.
   
   ![criar banco de dados][4]

Clicar em **Criar** envia para o serviço uma solicitação para importar o banco de dados. Dependendo do tamanho do banco de dados, a operação de importação pode demorar para ser concluída.

## <a name="monitor-the-progress-of-the-import-operation"></a>Monitorar o progresso da operação de importação
1. Clique em **Servidores SQL**.
2. Clique no servidor para o qual está restaurando.
3. Na folha SQL Server, na área Operações, clique em **Histórico de importação/exportação**:
   
   ![importar o histórico de exportação][5]
   ![importar o histórico de exportação][6]

4. Para verificar se o banco de dados está ativo no servidor, clique em **Bancos de Dados SQL** e verifique se o novo banco de dados está **Online**.

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre como conectar e consultar um Banco de Dados SQL importado, confira [Conectar-se ao Banco de Dados SQL com o SQL Server Management Studio e executar um exemplo de consulta T-SQL](sql-database-connect-query-ssms.md).
* Para ler uma postagem de blog da Equipe de Consultoria ao Cliente do SQL Server sobre a migração usando arquivos BACPAC, confira [Migrating from SQL Server to Azure SQL Database using BACPAC Files](https://blogs.msdn.microsoft.com/sqlcat/2016/10/20/migrating-from-sql-server-to-azure-sql-database-using-bacpac-files/) (Migrando do SQL Server para o Banco de Dados SQL do Azure usando arquivos BACPAC).
* Para ver uma discussão de todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png



<!--HONumber=Feb17_HO2-->


