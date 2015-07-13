<properties 
	pageTitle="Gerenciar o Backup do Azure usando a Automação do Azure" 
	description="Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Backup do Azure." 
	services="backup, automation" 
	documentationCenter="" 
	authors="eamonoreilly" 
	manager="jwinter" 
	editor=""/>

<tags 
	ms.service="backup" 
	ms.workload="storage-backup-recovery" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/13/2015" 
	ms.author="eamono"/>


#Gerenciando o Backup do Azure usando a Automação do Azure

Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento do Backup do Azure.

## O que é Automação do Azure?

[Automação do Azure](http://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor comercial ao transferir as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.


## Como a Automação do Azure ajuda a gerenciar o Backup do Azure?

O Backup pode ser gerenciado na Automação do Azure usando os cmdlets do PowerShell que estão disponíveis no [módulo Windows MSOnlineBackup](https://technet.microsoft.com/pt-br/library/hh770400.aspx). A Automação do Azure pode chamar esses cmdlets do PowerShell para que você possa executar todas as tarefas de gerenciamento de Backup no serviço. Você também pode combinar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.


## Próximas etapas

Agora que você aprendeu os fundamentos de Automação do Azure e como ele pode ser usado para gerenciar o Backup do Azure, siga estes links para saber mais sobre a Automação do Azure.

* Confira o [Guia de Introdução](http://go.microsoft.com/fwlink/?LinkId=390560) da Automação do Azure
 

<!---HONumber=62-->