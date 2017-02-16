---
title: "Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o portal do Azure | Microsoft Docs"
description: "Configurar a replicação geográfica para o Banco de Dados SQL do Azure usando o portal do Azure"
services: sql-database
documentationcenter: 
author: anosov1960
manager: jhubbard
editor: 
ms.assetid: d0b29822-714f-4633-a5ab-fb1a09d43ced
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/22/2016
ms.author: sashan;carlrab
translationtype: Human Translation
ms.sourcegitcommit: afcdae9ae0224e12ad874b389dad9882d9269fdf
ms.openlocfilehash: e3d4b3f7a628a058e7b761788a3f63a57228f39c


---
# <a name="configure-geo-replication-for-azure-sql-database-with-the-azure-portal"></a>Configurar a replicação geográfica para o Banco de Dados SQL do Azure com o portal do Azure
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-geo-replication-overview.md)
> * [Portal do Azure](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

Este artigo mostra como configurar a replicação geográfica ativa para o Banco de Dados SQL com o [portal do Azure](http://portal.azure.com).

Para iniciar um failover com o portal do Azure, veja [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o portal do Azure](sql-database-geo-replication-failover-portal.md).

> [!NOTE]
> A replicação geográfica ativa (secundários legíveis) agora está disponível para todos os bancos de dados em todas as camadas de serviço. Em abril de 2017, o tipo de secundário não legível será descontinuado, e os bancos de dados não legíveis existentes serão automaticamente atualizados para secundários legíveis.
> 
> 

Para configurar a replicação geográfica usando o portal do Azure, você precisa do seguinte recurso:

* Um Banco de Dados SQL do Azure: o banco de dados primário que você deseja replicar para uma região geográfica diferente.

> [!Note]
A replicação geográfica ativa deve estar entre bancos de dados da mesma assinatura.

## <a name="add-a-secondary-database"></a>Adicionar um banco de dados secundário
As etapas a seguir criam um novo banco de dados secundário em uma parceria de replicação geográfica.  

Para adicionar um banco de dados secundário, você deve ser o proprietário ou coproprietário da assinatura.

O banco de dados secundário tem o mesmo nome do banco de dados primário e, por padrão, tem o mesmo nível de serviço. O banco de dados secundário pode ser um banco de dados individual ou um banco de dados elástico. Para obter mais informações, consulte [Camadas de serviço](sql-database-service-tiers.md).
Depois que o banco de dados secundário for criado e propagado, os dados começarão a serem replicados desde o banco de dados primário até o novo banco de dados secundário.

> [!NOTE]
> O comando falhará se o banco de dados do parceiro já existir (por exemplo, como resultado do término de uma relação de replicação geográfica anterior).
> 
> 

### <a name="add-secondary"></a>Adicionar secundário
1. No [portal do Azure](http://portal.azure.com), navegue até o banco de dados que você deseja configurar para a replicação geográfica.
2. Na página de banco de dados SQL, selecione **Replicação Geográfica** e, em seguida, selecione a região para criar o banco de dados secundário. É possível selecionar qualquer região além da região que hospeda o banco de dados primário, mas recomendamos a [região emparelhada](../best-practices-availability-paired-regions.md).
   
    ![Configurar a replicação geográfica](./media/sql-database-geo-replication-portal/configure-geo-replication.png)
3. Selecione ou configure o servidor e o tipo de preço do banco de dados secundário.
   
    ![Configurar secundário](./media/sql-database-geo-replication-portal/create-secondary.png)
4. Opcionalmente, você pode adicionar um banco de dados secundário a um pool de banco de dados elástico. Para criar o banco de dados secundário em um pool, clique em **Pool de banco de dados elástico** e selecione um pool no servidor de destino. Um pool já deve existir no servidor de destino. Esse fluxo de trabalho não cria um pool.
5. Clique em **Criar** para adicionar o secundário.
6. O banco de dados secundário é criado e começa o processo de propagação.
   
    ![Configurar secundário](./media/sql-database-geo-replication-portal/seeding0.png)
7. Quando o processo de propagação for concluído, o banco de dados secundário exibirá seu status.
   
    ![Propagação concluída](./media/sql-database-geo-replication-portal/seeding-complete.png)

## <a name="remove-secondary-database"></a>Remover banco de dados secundário
Essa operação termina permanentemente a replicação para o banco de dados secundário e altera a função do secundário para um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for desfeita, o comando terá êxito, mas o secundário só se tornará de leitura/gravação quando a conectividade for restaurada.  

1. No [portal do Azure](http://portal.azure.com), navegue até o banco de dados primário na parceria de replicação geográfica.
2. Na página do Banco de Dados SQL, selecione **Replicação geográfica**.
3. Na lista **SECUNDÁRIOS**, selecione o banco de dados que você deseja remover da parceria de replicação geográfica.
4. Clique em **Parar Replicação**.
   
    ![Remover secundário](./media/sql-database-geo-replication-portal/remove-secondary.png)
5. Uma janela de confirmação é aberta. Clique em **Sim** para remover o banco de dados da parceria de replicação geográfica. (Defina-o como um banco de dados de leitura/gravação que não faz parte de nenhuma replicação.)

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a replicação geográfica ativa, consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md).
* Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).




<!--HONumber=Nov16_HO4-->


