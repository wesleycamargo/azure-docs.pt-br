<properties
	pageTitle="Gerenciar o Armazenamento do Azure usando a Automação do Azure"
	description="Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Armazenamento do Azure em grande escala."
	services="storage, automation"
	documentationCenter=""
	authors="jodoglevy"
	manager="eamono"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/20/2015"
	ms.author="jolevy"/>



# Gerenciando o armazenamento do Azure usando a Automação do Azure

Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento dos bancos de dados SQL do Azure.


## O que é Automação do Azure?

[Automação do Azure](http://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor comercial ao transferir as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.


## Como a Automação do Azure ajuda a gerenciar o Armazenamento do Azure?

O Armazenamento do Azure pode ser gerenciado na automação do Azure usando os cmdlets do PowerShell disponíveis em [Ferramentas PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). A Automação do Azure tem esses cmdlets do PowerShell do Armazenamento disponíveis imediatamente para que você possa executar todas as suas tarefas de blob, de tabela e de gerenciamento de fila no serviço. Você também pode combinar esses cmdlets na automação do Azure com os cmdlets para outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

A [Galeria de runbooks de automação do Azure](http://azure.microsoft.com/blog/2014/10/07/introducing-the-azure-automation-runbook-gallery/) contém uma variedade de runbooks da comunidade e da equipe de produto para começar a automatizar o gerenciamento do Armazenamento do Azure, outros serviços do Azure e sistemas de terceiros. A galeria de runbooks inclui:

 * [Remover blobs do Armazenamento do Azure mais antigos que X dias](https://gallery.technet.microsoft.com/scriptcenter/Remove-Storage-Blobs-that-aae4b761)
 * [Baixar um blob de Armazenamento do Azure para Automação do Azure](https://gallery.technet.microsoft.com/scriptcenter/a-Blob-from-Azure-Storage-6bc13745)
 * [Criar cópias de discos de dados de VM do Azure em um serviço de nuvem do Azure](https://gallery.technet.microsoft.com/scriptcenter/Make-copies-of-Azure-VM-065a6394)


## Próximas etapas

Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar blobs de Armazenamento do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

Consulte o [Tutorial de Introdução](automation-create-runbook-from-samples.md) da Automação do Azure

<!--HONumber=52-->