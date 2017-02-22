---
title: "Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o PowerShell | Microsoft Docs"
description: "Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure usando o PowerShell"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 5849b600-89cb-4995-ae9f-0188a17b4e1b
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: data-management
ms.date: 08/29/2016
ms.author: sstein
translationtype: Human Translation
ms.sourcegitcommit: 8d988aa55d053d28adcf29aeca749a7b18d56ed4
ms.openlocfilehash: ac575284544819c6bed7ef84669b2793085a3dc6


---
# <a name="initiate-a-planned-or-unplanned-failover-for-azure-sql-database-with-powershell"></a>Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o PowerShell

Este artigo mostra como iniciar um failover planejado ou não planejado para um Banco de Dados SQL com o PowerShell. Para configurar a Replicação Geográfica, consulte [Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure](sql-database-geo-replication-powershell.md).

## <a name="initiate-a-planned-failover"></a>Iniciar um failover planejado
Você pode usar o cmdlet **Set-AzureRmSqlDatabaseSecondary** com o parâmetro **-Failover** para promover um banco de dados secundário para que ele se torne o novo banco de dados primário de maneira planejada, rebaixando o primário existente para se tornar um secundário. Essa funcionalidade é designada para um failover planejado, como durante os exercícios de recuperação de desastres, e requer que o banco de dados primário esteja disponível.

O comando executa o seguinte fluxo de trabalho:

1. Alterne temporariamente a replicação para o modo síncrono. Isso fará com que todas as transações pendentes sejam liberadas para o secundário.
2. Alterne as funções dos dois bancos de dados na parceria de Replicação Geográfica.  

Essa sequência garante que os dois bancos de dados estejam sincronizados antes que as funções sejam alternadas, de forma que não ocorra nenhuma perda de dados. Há um breve período durante o qual os bancos de dados não estão disponíveis (na ordem de 0 a 25 segundos) enquanto as funções são alternadas. A operação inteira deve levar menos de um minuto para ser concluída em circunstâncias normais. Para saber mais, veja [Set-AzureRmSqlDatabaseSecondary](https://msdn.microsoft.com/library/mt619393\(v=azure.300\).aspx).

Esse cmdlet retornará quando o processo de alternar o banco de dados secundário para primário for concluído.

O comando a seguir alterna as funções do banco de dados chamado "mydb" no servidor "srv2" sob o grupo de recursos "rg2" para o primário. O primário original ao qual "db2" foi conectado será alternado para o secundário depois que os dois bancos de dados forem completamente sincronizados.

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg2” -ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover


> [!NOTE]
> Em situações raras, é possível que a operação não seja concluída e pareça não estar respondendo. Nesse caso, o usuário poderá executar o comando de failover à força e aceitar a perda de dados.
> 
> 

## <a name="initiate-an-unplanned-failover-from-the-primary-database-to-the-secondary-database"></a>Iniciar um failover não planejado do banco de dados primário para o banco de dados secundário
Você pode usar o cmdlet **Set-AzureRmSqlDatabaseSecondary** com os parâmetros **–Failover** e **-AllowDataLoss** a fim de promover um banco de dados secundário para que ele se torne o novo banco de dados primário de maneira não planejada, rebaixando o primário existente para se tornar um secundário quando o banco de dados primário não estiver mais disponível.

Essa funcionalidade foi designada para a recuperação de desastres quando a restauração da disponibilidade do banco de dados é fundamental e a perda de dados é aceitável. Quando o failover forçado é chamado, o banco de dados secundário especificado imediatamente se torna o banco de dados primário e começa a aceitar as transações de gravação. Assim que o banco de dados primário original for capaz de se reconectar com o novo banco de dados primário após a operação de failover forçado, um backup incremental será realizado no banco de dados primário original e o antigo banco de dados primário será transformado em um banco de dados secundário para o novo banco de dados primário; em seguida, será simplesmente uma réplica do novo primário.

Mas como a Restauração Pontual não tem suporte em bancos de dados secundários, se você quiser confirmar os dados de recuperação no banco de dados primário antigo que não tenham sido replicados para o novo banco de dados primário, deverá contar com o CSS para restaurar um banco de dados para o backup de log conhecido.

> [!NOTE]
> Se o comando for emitido quando o primário e o secundário estiverem online, o antigo primário se tornará o novo secundário, imediatamente e sem sincronização de dados. Se o primário estiver realizando transações quando o comando for emitido, alguma perda de dados poderá ocorrer.
> 
> 

Se o banco de dados primário tiver vários secundários, o comando será parcialmente bem-sucedido. O secundário no qual o comando foi executado se tornará o primário. O primário antigo, entretanto, permanecerá como primário, isto é, os dois primários terminarão em um estado inconsistente e conectados por um link de replicação suspensa. O usuário terá de reparar manualmente essa configuração usando uma API para "remover secundário" em qualquer um desses bancos de dados primários.

O comando a seguir alternará as funções de banco de dados chamado "mydb" para primário quando o primário não estiver disponível. O primário original ao qual “mydb” estava conectado será alternado para secundário quando voltar a ficar online. Nesse ponto, a sincronização poderá resultar em perda de dados.

    $database = Get-AzureRmSqlDatabase -DatabaseName "mydb" -ResourceGroupName "rg2” -ServerName "srv2”
    $database | Set-AzureRmSqlDatabaseSecondary -Failover -AllowDataLoss




## <a name="next-steps"></a>Próximas etapas
* Após o failover, verifique se os requisitos de autenticação para o servidor e o banco de dados estão configurados no novo primário. Para obter detalhes, consulte [Segurança do Banco de Dados SQL do Azure após a recuperação de desastre](sql-database-geo-replication-security-config.md).
* Para saber mais sobre a recuperação após um desastre usando a replicação geográfica ativa, incluindo as etapas anteriores e posteriores à recuperação e como executar uma análise de recuperação de desastre, confira [Disaster Recovery Drills](sql-database-disaster-recovery.md)
* Para ver uma postagem de blog de Sasha Nosov sobre a replicação geográfica ativa, confira [Spotlight on new Geo-Replication capabilities](https://azure.microsoft.com/blog/spotlight-on-new-capabilities-of-azure-sql-database-geo-replication/)
* Para obter informações sobre a criação de aplicativos de nuvem para usar a replicação geográfica ativa, confira [Projetando aplicativos de nuvem para continuidade de negócios usando a Replicação Geográfica](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
* Para obter informações sobre o uso da Replicação geográfica ativa com pools elásticos, consulte [Elastic Pool disaster recovery strategies](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md)(Estratégias de recuperação de desastre do pool elástico).
* Para obter uma visão geral sobre a continuidade dos negócios, confira [Visão geral de continuidade dos negócios](sql-database-business-continuity.md)




<!--HONumber=Feb17_HO3-->


