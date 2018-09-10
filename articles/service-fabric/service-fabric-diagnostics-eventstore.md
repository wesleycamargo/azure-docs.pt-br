---
title: EventStore do Azure Service Fabric | Microsoft Docs
description: Saiba mais sobre o EventStore do Azure Service Fabric
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 1d235d5a75975c8d58cad1bbde0f16df2b1b3e7a
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206509"
---
# <a name="eventstore-service-overview"></a>Visão geral do serviço EventStore

>[!NOTE]
>A partir da versão 6.2 do Service Fabric. As APIs do EventStore estão atualmente em versão prévia para clusters do Windows somente em execução no Azure. Estamos trabalhando mover essa funcionalidade para Linux, assim como para nossos clusters independentes.

## <a name="overview"></a>Visão geral

Apresentado na versão 6.2, o serviço EventStore é uma opção de monitoramento no Service Fabric que fornece uma maneira para entender o estado do cluster ou cargas de trabalho em um determinado ponto no tempo. O serviço EventStore expõe eventos do Service Fabric por meio de APIs dos quais você pode fazer chamadas. Essas APIs do EventStore permitem que você consulte o cluster diretamente para obter dados de diagnóstico em qualquer entidade no cluster e devem ser usadas para ajudar a:
* Diagnosticar problemas em desenvolvimento ou teste, ou em que talvez você possa estar usando um pipeline de monitoramento
* Confirmar que ações de gerenciamento que você está realizando no cluster estão sendo processadas corretamente por ele

Para ver uma lista completa de eventos disponíveis no EventStore, consulte [Eventos do Service Fabric](service-fabric-diagnostics-event-generation-operational.md).

O serviço EventStore pode ser consultado para eventos que estão disponíveis para cada entidade e tipo de entidade em seu cluster. Isso significa que você pode consultar eventos nos seguintes níveis:
* Cluster: todos os eventos no nível do cluster
* Nós: eventos todos os eventos no nível do nó
* Nó: eventos específicos a um nó, com base em `nodeName`
* Aplicativos: todos os eventos no nível do aplicativo
* Aplicativo: eventos específicos para um aplicativo
* Serviços: eventos de todos os serviços em seus clusters
* Serviço: eventos de um serviço específico
* Partições: eventos de todas as partições
* Partição: eventos de uma partição específica
* Réplicas: eventos de todas as réplicas/instâncias
* Réplica: eventos de uma réplica/instância específica


O serviço EventStore também tem a capacidade de correlacionar eventos em seu cluster. Examinando os eventos que foram gravados ao mesmo tempo de diferentes entidades que podem ter se afetado entre si, o serviço EventStore é capaz de vincular esses eventos para ajudar a identificar as causas de atividades em seu cluster. Por exemplo, caso um de seus aplicativos se tornar não íntegro sem quaisquer alterações induzidas, o EventStore também examinará outros eventos expostos pela plataforma e poderá correlacionar isso a um evento `NodeDown`. Isso ajuda com detecção de falha e análise de causas raiz mais rápidas.

Para começar a usar o serviço EventStore, consulte [Consultar APIs do EventStore para eventos de cluster](service-fabric-diagnostics-eventstore-query.md).

## <a name="next-steps"></a>Próximas etapas
* Visão geral do monitoramento e diagnóstico no Service Fabric - [Monitoramento e Diagnóstico para o Service Fabric](service-fabric-diagnostics-overview.md)
* Saiba mais sobre o monitoramento de seu cluster - [Monitoramento do cluster e da plataforma](service-fabric-diagnostics-event-generation-infra.md).