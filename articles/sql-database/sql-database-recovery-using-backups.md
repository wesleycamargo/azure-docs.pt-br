---
title: Restaurar um banco de dados SQL do Azure de um backup | Microsoft Docs
description: "Saiba mais sobre a Restauração Pontual, que permite que você reverta um Banco de Dados SQL do Azure para um ponto anterior no tempo (até 35 dias)."
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: monicar
ms.assetid: fd1d334d-a035-4a55-9446-d1cf750d9cf7
ms.service: sql-database
ms.custom: business continuity
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/20/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 751e1eaf65da889ab5c7dc26145c017682a12a4d
ms.lasthandoff: 03/28/2017


---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar um banco de dados SQL do Azure usando backups de banco de dados automatizados
O Banco de Dados SQL fornece três opções para recuperação de banco de dados usando os [backups automáticos de banco de dados](sql-database-automated-backups.md). Você pode restaurar um banco de dados durante o [período de retenção](sql-database-service-tiers.md) do backup do banco de dados para:

* Um novo banco de dados no mesmo servidor lógico recuperado para um ponto específico no tempo durante o período de retenção. 
* Um banco de dados no mesmo servidor lógico recuperado para o tempo de exclusão de um banco de dados excluído.
* Um novo banco de dados em qualquer servidor lógico em qualquer região recuperado para os backups diários mais recentes no armazenamento de blobs com replicação geográfica (RA-GRS).

> [!TIP]
> Para ver um tutorial, consulte [Introdução ao backup e restauração para proteção e recuperação dos dados](sql-database-get-started-backup-recovery-portal.md)
>

Você também pode usar [os backups de banco de dados automatizados](sql-database-automated-backups.md) para criar uma [cópia de banco de dados](sql-database-copy.md) em qualquer servidor lógico em qualquer região. Você também pode configurar um servidor de Banco de Dados SQL para armazenar os backups de Bancos de Dados SQL em um cofre dos Serviços de Recuperação do Azure por até 10 anos. Consulte [Retenção de backup de longo prazo](sql-database-long-term-retention.md).

## <a name="recovery-time"></a>Tempo de recuperação
O tempo de recuperação para restaurar um banco de dados usando backups de banco de dados automatizado é afetado por vários fatores: 

* O tamanho do banco de dados
* O nível de desempenho do banco de dados
* O número de logs de transações envolvidos
* A quantidade de atividade que precisa ser repetida para recuperar até o ponto de restauração
* A largura de banda de rede se a restauração for para uma região diferente 
* O número de solicitações simultâneas de restauração que estão sendo processadas na região de destino. 
  
  Para um banco de dados muito grande e/ou ativo, a restauração pode levar várias horas. Se houver uma interrupção prolongada em uma região, é possível que haja muitas solicitações de restauração geográfica sendo processadas por outras regiões. Quando houver muitas solicitações, o tempo de recuperação dos bancos de dados nessa região poderá aumentar. A maioria das restaurações de banco de dados é concluída em 12 horas.
  
  Não há nenhuma funcionalidade interna para restauração em massa. O [banco de dados SQL do Azure: o script recuperação de servidor completa](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) é um exemplo de uma maneira de realizar essa tarefa.

> [!IMPORTANT]
> Para recuperar usando backups automatizados, você deverá ser um membro da função Contribuidor do SQL Server na assinatura ou o proprietário da assinatura. Você pode recuperar usando o Portal do Azure, o PowerShell ou a API REST. Você não pode usar o Transact-SQL. 
> 

## <a name="point-in-time-restore"></a>Restauração pontual

Você pode restaurar um banco de dados existente para um ponto anterior no tempo como um novo banco de dados no mesmo servidor lógico usando o [Portal do Azure](sql-database-point-in-time-restore-portal.md), o [PowerShell](scripts/sql-database-restore-database-powershell.md) ou a [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!IMPORTANT]
> Não é possível substituir o banco de dados existente durante a restauração.
>

O banco de dados pode ser restaurado para qualquer nível de desempenho ou de camada de serviço e como um banco de dados individual ou em um pool elástico. Certifique-se de ter recursos suficientes no servidor lógico ou no pool elástico para o qual você está restaurando o banco de dados. Uma vez concluído, o banco de dados restaurado é um banco de dados online normal e totalmente acessível. O banco de dados restaurado é cobrado a taxas normais com base em seu nível de desempenho e na camada de serviço. Você não incorrerá encargos até que a restauração do banco de dados seja concluída.

Um banco de dados geralmente é restaurado para um ponto anterior para fins de recuperação. Ao fazê-lo, você poderá tratar o banco de dados restaurado como um substituto do banco de dados original ou usá-lo para recuperar os dados e, em seguida, atualizar o banco de dados original. 

* ***Substituição de banco de dados:*** se o banco de dados restaurado se destinar a ser um substituto do banco de dados original, você deverá verificar se o nível de desempenho e/ou a camada de serviço são adequados e escalar o banco de dados, se necessário. Você pode renomear o banco de dados original e, em seguida, dar ao banco de dados restaurado o nome original usando o comando ALTER DATABASE no T-SQL. 
* ***Recuperação de dados:*** se planejar recuperar dados do banco de dados restaurado para se recuperar de um erro de aplicativo ou de usuário, você precisará escrever e executar os scripts de recuperação de dados necessários para extrair dados do banco de dados restaurado para o banco de dados original. Embora a operação de restauração possa demorar muito para concluir, o banco de dados em restauração é visível na lista de banco de dados por todo o processo de restauração. Se você excluir o banco de dados durante a restauração, a operação de restauração será cancelada e você não será cobrado pelo banco de dados cuja restauração não foi concluída. 

## <a name="deleted-database-restore"></a>Restauração de banco de dados excluído
Você pode restaurar um banco de dados excluído para o momento de exclusão para um banco de dados excluído no mesmo servidor lógico usando o [Portal do Azure](sql-database-restore-deleted-database-portal.md), o [PowerShell](scripts/sql-database-restore-database-powershell.md) ou o [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx). 

> [!IMPORTANT]
> Se você excluir uma instância de servidor de Banco de Dados SQL do Azure, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados. No momento, não há suporte para restaurar um servidor excluído.
> 

## <a name="geo-restore"></a>Restauração geográfica
Você pode restaurar um Banco de Dados SQL em qualquer servidor e em qualquer região do Azure nos backups diferenciais e completos com replicação geográfica mais recentes. A restauração geográfica usará um backup com redundância geográfica como sua fonte, e ele poderá ser usado para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter estiver inacessível devido a uma interrupção. Você pode usar o [Portal do Azure](sql-database-geo-restore-portal.md), o [PowerShell](sql-database-geo-restore-powershell.md) ou o [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) 

A restauração geográfica será a opção de recuperação padrão quando seu banco de dados não estiver disponível devido a um incidente na região em que ele está hospedado. Se um incidente de grande escala em uma região resultar na indisponibilidade do seu aplicativo de banco de dados, você poderá restaurar um banco de dados do backup com replicação geográfica para um servidor em qualquer outra região. Há um atraso entre quando é um backup diferencial é realizado e quando ele é replicado geograficamente para um blob do Azure em uma região diferente. Esse atraso pode ser de até uma hora, então, em caso de desastre pode haver perda de dados de até uma hora. A ilustração a seguir mostra a restauração do banco de dados desde o último backup disponível em outra região.

![Restauração geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

Para obter informações detalhadas sobre como usar a Restauração geográfica para se recuperar de uma interrupção, consulte [Recuperação de uma interrupção](sql-database-disaster-recovery.md)

> [!IMPORTANT]
> A recuperação de backups é a mais básica dentre as soluções de recuperação de desastre disponíveis no Banco de Dados SQL com RPO e ERT (Tempo de Recuperação Estimado) mais longos. Para bancos de dados Básicos com tamanho máximo de 2 GB, a restauração geográfica fornece uma solução de DR razoável com um ERT de 12 horas. Para bancos de dados Standard ou Premium maiores, se desejar obter tempos de recuperação menores ou reduzir a probabilidade de perda de dados, considere usar a Replicação Geográfica Ativa. A Replicação Geográfica Ativa oferece um RPO e um ERT muito menores, pois exige somente que você inicie um failover para um secundário replicado continuamente. Para obter detalhes, consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md).
> 

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Executar recuperação programaticamente usando backups automatizados
Conforme discutido anteriormente, além do Portal do Azure, a recuperação de banco de dados pode ser realizada programaticamente usando o Azure PowerShell ou a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell
| Cmdlet | Descrição |
| --- | --- |
| [Get-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt603648.aspx) |Obtém um ou mais bancos de dados. |
| [Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/library/azure/mt693387.aspx) |Obtém um banco de dados excluído que você pode restaurar. |
| [Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx) |Obtém um backup com redundância geográfica de um banco de dados. |
| [Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx) |Restaura um banco de dados SQL. |
|  | |

### <a name="rest-api"></a>API REST
| API | Descrição |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Restaura um banco de dados |
| [Obter, Criar ou Atualizar o Status de um Banco de Dados](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Retorna o status durante uma operação de restauração |
|  | |

## <a name="summary"></a>Resumo
Backups automáticos protegem seus bancos de dados contra erros de usuário e de aplicativo, exclusão acidental do banco de dados e interrupções prolongadas. Essa funcionalidade interna está disponível para todas as camadas de serviço e níveis de desempenho. 

## <a name="next-steps"></a>Próximas etapas
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
* Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, consulte [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
* Para saber mais sobre a retenção de backup de longo prazo, consulte [Retenção de backup de longo prazo](sql-database-long-term-retention.md)
* Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure usando o Portal do Azure, consulte [Gerenciar retenção de backup de longo prazo usando o Portal do Azure](sql-database-manage-long-term-backup-retention-portal.md). 
* Para configurar, gerenciar e restaurar de retenção de longo prazo de backups automatizados em um cofre dos Serviços de Recuperação do Azure usando o PowerShell, consulte [Gerenciar retenção de backup de longo prazo usando o PowerShell](sql-database-manage-long-term-backup-retention-powershell.md).
* Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)  
* Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)

