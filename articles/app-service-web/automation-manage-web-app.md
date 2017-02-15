---
title: "Gerenciar aplicativo Web do Azure usando a Automação do Azure | Microsoft Docs"
description: "Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar o Aplicativo Web do Azure."
services: app-service\web, automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: 
ms.assetid: c960a44f-f941-401d-afba-a4bc0f0394eb
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/29/2016
ms.author: magoedte;csand
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d9b12c074417e51057dea68e8cff04df5081e111


---
# <a name="managing-azure-web-app-using-azure-automation"></a>Gerenciando o Aplicativo Web do Azure usando a Automação do Azure
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento do Aplicativo Web do Azure.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](../automation/automation-intro.md) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com o uso da Automação do Azure, as tarefas manuais, repetidas, de execução longa e propensas a erros podem ser automatizadas para aumentar a confiabilidade, a eficiência e o tempo de retorno para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que é dimensionado para atender às suas necessidades. Na Automação do Azure, processos podem ser inicializados manualmente, por sistemas de terceiros ou em intervalos agendados para que as tarefas acontecem exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI e DevOps para se concentrar no trabalho que agrega valor de negócios, transferindo as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-web-app"></a>Como a Automação do Azure ajuda a gerenciar o Aplicativo Web do Azure?
O aplicativo Web pode ser gerenciado na Automação do Azure usando os cmdlets do PowerShell que estão disponíveis em [Módulos do Azure PowerShell](../powershell-install-configure.md). Você pode [instalar esses cmdlets do PowerShell do aplicativo Web na Automação do Azure](https://azure.microsoft.com/blog/announcing-azure-resource-manager-support-azure-automation-runbooks/), de modo que você pode executar todas as tarefas de gerenciamento de aplicativo Web no serviço. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets de outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

Aqui estão alguns exemplos de Serviços de Aplicativos com a automação de gerenciamento:

* [Scripts para gerenciar os Aplicativos Web](https://azure.microsoft.com/documentation/scripts/)

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para gerenciar o Aplicativo Web do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

* Confira o [Guia de introdução](../automation/automation-first-runbook-graphical.md)




<!--HONumber=Nov16_HO3-->


