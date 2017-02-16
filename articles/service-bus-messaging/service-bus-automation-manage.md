---
title: "Gerenciar o Barramento de Serviço do Azure usando a Automação do Azure | Microsoft Docs"
description: "Saiba como usar o serviço de Automação do Azure para gerenciar o Barramento de Serviço do Azure."
services: service-bus-messaging, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: 2a06e94b-92ef-4b5d-a2b7-fe827063df82
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2017
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 994a379129bffd7457912bc349f240a970aed253
ms.openlocfilehash: 4817cda757b9f85f6225237f79293860c990ca88


---
# <a name="managing-azure-service-bus-using-azure-automation"></a>Gerenciando o Barramento de Serviço do Azure usando a Automação do Azure
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento do Barramento de Serviço do Azure.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](../automation/automation-intro.md) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processos e por uma configuração de estado desejada. Com o uso da Automação do Azure, as tarefas manuais, repetidas, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI e DevOps para se concentrar no trabalho que agrega valor de negócios, transferindo as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-service-bus"></a>Como a Automação do Azure ajuda a gerenciar o Barramento de Serviço do Azure?
Você pode gerenciar o Barramento de Serviço com a Automação do Azure usando a [API REST do Barramento de Serviço](https://docs.microsoft.com/rest/api/servicebus/). Na Automação do Azure, é possível executar scripts do PowerShell para executar muitas das tarefas do Barramento de Serviço usando a API REST. Você também pode combinar essas chamadas de API REST na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.

Veja alguns exemplos de uso do PowerShell para gerenciar o Barramento de Serviço do Azure:

* [Cmdlets personalizados do PowerShell para gerenciar as filas do Barramento de Serviço do Azure](https://blogs.technet.microsoft.com/uktechnet/2014/12/04/sample-of-custom-powershell-cmdlets-to-manage-azure-servicebus-queues)
* [Como criar filas, tópicos e assinaturas do Barramento de Serviço usando um script do PowerShell](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [Criar namespaces do Barramento de Serviço do Azure usando o PowerShell](http://buildazure.com/2015/09/24/create-azure-service-bus-namespaces-using-powershell-and-x-plat-cli/)

O módulo do PowerShell para trabalhar com o barramento de serviço do Azure em runbooks de Automação pode ser baixado na [Galeria do PowerShell](https://www.powershellgallery.com/packages/AzureServiceBusCreation/1.0).

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ele pode ser usado para gerenciar os Barramentos de Serviço do Azure, siga estes links para saber mais sobre a Automação do Azure.

* Confira o [Guia de introdução](../automation/automation-first-runbook-graphical.md)
* Confira como [gerenciar o Barramento de Serviço com o PowerShell](service-bus-powershell-how-to-provision.md)




<!--HONumber=Jan17_HO2-->


