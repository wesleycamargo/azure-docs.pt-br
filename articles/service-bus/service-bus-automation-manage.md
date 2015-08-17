<properties
	pageTitle="Gerenciar Barramento de Serviço do Azure usando a Automação do Azure"
	description="Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Barramento de Serviço do Azure."
	services="service-bus, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="core"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/13/2015"
	ms.author="csand"/>



#Gerenciando o Barramento de Serviço do Azure usando a Automação do Azure

Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento do Barramento de Serviço do Azure.

## O que é Automação do Azure?

[Automação do Azure](http://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com o uso da Automação do Azure, as tarefas manuais, repetidas com frequência, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI e DevOps para se concentrar no trabalho que agrega valor de negócios, transferindo as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.


## Como a Automação do Azure ajuda a gerenciar o Barramento de Serviço do Azure?

O Barramento de Serviço pode ser gerenciado na Automação do Azure usando a [API REST do Barramento de Serviço](https://msdn.microsoft.com/library/azure/hh780717.aspx). Dentro da Automação do Azure, você pode escrever scripts de fluxo de trabalho do PowerShell para executar muitas de suas tarefas de Barramento de Serviço usando a API REST. Você também pode combinar essas chamadas de API REST na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.


## Próximas etapas

Agora que você aprendeu os fundamentos de Automação do Azure e como ele pode ser usado para gerenciar os Barramentos de Serviço do Azure, siga estes links para saber mais sobre a Automação do Azure.

* Confira o [Guia de introdução](../automation-create-runbook-from-samples.md) da Automação do Azure.
* Confira o artigo sobre [Gerenciar Barramento de Serviço com o PowerShell](service-bus-powershell-how-to-provision.md)
 

<!---HONumber=August15_HO6-->