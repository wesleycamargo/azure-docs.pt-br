<properties
	pageTitle="Gerenciar bancos de dados do SQL Azure usando a Automação do Azure"
	description="Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar bancos de dados SQL do Azure em grande escala."
	services="sql-database, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="eamono"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="jolevy"/>



#Gerenciando bancos de dados SQL do Azure usando a Automação do Azure

Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento de seus bancos de dados SQL do Azure.


## O que é Automação do Azure?

[Automação do Azure](http://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo.  Com a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce.  Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor de negócios transferindo as tarefas de gerenciamento de nuvem para serem executados automaticamente por Automação do Azure.


## Como a Automação do Azure pode ajudar a gerenciar bancos de dados SQL do Azure?

Os banco de dados SQL do Azure podem ser gerenciados na automação do Azure usando os cmdlets do PowerShell que estão disponíveis em [Ferramentas PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx).  A Automação do Azure tem esses cmdlets do PowerShell de banco de dados SQL do Azure disponíveis imediatamente para que você possa executar todas as suas tarefas de gerenciamento de banco de dados SQL dentro do serviço.  Você também pode combinar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

A Automação do Azure também tem a capacidade de se comunicar com servidores SQL diretamente, emitindo comandos SQL usando o PowerShell.


## Próximas etapas

Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar bancos de dados do SQL Azure, siga estes links para obter mais informações sobre a Automação do Azure.

Consulte o [Tutorial de Introdução](automation-create-runbook-from-samples.md) para a Automação do Azure

<!--HONumber=49-->