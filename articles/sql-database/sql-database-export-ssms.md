---
title: 'SSMS: Exportar um banco de dados para um arquivo BACPAC (Azure) | Microsoft Docs'
description: Este artigo mostra como exportar um banco de dados SQL Server para um arquivo BACPAC usando o Assistente para Exportar Aplicativo da Camada de Dados no SQL Server Management Studio.
keywords: "Banco de Dados SQL do Microsoft Azure, migração do banco de dados, exportar banco de dados, exportar arquivo BACPAC, Assistente para Exportação de Aplicativo de Camada de Dados"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: 19c2dab4-81a6-411d-b08a-0ef79b90fbce
ms.service: sql-database
ms.custom: migrate and move
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: sqldb-migrate
ms.date: 02/07/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 3f0af43c103e34535fd114e33c40da010ea69d69
ms.openlocfilehash: 87d9ae3a5c9c8b8edb02e212f652de5f2f796188


---
# <a name="export-an-azure-sql-database-or-a-sql-server-database-to-a-bacpac-file-using-sql-server-management-studio"></a>Exportar um banco de dados SQL do Azure ou um Banco de Dados do SQL Server para um arquivo BACPAC usando o SQL Server Management Studio        
        
Este artigo mostra como exportar um Banco de Dados SQL do Azure ou um Banco de Dados do SQL Server para um arquivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) usando o Assistente para Exportação de Aplicativo de Camada de Dados no SQL Server Management Studio. Para obter uma visão geral de como exportar para um arquivo BACPAC, confira [Exportar para um BACPAC](sql-database-export.md).    
        
1. Verifique se você tem a versão mais recente do SQL Server Management Studio. As novas versões do Management Studio são atualizadas mensalmente para permanecerem em sincronia com as atualizações para o portal do Azure.        
           
   > [!IMPORTANT]        
   > Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290.aspx).        
   >         
         
 2. Abra o Management Studio e conecte-se ao banco de dados de origem no Pesquisador de Objetos.        
            
     ![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/MigrateUsingBACPAC01.png)        
 3. Clique com o botão direito do mouse no banco de dados de origem no Pesquisador de Objetos, aponte para **Tarefas** e clique em **Exportar Aplicativo da Camada de Dados...**        
            
     ![Exportar um aplicativo da camada de dados no menu de Tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)        
 4. No assistente de exportação, configure a exportação para salvar o arquivo BACPAC em um disco local ou em um blob do Azure. O BACPAC exportado sempre inclui o esquema de banco de dados completo e, por padrão, os dados de todas as tabelas. Se você quiser excluir dados de algumas ou de todas as tabelas, use a guia Avançado. Por exemplo, você pode optar por exportar somente os dados das tabelas de referência em vez de todas as tabelas.        
         
     ![Exportar configurações](./media/sql-database-cloud-migrate/MigrateUsingBACPAC02.png)        
         
    > [!IMPORTANT]        
    > Ao exportar um BACPAC para o armazenamento de blobs do Azure, use o armazenamento standard. Não há suporte para a importação de um BACPAC do armazenamento premium.        
    >        
            
## <a name="next-steps"></a>Próximas etapas        
* [Versão mais recente do SSMS](https://msdn.microsoft.com/library/mt238290.aspx)
* Para saber mais sobre como importar um BACPAC para um banco de dados do SQL Server, confira [Importar um BACPAC para um banco de dados do SQL Server](https://msdn.microsoft.com/library/hh710052.aspx)
* Para ver uma discussão de todo o processo de migração do banco de dados do SQL Server, incluindo as recomendações de desempenho, confira [Migrar um banco de dados do SQL Server para o Banco de Dados SQL do Azure](sql-database-cloud-migrate.md).
* Para saber mais sobre retenção de backup de longo prazo de um backup do Banco de Dados SQL do Azure como uma alternativa para a exportação de um banco de dados para fins de arquivamento, confira [Retenção de backup de longo prazo](sql-database-long-term-retention.md)


    



<!--HONumber=Feb17_HO2-->


