---
title: Restaurar um banco de dados SQL do Azure de um backup | Microsoft Docs
description: Saiba mais sobre a Restauração Pontual, que permite que você reverta um Banco de Dados SQL do Azure para um ponto anterior no tempo (até 35 dias).
services: sql-database
ms.service: sql-database
ms.subservice: operations
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: anosov1960
ms.author: sashan
ms.reviewer: carlrab
manager: craigg
ms.date: 10/08/2018
ms.openlocfilehash: ad82ae6158bbf343dd84be125a8839e992ad3634
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 10/08/2018
ms.locfileid: "48868145"
---
# <a name="recover-an-azure-sql-database-using-automated-database-backups"></a>Recuperar um banco de dados SQL do Azure usando backups de banco de dados automatizados

Por padrão, os backups do Banco de Dados SQL são armazenados no armazenamento de blobs com replicação geográfica (RA-GRS). As opções a seguir estão disponíveis para recuperação do banco de dados usando [backups de banco de dados automatizados](sql-database-automated-backups.md):

- Crie um novo banco de dados no mesmo servidor lógico recuperado para um ponto especificado no tempo dentro do período de retenção.
- Crie um banco de dados no mesmo servidor lógico recuperado para o tempo de exclusão de um banco de dados excluído.
- Crie um novo banco de dados em qualquer servidor lógico em qualquer região recuperada até o ponto dos backups mais recentes.

Se você configurou a [retenção de backup de longo prazo](sql-database-long-term-retention.md) também poderá criar um novo banco de dados a partir de qualquer backup de LTR em qualquer servidor lógico em qualquer região.  

> [!IMPORTANT]
> Não é possível substituir um banco de dados existente durante a restauração.

Ao usar a camada de serviço Standard ou Premium, um banco de dados restaurado incorre em um custo de armazenamento adicional nas seguintes condições:

- Restauração de P11 – P15 para S4 ou S12 ou P1– P6 se o tamanho máximo do banco de dados for superior a 500 GB.
- Restauração de P1–P6 para S4-S12 se o tamanho máximo do banco de dados for superior a 250 GB.

O custo extra ocorre porque o tamanho máximo do banco de dados restaurado é maior do que a quantidade de armazenamento incluída para o tamanho de computação, e o armazenamento extra provisionado acima da quantidade incluída recebe uma cobrança extra.  Para obter detalhes de preço do armazenamento extra, confira a página [Preços do Banco de Dados SQL](https://azure.microsoft.com/pricing/details/sql-database/).  Se a quantidade real de espaço usado for menor do que a quantidade de armazenamento incluída, esse custo extra poderá ser evitado por meio da redução do tamanho máximo do banco de dados para a quantidade incluída.  

> [!NOTE]
> [Backups de banco de dados automatizados](sql-database-automated-backups.md) são usadas quando você cria uma [cópia de banco de dados](sql-database-copy.md).

## <a name="recovery-time"></a>Tempo de recuperação

O tempo de recuperação para restaurar um banco de dados usando backups de banco de dados automatizado é afetado por vários fatores:

- O tamanho do banco de dados
- O tamanho de computação do banco de dados
- O número de logs de transações envolvidos
- A quantidade de atividade que precisa ser repetida para recuperar até o ponto de restauração
- A largura de banda de rede se a restauração for para uma região diferente
- O número de solicitações de restauração simultâneas sendo processadas na região de destino
  
Para um banco de dados muito grande e/ou ativo, a restauração pode levar várias horas. Caso haja uma interrupção prolongada em uma região, é possível que haja muitas solicitações de restauração geográfica sendo processadas por outras regiões. Quando houver muitas solicitações, o tempo de recuperação dos bancos de dados nessa região poderá aumentar. A maioria das restaurações de banco de dados é concluída em 12 horas.

Para uma assinatura única, há algumas limitações no número de solicitações simultâneas de restauração (incluindo restauração de ponto no tempo, restauração geográfica e restauração do backup de retenção de longo prazo) que estão sendo enviadas e prosseguidas:

|  | **Número máximo de solicitações simultâneas que estão sendo processadas** | **Número máximo de solicitações simultâneas que estão sendo enviadas** |
| :--- | --: | --: |
|Banco de dados único (por assinatura)|10|60|
|Pool Elástico (por pool)|4|200|
||||

Não há nenhuma funcionalidade interna para restauração em massa. O [banco de dados SQL do Azure: o script recuperação de servidor completa](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) é um exemplo de uma maneira de realizar essa tarefa.

> [!IMPORTANT]
> Para recuperar usando backups automatizados, você deverá ser um membro da função Contribuidor do SQL Server na assinatura ou o proprietário da assinatura - consulte [RBAC: funções internas](../role-based-access-control/built-in-roles.md). Você pode recuperar usando o Portal do Azure, o PowerShell ou a API REST. Você não pode usar o Transact-SQL.

## <a name="point-in-time-restore"></a>Restauração pontual

Você pode restaurar um banco de dados existente para um ponto anterior no tempo como um novo banco de dados no mesmo servidor lógico usando o portal do Azure, o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase) ou a [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [!TIP]
> Para obter um script de exemplo do PowerShell que mostra como executar uma recuperação pontual de um banco de dados, consulte [Restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).

O banco de dados pode ser restaurado para qualquer tamanho de computação ou camada de serviço e como um banco de dados individual ou em um pool elástico. Certifique-se de ter recursos suficientes no servidor lógico ou no pool elástico para o qual você está restaurando o banco de dados. Uma vez concluído, o banco de dados restaurado é um banco de dados online normal e totalmente acessível. O banco de dados restaurado é cobrado a taxas normais com base em seu tamanho de computação e na camada de serviço. Você não incorrerá encargos até que a restauração do banco de dados seja concluída.

Um banco de dados geralmente é restaurado para um ponto anterior para fins de recuperação. Ao fazê-lo, você poderá tratar o banco de dados restaurado como um substituto do banco de dados original ou usá-lo para recuperar os dados e, em seguida, atualizar o banco de dados original.

- **Substituição de banco de dados**

   Se o banco de dados restaurado for destinado a substituir o banco de dados original, verifique se o tamanho do computador e/ou camada de serviço são apropriados e dimensione o banco de dados, se necessário. Você pode renomear o banco de dados original e, em seguida, dar ao banco de dados restaurado o nome original usando o comando [ALTER DATABASE](/sql/t-sql/statements/alter-database-azure-sql-database) no T-SQL.

- **Recuperação de dados**

   Se você planeja recuperar dados do banco de dados restaurado para recuperar-se de um erro de usuário ou aplicativo, é necessário gravar e executar os scripts de recuperação de dados necessários para extrair dados do banco de dados restaurado para o banco de dados original. Embora a operação de restauração possa demorar muito para concluir, o banco de dados em restauração é visível na lista de banco de dados por todo o processo de restauração. Se você excluir o banco de dados durante a restauração, a operação de restauração será cancelada e você não será cobrado pelo banco de dados cuja restauração não foi concluída.

### <a name="recover-to-a-point-in-time-using-azure-portal"></a>Recuperar para um ponto no tempo usando o portal do Azure

Para recuperar para um ponto no tempo usando o Portal do Azure, abra a página do banco de dados e clique em **Restaurar** na barra de ferramentas.

![restauração pontual](./media/sql-database-recovery-using-backups/point-in-time-recovery.png)

## <a name="deleted-database-restore"></a>Restauração de banco de dados excluído

Você pode restaurar um banco de dados excluído para o momento da exclusão de um banco de dados excluído no mesmo servidor lógico usando o Portal do Azure, o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase) ou o [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx). Você pode restaurar um banco de dados excluído em um ponto anterior durante a retenção usando o [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.sql/restore-azurermsqldatabase).

> [!Note]
> A restauração do banco de dados excluído não está disponível na Instância Gerenciada.
> [!TIP]
> Para obter um script de exemplo do PowerShell que mostra como restaurar um banco de dados excluído, consulte [Restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).
> [!IMPORTANT]
> Se você excluir uma instância de servidor de Banco de Dados SQL do Azure, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados. No momento, não há suporte para restaurar um servidor excluído.

### <a name="recover-a-deleted-database-using-the-azure-portal"></a>Recuperar um banco de dados excluído usando o portal do Azure

Para recuperar um banco de dados excluído durante seu [período de retenção do modelo com base em DTU](sql-database-service-tiers-dtu.md) ou [período de retenção do modelo com base em vCore](sql-database-service-tiers-vcore.md) usando o portal do Azure, abra a página do seu servidor e, na área de Operações, clique em **Bancos de dados excluídos**.

![deleted-database-restore-1](./media/sql-database-recovery-using-backups/deleted-database-restore-1.png)

![deleted-database-restore-2](./media/sql-database-recovery-using-backups/deleted-database-restore-2.png)

## <a name="geo-restore"></a>Restauração geográfica

Você pode restaurar um Banco de Dados SQL em qualquer servidor e em qualquer região do Azure nos backups diferenciais e completos com replicação geográfica mais recentes. A restauração geográfica usa um backup com redundância geográfica como sua fonte e pode ser usada para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter está inacessível devido a uma interrupção.

> [!Note]
> A restauração geográfica não está disponível na Instância Gerenciada.

A restauração geográfica é a opção de recuperação padrão quando seu banco de dados não estiver disponível devido a um incidente na região em que ele está hospedado. Se um incidente de grande escala em uma região resultar na indisponibilidade do seu aplicativo de banco de dados, você poderá restaurar um banco de dados do backup com replicação geográfica para um servidor em qualquer outra região. Há um atraso entre quando é um backup diferencial é realizado e quando ele é replicado geograficamente para um blob do Azure em uma região diferente. Esse atraso pode ser de até uma hora, então, em caso de desastre pode haver perda de dados de até uma hora. A ilustração a seguir mostra a restauração do banco de dados desde o último backup disponível em outra região.

![Restauração geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

> [!TIP]
> Para obter um script de exemplo do PowerShell que mostra como executar uma restauração geográfica, consulte [Restaurar um banco de dados SQL usando o PowerShell](scripts/sql-database-restore-database-powershell.md).

Atualmente, não há suporte para a restauração pontual em uma área geográfica secundária. A restauração pontual pode ser feita somente em um banco de dados primário. Para obter informações detalhadas sobre como usar a restauração geográfica para se recuperar de uma interrupção, consulte [Recuperação de uma interrupção](sql-database-disaster-recovery.md).

> [!IMPORTANT]
> A recuperação de backups é a mais básica das soluções de recuperação de desastre disponíveis no Banco de Dados SQL com o RPO (Objetivo de Ponto de Recuperação) e ERT (Tempo de Recuperação Estimado) mais longos. Para soluções que usam bancos de dados pequenos (por exemplo, camada de serviço Básico ou bancos de dados de locatário pequeno em pools elásticos), a restauração geográfica frequentemente é uma solução DR razoável com um ERT de 12 horas. Para soluções que usam bancos de dados grandes e exigem tempos de recuperação mais curtos, considere usar [Grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md). A replicação geográfica ativa oferece um RPO e um ERT muito menores, pois exige somente que você inicie um failover para um secundário replicado continuamente. Para obter mais informações sobre as opções de continuidade dos negócios, consulte [Visão geral de continuidade de negócios](sql-database-business-continuity.md).

### <a name="geo-restore-using-the-azure-portal"></a>Restauração geográfica usando o portal do Azure

Para a restauração geográfica de um banco de dados durante seu [período de retenção do modelo com base em DTU](sql-database-service-tiers-dtu.md) ou [período de retenção do modelo com base em vCore](sql-database-service-tiers-vcore.md) usando o portal do Azure, abra a página do Banco de Dados SQL e clique em **Adicionar**. Na caixa de texto **Selecionar fonte**, selecione **Backup**. Especifique o backup do qual fazer a recuperação, na região e no servidor de sua escolha.

## <a name="programmatically-performing-recovery-using-automated-backups"></a>Executar recuperação programaticamente usando backups automatizados

Conforme discutido anteriormente, além do Portal do Azure, a recuperação de banco de dados pode ser realizada programaticamente usando o Azure PowerShell ou a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### <a name="powershell"></a>PowerShell

| Cmdlet | DESCRIÇÃO |
| --- | --- |
| [Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase) |Obtém um ou mais bancos de dados. |
| [Get-AzureRMSqlDeletedDatabaseBackup](/powershell/module/azurerm.sql/get-azurermsqldeleteddatabasebackup) | Obtém um banco de dados excluído que você pode restaurar. |
| [Get-AzureRmSqlDatabaseGeoBackup](/powershell/module/azurerm.sql/get-azurermsqldatabasegeobackup) |Obtém um backup com redundância geográfica de um banco de dados. |
| [Restore-AzureRmSqlDatabase](/powershell/module/azurerm.sql/restore-azurermsqldatabase) |Restaura um banco de dados SQL. |
|  | |

### <a name="rest-api"></a>API REST

| API | DESCRIÇÃO |
| --- | --- |
| [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |Restaura um banco de dados |
| [Obter, Criar ou Atualizar o Status de um Banco de Dados](https://msdn.microsoft.com/library/azure/mt643934.aspx) |Retorna o status durante uma operação de restauração |
|  | |

## <a name="summary"></a>Resumo

Backups automáticos protegem seus bancos de dados contra erros de usuário e de aplicativo, exclusão acidental do banco de dados e interrupções prolongadas. Essa funcionalidade interna está disponível para todas as camadas de serviço e tamanhos de computação.

## <a name="next-steps"></a>Próximas etapas

- Para obter uma visão geral e os cenários de continuidade dos negócios, consulte [Visão geral da continuidade dos negócios](sql-database-business-continuity.md).
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).
- Para saber mais sobre retenção de longo prazo, consulte [Retenção de longo prazo](sql-database-long-term-retention.md).
- Para saber mais sobre as opções de recuperação mais rápida, consulte [Grupos de failover e replicação geográfica ativa](sql-database-geo-replication-overview.md).  
