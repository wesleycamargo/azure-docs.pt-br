<properties
	pageTitle="Cenários de continuidade de negócios para Banco de Dados SQL do Azure | Microsoft Azure"
	description="Cenários de continuidade de negócios para Banco de Dados SQL do Azure"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="07/16/2016"
	ms.author="carlrab"
   ms.workload="sqldb-bcdr"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Cenários de continuidade de negócios para Banco de Dados SQL do Azure

Este artigo apresenta vários cenários de recuperação de desastres e diversos cenários de design de aplicativo para continuidade de negócios.

## Recuperação de uma interrupção

Caso ocorra uma interrupção, [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-disaster-recovery.md) descreve como usar qualquer uma das soluções de continuidade de negócios a seguir para recuperar-se da interrupção:

- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Restauração geográfica](sql-database-recovery-using.backups.md#geo-restore)

As etapas específicas e o período necessário para recuperar-se de uma interrupção variam de acordo com a solução de continuidade de negócios que você escolher. No entanto, independentemente de sua solução de continuidade de negócios, você precisa saber quando iniciar a recuperação, as etapas de recuperação do banco de dados para cada solução de continuidade de negócios, como configurar o banco de dados após a recuperação e como configurar seu aplicativo após a recuperação para concluir a recuperação de uma interrupção.

## Recuperação de um erro

Caso ocorra um erro do usuário ou outro erro de modificação não intencional, [Recuperar um Banco de Dados SQL do Azure de uma interrupção](sql-database-user-error-recovery.md) descreve como usar qualquer uma das soluções de continuidade de negócios a seguir para recuperar-se do erro:

- [Restauração pontual](sql-database-recovery-using-backups.md#point-in-time-restore)
- [Restaurar banco de dados excluído](sql-database-recovery-using-backups.md#deleted-database-restore)

As etapas específicas e o período necessário para recuperar-se de uma interrupção variam de acordo com a solução de continuidade de negócios que você escolher. No entanto, independentemente de sua solução de continuidade de negócios, você precisa saber como recuperar-se de um erro para cada solução de continuidade de negócios.

## Realizar uma simulação de recuperação de desastre para preparar-se para uma interrupção

Para que sua solução de continuidade de negócios seja eficaz, é recomendável realizar a validação periódica da preparação do aplicativo para o fluxo de trabalho de recuperação. Consideramos uma boa prática de engenharia a verificação do comportamento do aplicativo e as implicações de perda de dados e/ou interrupção que envolvem o failover. Isso também é uma exigência da maioria dos padrões do setor, como parte da certificação de continuidade dos negócios.

A execução de uma análise de recuperação de desastres é composta por:

- Simulação da interrupção da camada de dados
- Recuperando
- Validação da integridade do aplicativo após a recuperação

[Realizar uma simulação de recuperação de desastre](sql-database-disaster-recovery-drills.md) descreve como realizar uma simulação de recuperação de desastre usando uma das seguintes soluções de continuidade de negócios:

- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Restauração geográfica](sql-database-recovery-using-backups.md#geo-restore)

## Gerenciar atualizações sem interrupção dos aplicativos em nuvem usando a Replicação Geográfica Ativa

Atualizar um aplicativo em nuvem com um Banco de Dados SQL é uma operação de interrupção, portanto, você precisa incluir esse cenário como parte de seu planejamento e design de continuidade de negócios. [Gerenciar atualizações de aplicativos](sql-database-manage-application-rolling-upgrade.md) descreve como usar a [Replicação Geográfica](sql-database-geo-replication-overview.md) no Banco de Dados SQL para habilitar atualizações sem interrupção do seu aplicativo na nuvem. Como a atualização Este artigo abrange dois métodos diferentes para orquestrar o processo de atualização, bem como os benefícios e as compensações de cada opção.

## Criar um aplicativo para recuperação de desastre na nuvem usando a Replicação Geográfica Ativa

[Criar um aplicativo para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md) descreve como usar a [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) no Banco de Dados SQL para projetar aplicativos de banco de dados resilientes a falhas regionais e interrupções catastróficas. Esse artigo considera a topologia de implantação de aplicativos, o contrato de nível de serviço que você está visando, a latência do tráfego e os custos. Nele, também podemos encontrar os padrões comuns de aplicativo — cada um com seus benefícios e compensações.

## Estratégias de recuperação de desastre para aplicativos que usam pools de banco de dados elásticos

[Estratégias de recuperação de desastre para Pool Elástico](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) descreve os cenários de recuperação de desastre que usam [pools de banco de dados elásticos](sql-database-elastic-pool.md).

## Próximas etapas

- Para obter uma visão geral sobre a continuidade de negócios, consulte [Visão geral da continuidade de negócios](sql-database-business-continuity.md)
- Para saber mais sobre backups automatizados do Banco de Dados SQL do Azure, consulte [Backups automatizados do Banco de Dados SQL](sql-database-automated-backups.md)
- Para saber mais sobre cenários de design e recuperação de continuidade dos negócios, consulte [Cenários de continuidade](sql-database-business-continuity-scenarios.md)
- Para saber mais sobre como usar backups automatizados de recuperação, consulte [Restaurar um banco de dados de backups iniciados pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre opções de recuperação mais rápidas, consulte [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar backups automatizados para arquivamento, consulte [cópia de banco de dados](sql-database-copy.md)

<!---HONumber=AcomDC_0727_2016-->