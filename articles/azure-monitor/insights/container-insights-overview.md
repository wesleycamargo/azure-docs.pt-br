---
title: Visão geral do Azure Monitor para contêineres | Microsoft Docs
description: Este artigo descreve o Azure Monitor para contêineres que monitora as soluções de Insights do Contêiner AKS e o valor que ele oferece monitorando a integridade dos clusters AKS e de Instâncias de Contêiner no Azure.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/27/2019
ms.author: magoedte
ms.openlocfilehash: a31380c8581503a340c55c374afc02c6e1fa290b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60494790"
---
# <a name="azure-monitor-for-containers-overview"></a>Visão geral do Azure Monitor para contêineres

O Azure Monitor para contêineres é um recurso projetado para monitorar o desempenho de cargas de trabalho de contêiner implantadas em Instâncias de Contêiner do Azure ou em clusters do Kubernetes gerenciado hospedados no AKS (Serviço de Kubernetes do Azure). Monitorar os contêineres é fundamental, principalmente ao executar um cluster de produção em grande escala e com vários aplicativos.

O Azure Monitor para contêineres oferece visibilidade de desempenho coletando métricas de processador e memória de controladores, nós e contêineres disponíveis no Kubernetes por meio da API de Métricas. Os logs do contêiner também são coletados.  Depois de habilitar o monitoramento nos clusters do Kubernetes, essas métricas serão coletadas automaticamente para você por meio de uma versão em contêiner do agente do Log Analytics para Linux e armazenadas no espaço de trabalho do [Log Analytics](../log-query/log-query-overview.md). 
 
## <a name="what-does-azure-monitor-for-containers-provide"></a>O que o Azure Monitor para contêineres fornece?

O Azure Monitor para contêineres inclui vários modos de exibição predefinidos que mostram as cargas de trabalho de contêiner residentes e o que afeta a integridade do desempenho do cluster do Kubernetes monitorado para que você possa:  

* Identificar contêineres AKS que estão em execução no nó e sua utilização média de processador e memória. Esse conhecimento pode ajudá-lo a identificar gargalos de recursos.
* Identifique a utilização de processador e de memória de grupos de contêineres e seus contêineres hospedados em Instâncias de Contêiner do Azure.  
* Identificar em que local o contêiner reside em um controlador ou em um pod. Esse conhecimento pode ajudá-lo a exibir o desempenho geral do controlador ou do pod.
* Examine a utilização de recursos de cargas de trabalho em execução no host não relacionadas aos processos padrão que dão suporte ao pod.
* Compreender o comportamento do cluster sob cargas mais pesadas e médias. Esse conhecimento pode ajudá-lo a identificar as necessidades de capacidade e determinar a carga máxima que o cluster pode sustentar. 

Você também pode configurar alertas para notificá-lo proativamente ou registre-o quando a utilização de CPU e memória em nós ou contêineres exceder seus limites.  

## <a name="how-do-i-access-this-feature"></a>Como acessar esse recurso?
Acesse o Azure Monitor para contêineres de duas maneiras, pelo Azure Monitor ou diretamente no cluster AKS selecionado. No Azure Monitor, você tem uma perspectiva global de todos os contêineres implantados, quais são monitorados e quais não são, permitindo que você pesquise e filtre suas assinaturas e grupos de recursos e depois faça drill no Azure Monitor para contêineres a partir do contêiner selecionado.  Caso contrário, você pode simplesmente acessar o recurso diretamente de um contêiner AKS selecionado na página do AKS.  

![Visão geral dos métodos para acessar o Azure Monitor para contêineres](./media/container-insights-overview/azmon-containers-views-1812.png)

Se você estiver interessado em monitorar e gerenciar hosts de contêiner Docker e Windows para exibir configuração, auditoria e utilização de recursos, consulte a [Solução de Monitoramento de Contêiner](../../azure-monitor/insights/containers.md).

## <a name="next-steps"></a>Próximas etapas
Para começar a monitorar seu cluster do AKS, veja [Como integrar o Azure Monitor para contêineres](container-insights-onboard.md) para entender os requisitos e os métodos disponíveis para habilitar o monitoramento.  
