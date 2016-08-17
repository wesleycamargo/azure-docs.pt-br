<properties
   pageTitle="Continuidade de negócios em nuvem - Restaurar um banco de dados excluído - Banco de Dados SQL | Microsoft Azure"
   description="Saiba mais sobre a Restauração Pontual, que permite que você reverta um Banco de Dados SQL do Azure para um ponto anterior no tempo (até 35 dias)."
   services="sql-database"
   documentationCenter=""
   authors="stevestein"
   manager="jhubbard"
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/01/2016"
   ms.author="sstein"/>

# Recuperar um banco de dados SQL do Azure usando backups de banco de dados automatizados

O Banco de Dados SQL fornece três opções para recuperação de banco de dados usando [backups automáticos de Banco de Dados SQL](sql-database-automated-backups.md). Você pode restaurar um banco de dados dos backups iniciados pelo serviço durante seus [período de retenção](sql-database-service-tiers.md) para:

- Um novo banco de dados no mesmo servidor lógico recuperado para um ponto específico no tempo durante o período de retenção.
- Um banco de dados no mesmo servidor lógico recuperado para o tempo de exclusão de um banco de dados excluído.
- Um novo banco de dados em qualquer servidor lógico em qualquer região recuperado para os backups diários mais recentes no armazenamento de blobs com replicação geográfica (RA-GRS).

Você também pode usar os [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md) para criar uma [cópia de banco de dados](sql-database-copy.md) em qualquer servidor lógico de qualquer região que seja transacionalmente consistente com o Banco de Dados SQL atual. Você pode usar a cópia do banco de dados e [exportar para um BACPAC](sql-database-export.md) para arquivar uma cópia transacionalmente consistente do banco de dados para armazenamento de longo prazo após o período de retenção ou para transferir uma cópia do banco de dados para uma instância local ou VM do Azure do SQL Server.

## Tempo de recuperação

O tempo de recuperação para restaurar um banco de dados usando backups de banco de dados automatizada é afetado por vários fatores:
 - O tamanho do banco de dados
 - O nível de desempenho do banco de dados
 - O número de logs de transações envolvidos
 - A quantidade de atividade que precisa ser repetida para recuperar até o ponto de restauração
 - A largura de banda de rede se a restauração for para uma região diferente
 - O número de solicitações simultâneas de restauração que estão sendo processadas na região de destino.
 
 Para um banco de dados muito grande e/ou ativo, a restauração pode levar várias horas. Se houver uma interrupção prolongada em uma região, possivelmente haverá muitas solicitações de restauração geográfica sendo processadas por outras regiões. Se houver muitas solicitações, isso poderá aumentar o tempo de recuperação dos bancos de dados nessa região. A maioria das restaurações de banco de dados é concluída em 12 horas.

 Não há nenhuma funcionalidade interna para restauração em massa. O [banco de dados SQL do Azure: o script recuperação de servidor completa](https://gallery.technet.microsoft.com/Azure-SQL-Database-Full-82941666) é um exemplo de uma maneira de realizar essa tarefa.

> [AZURE.IMPORTANT] Para recuperar usando backups automatizados, você deverá ser um membro da função Contribuidor do SQL Server na assinatura ou o proprietário da assinatura. Você pode recuperar usando o Portal do Azure, o PowerShell ou a API REST. Você não pode usar o Transact-SQL.

## Restauração pontual

A Recuperação Pontual permite que você restaure um banco de dados existente como um novo banco de dados para um determinado momento anterior no mesmo servidor lógico usando [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md). Não é possível substituir o banco de dados existente. Você pode restaurar para um determinado momento anterior usando o [Portal do Azure](sql-database-point-in-time-restore-portal.md), o [PowerShell](sql-database-point-in-time-restore-powershell.md) ou a [API REST](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauração Pontual: Portal do Azure](sql-database-point-in-time-restore-portal.md)
- [Restauração Pontual: PowerShell](sql-database-point-in-time-restore-powershell.md)

O banco de dados pode ser restaurado para qualquer nível de desempenho ou pool elástico. É preciso garantir que você tenha uma cota DTU suficiente no servidor lógico ou no pool elástico. Tenha em mente que a restauração cria um novo banco de dados e que o nível de desempenho e a camada de serviço de banco de dados restaurado pode ser diferente do estado atual do banco de dados dinâmico. Uma vez concluído, o banco de dados restaurado é um banco de dados online normal e totalmente acessível, cobrado a taxas normais com base em seu nível de desempenho e na camada de serviço. Você não incorrerá encargos até que a restauração do banco de dados seja concluída.

Um banco de dados geralmente é restaurado para um ponto anterior para fins de recuperação. Ao fazê-lo, você poderá tratar o banco de dados restaurado como um substituto do banco de dados original ou usá-lo para recuperar os dados e, em seguida, atualizar o banco de dados original.

- ***Substituição de banco de dados:*** se o banco de dados restaurado se destinar a ser um substituto do banco de dados original, você deverá verificar se o nível de desempenho e/ou a camada de serviço são adequados e dimensionar o banco de dados, se necessário. Você pode renomear o banco de dados original e, em seguida, dar ao banco de dados restaurado o nome original usando o comando ALTER DATABASE no T-SQL.
- ***Recuperação de dados:*** se você planeja recuperar os dados do banco de dados restaurado para se recuperar de um erro de aplicativo ou de usuário, precisa escrever e executar separadamente quaisquer scripts de recuperação de dados necessários para extrair os dados do banco de dados restaurado para o banco de dados original. Embora a operação de restauração possa demorar muito para concluir, o banco de dados em restauração ficará visível na lista de banco de dados o tempo todo. Se você excluir o banco de dados durante a restauração, ele cancelará a operação e você não será cobrado pelo banco de dados cuja restauração não foi concluída.

Para obter informações detalhadas sobre como usar a Restauração Pontual para se recuperar de erros de usuário e de aplicativo, consulte [Recuperação pontual](sql-database-recovery-using-backups.md#point-in-time-restore)

## Restauração de banco de dados excluído

A recuperação de banco de dados excluído permite que você restaure um banco de dados excluído para o momento de exclusão para um banco de dados excluído no mesmo servidor lógico usando [backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md).

> [AZURE.IMPORTANT] Se você excluir uma instância de servidor do Banco de Dados SQL, todos os seus bancos de dados também serão excluídos e não poderão ser recuperados. No momento, não há suporte para restaurar um servidor excluído.

Você pode usar o mesmo ou um novo nome de banco de dados para o banco de dados restaurado. Você pode usar o [Portal do Azure](sql-database-restore-deleted-database-portal.md), o [PowerShell](sql-database-restore-deleted-database-powershell.md) ou o [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx).

> [AZURE.SELECTOR]
- [Restauração de banco de dados excluída: Portal do Azure](sql-database-restore-deleted-database-portal.md)
- [Restauração pontual excluída: PowerShell](sql-database-restore-deleted-database-powershell.md)

## Restauração geográfica

A restauração geográfica permite que você restaure um Banco de Dados SQL em qualquer servidor e em qualquer região do Azure com base no [backup diário automatizado](sql-database-automated-backups.md) com replicação geográfica mais recente. A restauração geográfica usará um backup com redundância geográfica como sua fonte, e ele poderá ser usado para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter estiver inacessível devido a uma interrupção. Você pode usar o [Portal do Azure](sql-database-geo-restore-portal.md), o [PowerShell](sql-database-geo-restore-powershell.md) ou o [REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx)

> [AZURE.SELECTOR]
- [Restauração geográfica: Portal do Azure](sql-database-geo-restore-portal.md)
- [Restauração geográfica: PowerShell](sql-database-geo-restore-powershell.md)

A restauração geográfica será a opção de recuperação padrão quando seu banco de dados não estiver disponível devido a um incidente na região em que ele está hospedado. Se um incidente de grande escala em uma região resultar na indisponibilidade do seu aplicativo de banco de dados, você poderá usar a restauração geográfica para restaurar um banco de dados do backup mais recente para um servidor em qualquer outra região. Todos os backups são replicados geograficamente e podem apresentar atraso entre o momento em que o backup é feito e replicado geograficamente e o Blob do Azure em uma região diferente. Esse atraso pode ser até uma hora, por isso em caso de desastre pode haver perda de dados de até 1 hora, ou seja, RPO de até 1 hora. Veja a seguir a restauração do banco de dados do último backup diário.

![restauração geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

Para obter informações detalhadas sobre como usar a Restauração geográfica para se recuperar de uma interrupção, consulte [Recuperação de uma interrupção](sql-database-disaster-recovery.md)

> [AZURE.IMPORTANT] Embora a restauração geográfica esteja disponível para todos os níveis de serviço, ela é a mais básica dentre as soluções de recuperação de desastre disponíveis no Banco de Dados SQL com RPO e ERT (Tempo de Recuperação Estimado) mais longos. Para bancos de dados Básicos com tamanho máximo de 2 GB, a restauração geográfica fornece uma solução de DR razoável com um ERT de 12 horas. Para bancos de dados Standard ou Premium maiores, se desejar obter tempos de recuperação significativamente menores ou reduzir a probabilidade de perda de dados, considere usar a Replicação Geográfica Ativa. A Replicação Geográfica Ativa oferece um RPO e um ERT muito menores, pois exige somente que você inicie um failover para um secundário replicado continuamente. Para obter detalhes, consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md).

## Executar recuperação programaticamente usando backups automatizados

Conforme discutido acima, além do Portal do Azure, a recuperação de banco de dados pode ser realizada programaticamente usando o Azure PowerShell e a API REST. As tabelas a seguir descrevem o conjunto de comandos disponíveis.

### PowerShell

|Cmdlet|Descrição|
|------|-----------|
|[Get-AzureRmSqlDatabase](https://msdn.microsoft.com/pt-BR/library/azure/mt603648.aspx)|Obtém um ou mais bancos de dados.|
|[Get-AzureRMSqlDeletedDatabaseBackup](https://msdn.microsoft.com/pt-BR/library/azure/mt693387.aspx)|Obtém um banco de dados excluído que você pode restaurar.|
|[Get-AzureRmSqlDatabaseGeoBackup](https://msdn.microsoft.com/library/azure/mt693388.aspx)|Obtém um backup com redundância geográfica de um banco de dados.|
|[Restore-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt693390.aspx)|Restaura um banco de dados SQL.|
||||

### API REST

|API|Descrição|
|---|-----------|
|[REST (createMode=Recovery)](https://msdn.microsoft.com/library/azure/mt163685.aspx)|Restaura um banco de dados|
|[Obter, Criar ou Atualizar o Status de um Banco de Dados](https://msdn.microsoft.com/library/azure/mt643934.aspx)|Retorna o status durante uma operação de restauração|
||||



## Resumo

Backups automáticos protegem seus bancos de dados contra erros de usuário e de aplicativo, exclusão acidental do banco de dados e interrupções prolongadas. A solução de administração de custo zero está disponível com todos os bancos de dados SQL.

## Próximas etapas

- Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, confira [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, confira [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0803_2016-->