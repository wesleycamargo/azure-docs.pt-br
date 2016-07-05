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
	ms.date="06/16/2016"
	ms.author="carlrab"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Cenários de continuidade de negócios para Banco de Dados SQL do Azure

> [AZURE.SELECTOR]
- [Continuidade dos negócios](sql-database-business-continuity.md)
- [Cenários](sql-database-business-continuity-scenarios.md)
- [Restauração pontual](sql-database-point-in-time-retore.md)
- [Restaurar banco de dados excluído](sql-database-restore-deleted-database.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação geográfica ativa](sql-database-geo-replication)

Neste artigo, você aprenderá sobre vários cenários de continuidade de negócios de banco de dados SQL do Azure.

## Recuperação de uma interrupção

[Restaurar um Banco de Dados SQL ou fazer failover para um secundário](sql-database-disaster-recovery.md) descreve como se recuperar de uma interrupção usando os seguintes recursos:

- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Restauração geográfica](sql-database-geo-restore.md)

Este artigo abordou quando iniciar a recuperação, como se recuperar usando cada recurso, como configurar seu banco de dados depois da recuperação e como configurar seu aplicativo após a recuperação.

## Recuperar de um erro de usuário

[Recuperar um Banco de Dados SQL do Azure de um erro de usuário](sql-database-user-error-recovery.md) descreve como se recuperar de erros de usuário ou de modificação de dados não intencionais usando os seguintes recursos:

- [Restauração pontual](sql-database-point-in-time-restore.md) 
- [Restaurar banco de dados excluído](sql-database-restore-deleted-database.md)

## Executar uma análise de recuperação de desastre

[Executar análise de recuperação de desastre](sql-database-disaster-recovery-drills.md) descreve como executar uma análise de recuperação de desastre usando os seguintes recursos:

- [Replicação geográfica ativa](sql-database-geo-replication-overview.md)
- [Restauração geográfica](sql-database-geo-restore.md)

Recomenda-se a validação periódica da preparação do aplicativo para o fluxo de trabalho de recuperação. Consideramos uma boa prática de engenharia a verificação do comportamento do aplicativo e as implicações de perda de dados e/ou interrupção que envolvem o failover. Isso também é uma exigência da maioria dos padrões do setor, como parte da certificação de continuidade dos negócios.

A execução de uma análise de recuperação de desastres é composta por:

- Simulação da interrupção da camada de dados
- Recuperando 
- Validação da integridade do aplicativo após a recuperação

## Gerenciar a segurança depois da recuperação de desastre

[Como gerenciar a segurança após a recuperação de desastre](sql-database-geo-replication-security-config.md) descreve os requisitos de autenticação para configurar e controlar a [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) e as etapas necessárias para configurar o acesso do usuário ao banco de dados secundário. Ele também descreve como habilitar o acesso ao banco de dados recuperado depois de usar a [Restauração Geográfica](sql-database-geo-restore.md).

## Gerenciar atualizações sem interrupção dos aplicativos em nuvem usando a Replicação Geográfica Ativa

[Gerenciando a rolagem de atualizações de aplicativos na nuvem usando a Replicação Geográfica Ativa do Banco de Dados SQL](sql-database-manage-application-rolling-upgrade.md) descreve como usar a [Replicação Geográfica](sql-database-geo-replication-overview.md) no Banco de Dados SQL para habilitar as atualizações sem interrupção do seu aplicativo em nuvem. Como a atualização é uma operação com interrupção, ele deve fazer parte de seu design e planejamento de continuidade dos negócios. Este artigo abrange dois métodos diferentes para orquestrar o processo de atualização, bem como os benefícios e as compensações de cada opção.

## Criar um aplicativo para recuperação de desastre na nuvem usando a Replicação Geográfica Ativa

[Criar um aplicativo para recuperação de desastre na nuvem usando a Replicação Geográfica Ativa no Banco de Dados SQL](sql-database-designing-cloud-solutions-for-disaster-recovery.md) descreve como usar a [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md) no Banco de Dados SQL para projetar aplicativos de banco de dados resilientes a falhas regionais e interrupções catastróficas. Esse artigo considera a topologia de implantação de aplicativos, o contrato de nível de serviço que você está visando, a latência do tráfego e os custos. Nele, também podemos encontrar os padrões comuns de aplicativo — cada um com seus benefícios e compensações.

## Estratégias de recuperação de desastre para aplicativos que usam pools de banco de dados elásticos

[Estratégias de recuperação de desastre para aplicativos que usam o Pool Elástico de Banco de Dados SQL](sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool.md) descreve os cenários de recuperação de desastre que usam [pools de banco de dados elásticos](sql-database-elastic-pool.md).

## Próximas etapas

- Para saber mais sobre como usar e configurar a Replicação Geográfica Ativa para recuperação de desastre, confira [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- Para saber mais sobre como usar a Restauração Geográfica para recuperação de desastre, confira [Restauração Geográfica](sql-database-geo-restore.md)

## Recursos adicionais

- [Recuperação de desastre e continuidade de negócios do Banco de Dados SQL](sql-database-business-continuity.md)
- [Restauração pontual](sql-database-point-in-time-restore.md)
- [Restauração geográfica](sql-database-geo-restore.md)
- [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
- [Criando aplicativos para recuperação de desastre na nuvem](sql-database-designing-cloud-solutions-for-disaster-recovery.md)
- [Finalizar seu Banco de Dados SQL do Azure recuperado](sql-database-recovered-finalize.md)
- [Configuração de segurança para a Replicação Geográfica](sql-database-geo-replication-security-config.md)
- [Perguntas frequentes sobre BCDR no Banco de Dados SQL](sql-database-bcdr-faq.md)

<!---HONumber=AcomDC_0622_2016-->