---
title: Melhores práticas de monitoramento do Azure Service Fabric | Microsoft Docs
description: Melhores práticas de monitoramento de clusters e aplicativos do Service Fabric.
services: service-fabric
documentationcenter: .net
author: peterpogorski
manager: jeanpaul.connock
editor: ''
ms.assetid: 19ca51e8-69b9-4952-b4b5-4bf04cded217
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: 5e3cfad70dd0cc0c53f6971c9ddce44f0ca25ecd
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/28/2019
ms.locfileid: "55104284"
---
# <a name="monitoring-and-diagnostics"></a>Monitoramento e diagnóstico

[O monitoramento e o diagnóstico](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-overview) são críticos para o desenvolvimento, o teste e a implantação de cargas de trabalho em qualquer ambiente de nuvem. Por exemplo, você pode controlar como seus aplicativos são usados, as ações realizadas pela plataforma do Service Fabric, a utilização de recursos com contadores de desempenho e a integridade geral do seu cluster. Você pode usar essas informações para diagnosticar e corrigir problemas e evitar que eles ocorram no futuro.

## <a name="application-monitoring"></a>Monitoramento de aplicativo

O monitoramento de aplicativo controla como os recursos e componentes de um aplicativo estão sendo usados. Monitore seus aplicativos para garantir que os problemas que afetam os usuários sejam capturados. O monitoramento de aplicativo é a responsabilidade daqueles que desenvolvem o aplicativo e seus serviços, pois depende exclusivamente da lógica de negócios do aplicativo. É recomendável que você configure o monitoramento de aplicativo com o [Application Insights](https://docs.microsoft.com/azure/service-fabric/service-fabric-tutorial-monitoring-aspnet), a ferramenta de monitoramento de aplicativo do Azure.

## <a name="cluster-monitoring"></a>Monitoramento do cluster

Uma das metas do Service Fabric é tornar os aplicativos resilientes a falhas de hardware. Esse objetivo é conseguido por meio da capacidade de serviços de sistema da plataforma para detectar problemas de infraestrutura e rápidas failover de cargas de trabalho para outros nós no cluster. Mas e se os próprios serviços do sistema tiverem problemas? Ou se, ao tentar implantar ou mover uma carga de trabalho, as regras para a veiculação de serviços forem violadas? O Service Fabric fornece diagnóstico para esses e outros problemas, a fim de garantir que você seja informado sobre como a plataforma Service Fabric interage com aplicativos, serviços, contêineres e nós.

Para clusters do Windows, é recomendável que você configure o monitoramento de cluster com o [Agente do Diagnostics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-event-aggregation-wad) e o [Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-setup).

Para clusters do Linux, o Log Analytics também é a ferramenta recomendada para o monitoramento da infraestrutura e da plataforma Azure. O diagnóstico da plataforma Linux exige outra configuração, conforme observado em [Eventos de cluster do Linux do Service Fabric no Syslog](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-syslog).

## <a name="infrastructure-monitoring"></a>Monitoramento da infraestrutura

O [Log Analytics](https://docs.microsoft.com/azure/service-fabric/service-fabric-diagnostics-oms-agent) é recomendado para o monitoramento de eventos no nível do cluster. Depois de configurar o agente do Log Analytics no workspace, conforme descrito no link anterior, você poderá coletar métricas de desempenho como Utilização da CPU, contadores de desempenho do .NET como utilização da CPU no nível do processo, contadores de desempenho do Service Fabric como o número de exceções de um serviço confiável e métricas de contêiner como Utilização da CPU.  Você precisará gravar os logs de contêiner no StdOut e em stderr, de modo que eles estejam disponíveis no Log Analytics.

## <a name="watchdogs"></a>Watchdogs

Em geral, um watchdog é um serviço separado que inspeciona a integridade e a carga entre serviços, executa ping em pontos de extremidade e relata eventos de integridade inesperados no cluster. Isso pode ajudar a evitar erros que podem não ser detectados com base apenas no desempenho de um único serviço. Os watchdogs também são um bom local para hospedar o código que executa ações corretivas que não exigem a interação do usuário, como limpeza de arquivos de log no armazenamento em determinados intervalos de tempo. Confira uma implementação de serviço de watchdog de exemplo em [Eventos de cluster do Linux do Service Fabric no Syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Próximas etapas

* Introdução à instrumentação de aplicativos: [Geração de eventos e logs no nível do aplicativo](service-fabric-diagnostics-event-generation-app.md).
* Siga as etapas para configurar o Application Insights para seu aplicativo com [Monitorar e diagnosticar um aplicativo ASP.NET Core no Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Saiba mais sobre como monitorar a plataforma e os eventos que o Service Fabric fornece para você: [Geração de eventos e logs no nível da plataforma](service-fabric-diagnostics-event-generation-infra.md).
* Configure a integração do Log Analytics ao Service Fabric: [Configurar o Log Analytics para um cluster](service-fabric-diagnostics-oms-setup.md)
* Saiba como configurar o Log Analytics para o monitoramento de contêineres: [Monitoramento e diagnóstico para Contêineres do Windows no Azure Service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Confira os problemas de diagnóstico de exemplo e as soluções com o Service Fabric: [Cenários comuns de diagnóstico](service-fabric-diagnostics-common-scenarios.md)
* Saiba mais sobre as recomendações gerais de monitoramento para recursos do Azure: [Melhores práticas – monitoramento e diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring).