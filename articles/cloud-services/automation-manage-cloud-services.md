---
title: Gerenciar os Serviços de Nuvem do Azure usando a Automação do Azure | Microsoft Docs
description: Saiba mais sobre como o serviço de Automação do Azure pode ser usado para gerenciar serviços de nuvem do Azure em grande escala.
services: cloud-services, automation
documentationcenter: ''
author: jodoglevy
manager: timlt
editor: ''

ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2016
ms.author: jolevy

---
# Gerenciamento de Serviços de Nuvem do Azure usando a Automação do Azure
Este guia apresentará o serviço de Automação do Azure e como ele pode ser usado para simplificar o gerenciamento de seus serviços de nuvem do Azure.

## O que é Automação do Azure?
[Automação do Azure](https://azure.microsoft.com/services/automation/) é um serviço do Azure para simplificar o gerenciamento de nuvem por meio da automação de processo. Com a Automação do Azure, tarefas de execução longa, manuais, propensas a erros e repetidas com frequência podem ser automatizadas para melhorar a confiabilidade, a eficiência e o tempo de implantação para sua organização.

A Automação do Azure fornece um mecanismo de execução de fluxo de trabalho altamente confiável e altamente disponível que pode ser dimensionado para atender às suas necessidades à medida que sua organização cresce. Na Automação do Azure, os processos podem ser inicializados manualmente por sistemas de terceiros ou a intervalos agendados para que as tarefas aconteçam exatamente quando necessário.

Reduza o custo operacional e libere a equipe de TI/ DevOps para se concentrar no trabalho que adiciona valor comercial ao transferir as tarefas de gerenciamento de nuvem para serem executadas automaticamente pela Automação do Azure.

## Como a Automação do Azure ajuda a gerenciar os serviços de nuvem do Azure?
Os serviços de nuvem do Azure podem ser gerenciados na Automação do Azure usando os cmdlets do PowerShell disponíveis em [Ferramentas PowerShell do Azure](https://msdn.microsoft.com/library/azure/jj156055.aspx). A Automação do Azure tem esses cmdlets do PowerShell do serviço de nuvem disponíveis imediatamente para que você possa executar todas as tarefas de gerenciamento do serviço de nuvem no serviço. Você também pode combinar esses cmdlets na Automação do Azure com os cmdlets para outros serviços do Azure, para automatizar tarefas complexas nos serviços do Azure e sistemas de terceiros.

Entre alguns usos de exemplo da Automação do Azure para gerenciar os Serviços de Nuvem do Azure estão:

* [Implantação contínua de um Serviço de Nuvem sempre que cscfg ou cspkg é atualizado no Armazenamento de Blobs do Azure](https://gallery.technet.microsoft.com/scriptcenter/Continuous-Deployment-of-A-eeebf3a6)
* [Reinicialização das instâncias do Serviço de Nuvem em paralelo, com um domínio de atualização por vez](https://gallery.technet.microsoft.com/scriptcenter/Reboot-Cloud-Service-PaaS-b337a06d)

## Próximas etapas
Agora que você aprendeu os fundamentos de Automação do Azure e como ela pode ser usada para serviços de nuvem do Azure, siga estes links para obter mais informações sobre a Automação do Azure.

* [Visão geral da Automação](../automation/automation-intro.md)
* [Meu primeiro runbook](../automation/automation-first-runbook-graphical.md)
* [Mapa de aprendizagem da Automação do Azure](https://azure.microsoft.com/documentation/learning-paths/automation/)

<!---HONumber=AcomDC_0622_2016-->