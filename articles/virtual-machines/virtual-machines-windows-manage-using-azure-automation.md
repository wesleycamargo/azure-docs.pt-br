---
title: "Gerenciar VMs do Azure usando a automação do Azure | Microsoft Docs"
description: "Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar máquinas virtuais do Azure em grande escala."
services: virtual-machines-windows, automation
documentationcenter: 
author: jodoglevy
manager: timlt
editor: 
ms.assetid: ce49f83a-f409-42ee-af74-a8ea2caa9ae8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/19/2016
ms.author: jolevy
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 60d9c161b58419bb85c7ec473d853db4cbc40dd1


---
# <a name="managing-azure-virtual-machines-using-azure-automation"></a>Gerenciamento de Máquinas Virtuais do Azure usando a Automação do Azure
Este guia apresenta o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento das máquinas virtuais do Azure.

## <a name="what-is-azure-automation"></a>O que é Automação do Azure?
[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Usando a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e disponível que pode ser dimensionado para atender às suas necessidades conforme sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Você pode reduzir o custo operacional e liberar a equipe de TI e DevOps para se concentrar em trabalho que agregue valor de negócios ao executar as tarefas de gerenciamento de nuvem automaticamente com a Automação do Azure.

## <a name="how-can-azure-automation-help-manage-azure-virtual-machines"></a>Como a Automação do Azure pode ajudar a gerenciar máquinas virtuais do Azure?
As máquinas virtuais podem ser gerenciadas na Automação do Azure usando [PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). A Automação do Azure inclui cmdlets do PowerShell do Azure, então você pode executar todas as suas tarefas de gerenciamento de máquina virtual dentro do serviço. Você também pode combinar os cmdlets na Automação do Azure com os cmdlets para outros serviços do Azure para automatizar tarefas complexas em serviços do Azure e sistemas de terceiros.

## <a name="next-steps"></a>Próximas etapas
Agora que você aprendeu os fundamentos da Automação do Azure e como ela pode ser usada para gerenciar máquinas virtuais do Azure, saiba mais:

* [Visão geral da Automação](../automation/automation-intro.md)
* [Meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizagem de Automação do Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)




<!--HONumber=Nov16_HO3-->


