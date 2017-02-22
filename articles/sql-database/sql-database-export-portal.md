---
title: 'Portal do Azure: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC | Microsoft Docs'
description: Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o Portal do Azure
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.date: 02/07/2017
ms.author: carlrab
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 3d04be3d2427bc59d24bfaad227730991b61265b
ms.openlocfilehash: ed983134e468a69c0e89387a3211479630a278e3


---
# <a name="export-an-azure-sql-database-to-a-bacpac-file-using-the-azure-portal"></a>Exportar um Banco de Dados SQL do Azure para um arquivo BACPAC usando o portal do Azure

Este artigo fornece instruções para exportar seu Banco de Dados SQL Azure para um arquivo BACPAC (armazenado no armazenamento de blobs do Azure) usando o [portal do Azure](https://portal.azure.com). Para obter uma visão geral de como exportar para um arquivo BACPAC, confira [Exportar para um BACPAC](sql-database-export.md).

> [!NOTE]
> Você também pode exportar o arquivo de Banco de Dados SQL do Azure para um arquivo BACPAC usando o [SQL Server Management Studio](sql-database-export-ssms.md), o [PowerShell](sql-database-export-powershell.md) ou o [SQLPackage](sql-database-export-sqlpackage.md).
>

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este artigo, você precisa do seguinte:

* Uma assinatura do Azure.
* Um banco de dados SQL Azure. 
* Uma [conta de Armazenamento Standard do Azure](../storage/storage-create-storage-account.md) com um contêiner de blob para armazenar o BACPAC no armazenamento standard.

## <a name="export-your-database"></a>Exportar o banco de dados

1. Vá para o [Portal do Azure](https://portal.azure.com).
2. Abra a folha do Banco de Dados SQL do banco de dados que você deseja exportar.
3. Certifique-se de que nenhuma transação ocorrerá durante a exportação. 

   > [!IMPORTANT]
   > Para assegurar um arquivo BACPAC com consistência transacional, um bom método é [criar uma cópia do banco de dados](sql-database-copy.md) e, em seguida, exportar dessa cópia. 
   > 

4. Clique em **Bancos de dados SQL**.
5. Clique no banco de dados para arquivar.
6. Na folha Banco de Dados SQL, clique em **Exportar** para abrir a folha **Exportar banco de dados**:
   
   ![botão exportar][1]
7. Clique em **Armazenamento** e selecione a conta de armazenamento e o contêiner de blob para armazenar o BACPAC:
   
   ![Exportar banco de dados][2]
8. Selecione seu tipo de autenticação. 
9. Insira as credenciais de autenticação apropriadas para o SQL Server do Azure contendo o banco de dados que está sendo exportado.
10. Clique em **OK** para exportar o banco de dados. Clicar em **OK** cria uma solicitação de exportação de banco de dados e a envia ao serviço. O tempo que leva a exportação depende do tamanho e da complexidade do seu banco de dados e seu nível de serviço. 
11. Exibir a notificação recebida.
   
   ![exportar notificação][3]

## <a name="monitor-the-progress-of-the-export-operation"></a>Monitorar o progresso da operação de exportação
1. Clique em **Servidores SQL**.
2. Clique no servidor que contém o banco de dados original (origem) que você arquivou.
3. Role para baixo até Operações.
4. Na folha SQL Server, clique em **Histórico de importação/exportação**:
   
   ![histórico de importação exportação][4]

## <a name="verify-the-bacpac-is-in-your-storage-container"></a>Verificar se o BACPAC está no seu contêiner de armazenamento
1. Clique em **Contas de armazenamento**.
2. Clique na conta de armazenamento em que você armazenou o arquivo morto BACPAC.
3. Clique em **Contêineres** e selecione o contêiner para o qual você exportou o banco de dados a fim de obter detalhes (aqui é possível baixar e salvar o BACPAC).
   
   ![detalhes do arquivo .bacpac][5]    

## <a name="next-steps"></a>Próximas etapas

* Para saber mais sobre retenção de backup de longo prazo de um backup do Banco de Dados SQL do Azure como uma alternativa para a exportação de um banco de dados para fins de arquivamento, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md)
* Para saber mais sobre como importar um BACPAC para um banco de dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)

<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-notification.png
[4]: ./media/sql-database-export/export-history.png
[5]: ./media/sql-database-export/bacpac-archive.png




<!--HONumber=Feb17_HO2-->


