---
title: "Portal do Azure: Alterar o tipo de preço do Banco de Dados SQL do Azure | Microsoft Docs"
description: "Saiba como usar o Portal do Azure para alterar a camada de serviço e o nível de desempenho de um banco de dados SQL do Azure, bem como para escalar verticalmente para cima e para baixo os recursos e o preço do banco de dados SQL."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cbd67e88-08d5-40e2-a223-0fb0c718a782
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 88104e0684281f77da497ef4dd608c6a0c64b274


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Alterar a camada de serviços e o nível de desempenho (tipo de preço) de um Banco de Dados SQL com o Portal do Azure
> [!div class="op_single_selector"]
> * [**Portal do Azure**](sql-database-scale-up.md)
> * [PowerShell](sql-database-scale-up-powershell.md)> 
> 

As camadas de serviço e níveis de desempenho descrevem as características e recursos disponíveis para o banco de dados SQL e podem ser atualizados conforme as necessidades do seu aplicativo mudam. Para obter detalhes, consulte [Camadas de serviço](sql-database-service-tiers.md).

Observe que a alteração da camada de serviço e/ou nível de desempenho de um banco de dados cria uma réplica do banco de dados original com o novo nível de desempenho e então faz a transição das conexões para réplica. Nenhum dado é perdido durante esse processo, mas durante o breve momento em que realizamos a transição para a réplica, conexões com o banco de dados são desabilitadas, então algumas transações em andamento podem ser revertidas. Essa janela varia, mas é em média inferior a quatro segundos e, em mais de 99% dos casos, de menos de 30 segundos. Muito raramente, especialmente se houver grandes números de transações em andamento no momento em que as conexões estiverem desabilitadas, esta janela poderá ser maior.  

A duração de todo o processo de escala vertical levará menos de 5 minutos na maioria dos casos. 

Use as informações em [Camadas de serviço e níveis de desempenho do Banco de Dados SQL do Azure](sql-database-service-tiers.md) para determinar o nível de desempenho e a camada de serviço apropriados para o Banco de Dados SQL do Azure.

* Para fazer downgrade de um banco de dados, este deve ter um tamanho menor do que o máximo permitido para a camada de serviço de destino. 
* Ao atualizar um banco de dados com a [Replicação Geográfica](sql-database-geo-replication-overview.md) habilitada, é necessário primeiro atualizar seus bancos de dados secundários para o nível de desempenho desejado antes de atualizar o banco de dados primário.
* Ao fazer downgrade de uma camada de serviço, primeiro você deve encerrar todos os relacionamentos de Replicação Geográfica. 
* As ofertas de serviço de restauração são diferentes para as várias camadas de serviço. Se estiver fazendo downgrade, talvez você perca a capacidade de fazer uma restauração pontual ou tenha um período menor de retenção do backup. Para saber mais, confira [Backup e restauração do Banco de dados SQL do Azure](sql-database-business-continuity.md).
* Alterar o tipo de preço do banco de dados não altera o tamanho máximo do banco de dados. Para alterar o tamanho máximo do banco de dados, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* As novas propriedades do banco de dados não serão aplicadas até que as alterações sejam concluídas.

**Para concluir este artigo, você precisa do seguinte:**

* Um banco de dados SQL do Azure. Se você não tiver um banco de dados SQL, crie um executando as etapas neste artigo: [Criar seu primeiro Banco de Dados SQL do Azure](sql-database-get-started.md).

## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Alterar a camada de serviço e o nível de desempenho do banco de dados
Abra a folha Banco de Dados SQL do banco de dados que você deseja escalar verticalmente:

1. No [Portal do Azure](https://portal.azure.com), clique em **Mais serviços** > **Bancos de Dados SQL**.
2. Clique no banco de dados que você deseja alterar.
3. Em seguida, na folha do **Banco de Dados SQL**, clique em **Tipo de preço (DTUs em escala)**:
   
   ![Tipo de preço][1]
4. Selecione uma nova camada e clique em **Selecionar**:
   
   Clicar em **Selecionar** envia uma solicitação de escala para alterar o tipo de preço. Dependendo do tamanho do banco de dados, a operação de escala poderá demorar um pouco para ser concluída (consulte as informações no início deste artigo).
   
   > [!NOTE]
   > Alterar o tipo de preço do banco de dados não altera o tamanho máximo do banco de dados. Para alterar o tamanho máximo do banco de dados, use [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   > 
   > 
   
   ![selecionar camada de preços][2]
5. Clique no ícone de notificação (sino), no canto superior direito:
   
   ![Notificações][3]
   
   Clique no texto da notificação para abrir o painel de detalhes, onde você poderá ver o status da solicitação.

## <a name="next-steps"></a>Próximas etapas
* Altere o tamanho máximo do banco de dados usando [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* [Escalar horizontalmente](sql-database-elastic-scale-get-started.md)
* [Conectar-se a e consultar um banco de dados SQL com SSMS](sql-database-connect-query-ssms.md)
* [Exportar um banco de dados SQL do Azure](sql-database-export.md)

## <a name="additional-resources"></a>Recursos adicionais
* [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* [Documentação do banco de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png



<!--HONumber=Jan17_HO1-->


