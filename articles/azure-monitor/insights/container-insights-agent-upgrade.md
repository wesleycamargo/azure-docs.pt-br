---
title: Como Azure Monitor do Azure para contêineres (Visualizar) agente | Microsoft Docs
description: Este artigo descreve como atualizar o agente de Log Analytics usado pelo Azure Monitor para contêineres.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 57bfa47d60ffd8aa7c4240ab77f788773e426bd8
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713766"
---
# <a name="how-to-upgrade-the-azure-monitor-for-containers-preview-agent"></a>Como atualizar o Azure Monitor para o agente de contêineres (visualização)
O Azure Monitor para contêineres usa uma versão contêiner do agente do Log Analytics para Linux. Quando uma nova versão do agente é lançada, o agente não é atualizado automaticamente nos clusters do Kubernetes gerenciados hospedados no Azure Kubernetes Service (AKS).

Este artigo descreve o processo de atualização do agente.

## <a name="upgrading-agent-on-monitored-kubernetes-cluster"></a>Agente de upgrade no cluster do Kubernetes monitorado
O processo para atualizar o agente consiste em duas etapas diretas. A primeira etapa é desabilitar o monitoramento com o Azure Monitor para contêineres usando a CLI do Azure.  Siga as etapas descritas no artigo [Desativar monitoramento](container-insights-optout.md?toc=%2fazure%2fmonitoring%2ftoc.json#azure-cli). O uso da CLI do Azure nos permite remover o agente dos nós no cluster sem afetar a solução e os dados correspondentes armazenados na área de trabalho. 

>[!NOTE]
>Enquanto você está executando esta atividade de manutenção, os nós no cluster não estão encaminhando dados coletados e as visualizações de desempenho não mostrarão dados entre o momento em que você remover o agente e instalar a nova versão. 
>

Para instalar a nova versão do agente, siga as etapas descritas no artigo [Monitoramento Onboard](container-insights-onboard.md?toc=%2fazure%2fmonitoring%2ftoc.json#enable-monitoring-using-azure-cli) usando a CLI do Azure para concluir esse processo.  

Depois de reativar o monitoramento, pode levar cerca de 15 minutos até visualizar as métricas de integridade atualizadas do cluster. 

## <a name="next-steps"></a>Próximas etapas
Se você tiver problemas ao atualizar o agente, revise o [guia de solução de problemas](container-insights-troubleshoot.md) para obter suporte.