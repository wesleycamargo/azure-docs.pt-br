---
title: Visão geral do monitoramento dos contêineres do Azure | Microsoft Docs
description: Este artigo fornece uma visão geral dos diferentes métodos disponíveis no Azure para monitorar os contêineres no Azure para compreender rapidamente a disponibilidade e integridade de um cluster.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: magoedte
ms.openlocfilehash: d137576b4beb5cf36dce99ffb1869049f37b60b2
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/19/2019
ms.locfileid: "57881258"
---
# <a name="overview-of-monitoring-containers-in-azure"></a>Visão geral do monitoramento dos contêineres no Azure
Com o Azure, você pode, efetivamente, monitorar e gerenciar suas cargas de trabalho implantadas em contêineres do Azure executando Kubernetes ou Docker. É importante entender como os contêineres com vários aplicativos de microsserviço são executados para prestar um serviço confiável em grande escala e para suporte do seu plano de monitoramento. Este artigo fornece uma visão geral dos recursos de gerenciamento e monitoramento no Azure para ajudar a entender cada um deles e quais são apropriados dependendo dos seus requisitos.

Usando o [Azure Monitor para contêineres](container-insights-overview.md), é possível exibir rapidamente o desempenho e a integridade da infraestrutura de contêineres do Linux e investigar os problemas com agilidade. A telemetria é armazenada em um espaço de trabalho do Log Analytics e integrada ao portal do Azure, onde você pode explorar, filtrar e segmentar dados agregados com painéis para ficar de olho na carga, integridade e desempenho.  

Para contêineres em execução fora do serviço hospedado do Azure Kubernetes, a [solução de contêiner do Docker e do Windows](../../azure-monitor/insights/containers.md) do Log Analytics ajuda você a exibir e gerenciar seus hosts de contêiner do Windows e do Docker. No espaço de trabalho do Log Analytics, você pode exibir detalhes do inventário, o desempenho e eventos de nós e contêineres no ambiente. Você pode exibir informações detalhadas de auditoria, mostrando os comandos usados com contêineres e você pode solucionar problemas de contêineres exibindo e pesquisando logs centralizados sem a necessidade de acessar remotamente os hosts do Docker ou do Windows.

Para conseguir um monitoramento holístico ou de ponta a ponta do aplicativo, qualquer dependência, seja um recurso local ou no Azure, deve ser monitorada com o Azure Monitor ou Log Analytics.  A camada de aplicativo deve ser incluída para adicionar uma camada adicional de reconhecimento de integridade, tanto no nível de plataforma como no nível de aplicativos usando o Application Insights. No nível da plataforma, há SDKs do Application Insights para [Kubernetes]( https://github.com/Microsoft/ApplicationInsights-Kubernetes), [Docker](https://hub.docker.com/r/microsoft/applicationinsights/) e [Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-analysis-appinsights). Para aplicativos de microsserviço, há suporte para [Java](../../azure-monitor/app/java-get-started.md), [Node. js](../../azure-monitor/learn/nodejs-quick-start.md), [.NET](../../azure-monitor/app/asp-net.md), [.NET Core](../../azure-monitor/app/asp-net-core.md), bem como um número de outros [idiomas/estruturas](../../azure-monitor/app/platforms.md). 

Caso contrário, os problemas que podem afetar a disponibilidade do aplicativo não serão identificados e as metas de nível de serviço não serão atendidas.  
