<properties
	pageTitle="Gerenciar cofre de chave do Azure usando a automação do Azure | Microsoft Azure"
	description="Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Cofre da Chave do Azure."
	services="Key-Vault, automation"
	documentationCenter=""
	authors="csand-msft"
	manager="eamono"
	editor=""/>

<tags
	ms.service="key-vault"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2015"
	ms.author="csand"/>



#Gerenciar o Cofre da Chave do Azure usando a Automação do Azure

Este guia apresentará a você o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento de suas chaves e segredos no cofre da chave do Azure.

## O que é Automação do Azure?

[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com o uso da Automação do Azure, as tarefas manuais, repetidas com frequência, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI e DevOps para se concentrar no trabalho que agrega valor de negócios, transferindo as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.


## Como a Automação do Azure pode ajudar a gerenciar o cofre da chave do Azure?

O Cofre da Chave pode ser gerenciado na Automação do Azure usando os [cmdlets do Cofre da Chave do Azure](https://msdn.microsoft.com/library/azure/dn868052.aspx) que estão disponíveis na [Galeria do PowerShell](https://azure.microsoft.com/blog/azps-1-0/). Você pode importar esse módulo na Automação do Azure para que seja possível executar muitas de suas tarefas de gerenciamento do Cofre da Chave no serviço. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.

Com os cmdlets do Cofre da Chave do Azure, você pode executar estas tarefas e muitas outras: criar ou importar uma chave, criar ou atualizar um segredo, atualizar atributos de uma chave, obter uma chave ou um segredo ou excluir uma chave ou um segredo.


## Próximas etapas

Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar o Cofre da Chave do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

* Consulte o [Tutorial de introdução](../automation-create-runbook-from-samples.md) da Automação do Azure.
* Consulte os [scripts do PowerShell do Cofre da Chave do Azure](https://gallery.technet.microsoft.com/scriptcenter/Azure-Key-Vault-Powershell-1349b091).

<!---HONumber=AcomDC_0128_2016-->