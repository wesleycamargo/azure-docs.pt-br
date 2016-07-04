<properties
   pageTitle="Continuidade dos negócios na nuvem - Restauração geográfica | Microsoft Azure"
   description="Saiba como o Banco de Dados SQL do Azure dá suporte para a continuidade dos negócios em nuvem e para a recuperação de banco de dados, além de ajudar a manter os aplicativos em nuvem críticos em execução."
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
   ms.workload="sqldb-bcdr"
   ms.date="06/17/2016"
   ms.author="sstein"/>

# Visão geral: restaurar um Banco de Dados SQL do Azure de um backup com redundância geográfica

> [AZURE.SELECTOR]
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Restaurar banco de dados excluído](sql-database-restore-deleted-database.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)


A restauração geográfica permite que você restaure um Banco de Dados SQL em qualquer servidor e em qualquer região do Azure do [backup diário automático](sql-database-automated-backups.md) mais recente. A restauração geográfica usará um backup com redundância geográfica como sua fonte, e ele poderá ser usado para recuperar um banco de dados, mesmo se o banco de dados ou o datacenter estiver inacessível devido a uma interrupção. Você pode usar o [Portal do Azure](sql-database-geo-restore-portal.md), o [PowerShell](sql-database-geo-restore-powershell.md) ou o [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx)

> [AZURE.SELECTOR]
- [Visão geral](sql-database-geo-restore.md)
- [Portal do Azure](sql-database-geo-restore-portal.md)
- [PowerShell](sql-database-geo-restore-powershell.md)

A restauração geográfica será a opção de recuperação padrão quando seu banco de dados não estiver disponível devido a um incidente na região em que ele está hospedado. O banco de dados pode ser criado em qualquer servidor em qualquer região do Azure. A restauração geográfica depende dos [backups automatizados de banco de dados](sql-database-automated-backups.md) no armazenamento com redundância geográfica do Azure e restaura da cópia de backup replicada geograficamente e, portanto, é resistente a interrupções de armazenamento na região primária.

## Restauração geográfica em detalhes

A restauração geográfica usa a mesma tecnologia que a Recuperação Pontual, com uma diferença importante. Ela restaura o banco de dados de uma cópia de backup diário mais recente no RA-GRS (armazenamento de blobs replicado geograficamente). Para cada banco de dados ativo, o serviço mantém uma cadeia de backup que inclui um backup completo semanal, vários backups diferenciais diários e logs de transações salvos a cada 5 minutos. Esses blobs são replicados geograficamente, garantindo que os backups diários estejam disponíveis mesmo após uma grande falha na região primária. O código a seguir mostra a Replicação Geográfica de backups diários e semanais copiados para os contêineres de armazenamento.

![restauração geográfica](./media/sql-database-geo-restore/geo-restore-1.png)


Se um incidente de grande escala em uma região resultar na indisponibilidade do seu aplicativo de banco de dados, você poderá usar a restauração geográfica para restaurar um banco de dados do backup mais recente para um servidor em qualquer outra região. Todos os backups são replicados geograficamente e podem apresentar atraso entre o momento em que o backup é feito e replicado geograficamente e o Blob do Azure em uma região diferente. Esse atraso pode ser até uma hora, por isso em caso de desastre pode haver perda de dados de até 1 hora, ou seja, RPO de até 1 hora. Veja a seguir a restauração do banco de dados do último backup diário.


![restauração geográfica](./media/sql-database-geo-restore/geo-restore-2.png)

Use [Obter banco de dados recuperável](https://msdn.microsoft.com/library/dn800985.aspx) (*LastAvailableBackupDate*) para obter o ponto de restauração mais recente replicado geograficamente.

## Tempo de recuperação de uma restauração geográfica

O tempo de recuperação é afetado por vários fatores: o tamanho e o nível de desempenho do banco de dados, bem como o número de solicitações simultâneas de restauração que estão sendo processadas na região de destino. Se houver uma paralisação prolongada em uma região, possivelmente haverá muitas solicitações de restauração geográfica sendo processadas por outras regiões. Se houver muitas solicitações, isso poderá aumentar o tempo de recuperação dos bancos de dados nessa região. O tempo que se leva para restaurar um banco de dados depende de vários fatores, como tamanho do banco de dados, número do log de transações, largura de banda da rede, etc. A maioria das restaurações de banco de dados é concluída em 12 horas.

## Resumo

Embora a restauração geográfica esteja disponível para todos os níveis de serviço, ela é a mais básica dentre as soluções de recuperação de desastre disponíveis no Banco de Dados SQL com RPO e ERT (Tempo de Recuperação Estimado) mais longos. Para bancos de dados Básicos com tamanho máximo de 2 GB, a restauração geográfica fornece uma solução de DR razoável com um ERT de 12 horas. Para bancos de dados Standard ou Premium maiores, se desejar obter tempos de recuperação significativamente menores ou reduzir a probabilidade de perda de dados, considere usar a Replicação Geográfica Ativa. A Replicação Geográfica Ativa oferece um RPO e um ERT muito menores, pois exige somente que você inicie um failover para um secundário replicado continuamente. Para obter detalhes, confira [Active Geo-Replication](sql-database-geo-replication-overview.md) (Replicação geográfica ativa).

## Próximas etapas

- Para obter as etapas detalhadas de como restaurar um Banco de Dados SQL do Azure usando o portal do Azure de um backup com redundância geográfica, confira [Geo-Restore using the Azure Portal](sql-database-geo-restore-portal.md) (Restauração geográfica com o Portal do Azure)
- Para obter as etapas detalhadas de como restaurar um Banco de Dados SQL do Azure usando o PowerShell de um backup com redundância geográfica, confira [Geo-Restore using PowerShell](sql-database-geo-restore-powershell.md) (Restauração geográfica com o PowerShell)
- Para uma discussão completa sobre como se recuperar de uma interrupção, confira [Recover from an outage](sql-database-disaster-recovery.md) (Recuperação de uma interrupção)

## Recursos adicionais

- [Cenários de continuidade dos negócios](sql-database-business-continuity-scenarios.md)

<!---HONumber=AcomDC_0622_2016-->