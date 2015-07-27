<properties
	pageTitle="Gerenciar o Gerenciamento de API do Azure usando a Automação do Azure"
	description="Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Gerenciamento de API do Azure."
	services="api-management, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="api-management"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/16/2015"
	ms.author="csand"/>



#Gerenciando o Gerenciamento de API do Azure usando a Automação do Azure

Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento do Gerenciamento de API.

## O que é Automação do Azure?

[Automação do Azure](http://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com o uso da Automação do Azure, as tarefas manuais, repetidas com frequência, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI e DevOps para se concentrar no trabalho que agrega valor de negócios, transferindo as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.


## Como a Automação do Azure ajudar a gerenciar o Gerenciamento de API?

O Gerenciamento de API pode ser gerenciado na Automação do Azure usando a [API REST de Gerenciamento de API](https://msdn.microsoft.com/library/azure/dn776326.aspx). Dentro de Automação do Azure, você pode escrever scripts de fluxo de trabalho do PowerShell para executar muitas de suas tarefas de Gerenciamento de API usando a API REST. Você também pode combinar essas chamadas de API REST na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.


## Próximas etapas

Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar o Gerenciamento de API do Azure, siga estes links para obter mais informações.

* Consulte o [Tutorial de introdução](../automation-create-runbook-from-samples.md) da Automação do Azure.
* Leia a postagem de blog da comunidade [Módulo do PowerShell para as APIs REST de Gerenciamento de API do #Azure](https://alexandrebrisebois.wordpress.com/2014/08/17/powershell-module-for-the-azure-api-management-rest-apis/).
 

<!---HONumber=July15_HO3-->