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
   ms.workload="data-management"
   ms.date="05/10/2016"
   ms.author="sstein"/>

# Visão geral: restauração geográfica do Banco de Dados SQL

A restauração geográfica permite restaurar o banco de dados SQL do backup diário mais recente, sendo habilitado automaticamente para todos os níveis de serviço sem nenhum custo extra. A restauração geográfica usa um backup com redundância geográfica como sua fonte, podendo ser usado para recuperar um banco de dados mesmo que ele esteja inacessível devido a uma interrupção.

Iniciar a restauração geográfica cria um novo banco de dados SQL que pode ser criado em qualquer servidor de qualquer região do Azure.


|Tarefa (Portal) | PowerShell | REST |
|:--|:--|:--|
| [Recuperar um banco de dados SQL de uma cópia em uma região diferente](sql-database-geo-restore-portal.md) | [PowerShell](sql-database-geo-restore-powershell.md) | [REST (createMode=Restore)](https://msdn.microsoft.com/library/azure/mt163685.aspx) |



A restauração geográfica fornecerá a opção de recuperação padrão quando um banco de dados estiver indisponível devido a um incidente na região onde ele está hospedado. Semelhante à [recuperação pontual](sql-database-point-in-time-restore.md), a restauração geográfica conta com backups de banco de dados no armazenamento do Azure com redundância geográfica. Ele restaura a partir da cópia de backup replicado geograficamente e, portanto, é resistente a falhas de armazenamento na região primária.



## Restauração geográfica em detalhes

A restauração geográfica usa a mesma tecnologia que a recuperação pontual, com uma diferença importante. Ela restaura o banco de dados de uma cópia de backup diário mais recente no RA-GRS (armazenamento de blobs replicado geograficamente). Para cada banco de dados ativo, o serviço mantém uma cadeia de backup que inclui um backup completo semanal, vários backups diferenciais diários e logs de transações salvos a cada 5 minutos. Esses blobs são replicados geograficamente, garantindo que os backups diários estejam disponíveis mesmo após uma grande falha na região primária. O código a seguir mostra a replicação geográfica de backups diários e semanais copiados para os contêineres de armazenamento.

![restauração geográfica](./media/sql-database-geo-restore/geo-restore-1.png)


Se um incidente de grande escala nos resultados de uma região causar indisponibilidade no seu aplicativo de banco de dados, você poderá usar a restauração geográfica para recuperar um banco de dados do backup mais recente para um servidor em qualquer outra região. Todos os backups são replicados geograficamente e podem apresentar atraso entre o momento em que o backup é feito e replicado geograficamente e o Blob do Azure em uma região diferente. Esse atraso pode ser até uma hora, por isso em caso de desastre pode haver perda de dados de até 1 hora, ou seja, RPO de até 1 hora. Veja a seguir a restauração do banco de dados do último backup diário.


![restauração geográfica](./media/sql-database-geo-restore/geo-restore-2.png)



## Tempo de recuperação de uma restauração geográfica

O tempo de recuperação é afetado por vários fatores: o tamanho e o nível de desempenho do banco de dados, bem como o número de solicitações simultâneas de restauração que estão sendo processadas na região de destino. Caso haja uma paralisação prolongada em uma região, é possível que haja muitas solicitações de restauração geográfica sendo processadas por outras regiões. Se houver muitas solicitações, isso poderá aumentar o tempo de recuperação dos bancos de dados nessa região.


## Resumo

Embora a restauração geográfica esteja disponível para todos os níveis de serviço, ela é a mais básica dentre as soluções de recuperação de desastres disponíveis no Banco de Dados SQL com RPO e ERT (Tempo de Recuperação Estimado) mais longos. Para bancos de dados Básicos com tamanho máximo de 2 GB, a restauração geográfica fornece uma solução de DR razoável com um ERT de 12 horas. Para bancos de dados Standard ou Premium maiores, se desejar obter tempos de recuperação significativamente menores ou reduzir a probabilidade de perda de dados, considere usar a replicação geográfica ativa. A replicação geográfica ativa oferece um RPO e ERT muito menor, pois exige somente que você inicie um failover para um secundário replicado continuamente. Para obter detalhes, consulte [Replicação geográfica ativa](sql-database-geo-replication-overview.md).

## Recursos adicionais

- [Perguntas frequentes sobre BCDR no Banco de Dados SQL](sql-database-bcdr-faq.md)
- [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)

<!---HONumber=AcomDC_0511_2016-->