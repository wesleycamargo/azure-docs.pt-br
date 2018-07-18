---
title: Lista de Eventos do Azure Service Fabric | Microsoft Docs
description: Lista abrangente dos eventos fornecida pelo Azure Service Fabric para ajudar a monitorar os clusters.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: reference
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/25/2018
ms.author: dekapur
ms.openlocfilehash: 529df0147d2563c62c4a9578e47184bd98b01761
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 05/16/2018
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos do Service Fabric 

O Service Fabric expõe um conjunto primário de eventos de cluster para informar a você sobre o status do seu cluster como [Eventos do Service Fabric](service-fabric-diagnostics-events.md). Eles são baseados em ações realizadas pelo Service Fabric nos seus nós e no seu cluster ou decisões de gerenciamento tomadas por um proprietário/operador do cluster. Estes eventos podem ser acessados consultando a [EventStore](service-fabric-diagnostics-eventstore.md) no seu cluster ou através do canal operacional. Nos computadores Windows, o canal operacional também está ligado ao EventLog - então você pode ver os Eventos do Service Fabric no Visualizador de Eventos. 

>[!NOTE]
>Para ver uma lista dos eventos do Service Fabric para clusters nas versões anteriores à versão 6.2, consulte a seção a seguir. 

Aqui está uma lista de todos os eventos disponíveis na plataforma, classificados pela entidade para a qual eles fazem o mapeamento.

## <a name="cluster-events"></a>Eventos do cluster

**Eventos de atualização do cluster**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStartOperational | CM | Informativo | 1 |
| 29628 | ClusterUpgradeCompleteOperational | CM | Informativo | 1 |
| 29629 | ClusterUpgradeRollbackStartOperational | CM | Informativo | 1 |
| 29630 | ClusterUpgradeRollbackCompleteOperational | CM | Informativo | 1 |
| 29631 | ClusterUpgradeDomainCompleteOperational | CM | Informativo | 1 |

**Eventos de relatório de integridade do cluster**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 54428 | ProcessClusterReportOperational | HM | Informativo | 1 |
| 54437 | ExpiredClusterEventOperational | HM | Informativo | 1 |

**Eventos de serviço de caos**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 50021 | ChaosStartedEvent | Capacidade de teste | Informativo | 1 |
| 50023 | ChaosStoppedEvent | Capacidade de teste | Informativo | 1 |

## <a name="node-events"></a>Eventos de nó

**Eventos de ciclo de vida do nó** 

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 18602 | DeactivateNodeCompletedOperational | FM | Informativo | 1 |
| 18603 | NodeUpOperational | FM | Informativo | 1 |
| 18604 | NodeDownOperational | FM | Informativo | 1 |
| 18605 | NodeAddedOperational | FM | Informativo | 1 |
| 18606 | NodeRemovedOperational | FM | Informativo | 1 |
| 18607 | DeactivateNodeStartOperational | FM | Informativo | 1 |
| 25620 | NodeOpening | FabricNode | Informativo | 1 |
| 25621 | NodeOpenedSuccess | FabricNode | Informativo | 1 |
| 25622 | NodeOpenedFailed | FabricNode | Informativo | 1 |
| 25623 | NodeClosing | FabricNode | Informativo | 1 |
| 25624 | NodeClosed | FabricNode | Informativo | 1 |
| 25625 | NodeAborting | FabricNode | Informativo | 1 |
| 25626 | NodeAborted | FabricNode | Informativo | 1 |

**Eventos de relatório de integridade do nó**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 54423 | ProcessNodeReportOperational | HM | Informativo | 1 |
| 54432 | ExpiredNodeEventOperational | HM | Informativo | 1 |

**Eventos de nó de caos**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 50033 | ChaosRestartNodeFaultScheduledEvent | Capacidade de teste | Informativo | 1 |
| 50087 | ChaosRestartNodeFaultCompletedEvent | Capacidade de teste | Informativo | 1 |

## <a name="application-events"></a>Eventos de aplicativo

**Eventos do ciclo de vida do aplicativo**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 29620 | ApplicationCreatedOperational | CM | Informativo | 1 |
| 29625 | ApplicationDeletedOperational | CM | Informativo | 1 |
| 23083 | ProcessExitedOperational | Hosting | Informativo | 1 |

**Eventos de atualização do aplicativo**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 29621 | ApplicationUpgradeStartOperational | CM | Informativo | 1 |
| 29622 | ApplicationUpgradeCompleteOperational | CM | Informativo | 1 |
| 29623 | ApplicationUpgradeRollbackStartOperational | CM | Informativo | 1 |
| 29624 | ApplicationUpgradeRollbackCompleteOperational | CM | Informativo | 1 |
| 29626 | ApplicationUpgradeDomainCompleteOperational | CM | Informativo | 1 |

**Eventos de relatório de integridade do aplicativo**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 54425 | ProcessApplicationReportOperational | HM | Informativo | 1 |
| 54426 | ProcessDeployedApplicationReportOperational | HM | Informativo | 1 |
| 54427 | ProcessDeployedServicePackageReportOperational | HM | Informativo | 1 |
| 54434 | ExpiredApplicationEventOperational | HM | Informativo | 1 |
| 54435 | ExpiredDeployedApplicationEventOperational | HM | Informativo | 1 |
| 54436 | ExpiredDeployedServicePackageEventOperational | HM | Informativo | 1 |

**Eventos de aplicativo de caos**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 50053 | ChaosRestartCodePackageFaultScheduledEvent | Capacidade de teste | Informativo | 1 |
| 50101 | ChaosRestartCodePackageFaultCompletedEvent | Capacidade de teste | Informativo | 1 |

## <a name="service-events"></a>Eventos de serviço

**Eventos de ciclo de vida do serviço**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 18602 | ServiceCreatedOperational | FM | Informativo | 1 |
| 18658 | ServiceDeletedOperational | FM | Informativo | 1 |

**Eventos de relatório de integridade do serviço**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 54424 | ProcessServiceReportOperational | HM | Informativo | 1 |
| 54433 | ExpiredServiceEventOperational | HM | Informativo | 1 |

## <a name="partition-events"></a>Eventos de partição

**Eventos de movimentação de partição**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 18940 | ReconfigurationCompleted | RA | Informativo | 1 |

**Eventos de relatório de integridade da partição**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 54422 | ProcessPartitionReportOperational | HM | Informativo | 1 |
| 54431 | ExpiredPartitionEventOperational | HM | Informativo | 1 |

**Eventos de partição de caos**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 50069 | ChaosMovePrimaryFaultScheduledEvent | Capacidade de teste | Informativo | 1 |
| 50077 | ChaosMoveSecondaryFaultScheduledEvent | Capacidade de teste | Informativo | 1 |

**Eventos de análise de partição**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 65003 | PrimaryMoveAnalysisEvent | Capacidade de teste | Informativo | 1 |

## <a name="replica-events"></a>Eventos de réplica

**Eventos de relatório de integridade da réplica**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 54429 | ProcessStatefulReplicaReportOperational | HM | Informativo | 1 |
| 54430 | ProcessStatelessInstanceReportOperational | HM | Informativo | 1 |
| 54438 | ExpiredStatefulReplicaEventOperational | HM | Informativo | 1 |
| 54439 | ExpiredStatelessInstanceEventOperational | HM | Informativo | 1 |

**Eventos de réplica de caos**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 50047 | ChaosRestartReplicaFaultScheduledEvent | Capacidade de teste | Informativo | 1 |
| 50051 | ChaosRemoveReplicaFaultScheduledEvent | Capacidade de teste | Informativo | 1 |
| 50093 | ChaosRemoveReplicaFaultCompletedEvent | Capacidade de teste | Informativo | 1 |

## <a name="container-events"></a>Eventos de contêiner

**Eventos de ciclo de vida do contêiner** 

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 23074 | ContainerActivatedOperational | Hosting | Informativo | 1 |
| 23075 | ContainerDeactivatedOperational | Hosting | Informativo | 1 |
| 23082 | ContainerExitedOperational | Hosting | Informativo | 1 |

## <a name="other-events"></a>Outros eventos

**Eventos de correlação**

| EventId | NOME | Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- |
| 65011 | CorrelationOperational | Capacidade de teste | Informativo | 1 |

## <a name="events-prior-to-version-62"></a>Eventos anteriores à versão 6.2

Esta é uma lista abrangente dos eventos fornecidos pelo Service Fabric antes da versão 6.2.

| EventId | NOME | Origem (Tarefa) | Nível |
| --- | --- | --- | --- |
| 25620 | NodeOpening | FabricNode | Informativo |
| 25621 | NodeOpenedSuccess | FabricNode | Informativo |
| 25622 | NodeOpenedFailed | FabricNode | Informativo |
| 25623 | NodeClosing | FabricNode | Informativo |
| 25624 | NodeClosed | FabricNode | Informativo |
| 25625 | NodeAborting | FabricNode | Informativo |
| 25626 | NodeAborted | FabricNode | Informativo |
| 29627 | ClusterUpgradeStart | CM | Informativo |
| 29628 | ClusterUpgradeComplete | CM | Informativo |
| 29629 | ClusterUpgradeRollback | CM | Informativo |
| 29630 | ClusterUpgradeRollbackComplete | CM | Informativo |
| 29631 | ClusterUpgradeDomainComplete | CM | Informativo |
| 23074 | ContainerActivated | Hosting | Informativo |
| 23075 | ContainerDeactivated | Hosting | Informativo |
| 29620 | ApplicationCreated | CM | Informativo |
| 29621 | ApplicationUpgradeStart | CM | Informativo |
| 29622 | ApplicationUpgradeComplete | CM | Informativo |
| 29623 | ApplicationUpgradeRollback | CM | Informativo |
| 29624 | ApplicationUpgradeRollbackComplete | CM | Informativo |
| 29625 | ApplicationDeleted | CM | Informativo |
| 29626 | ApplicationUpgradeDomainComplete | CM | Informativo |
| 18566 | ServiceCreated | FM | Informativo |
| 18567 | ServiceDeleted | FM | Informativo |

## <a name="next-steps"></a>Próximas etapas

* Saiba mais sobre a [geração de eventos geral no nível de plataforma](service-fabric-diagnostics-event-generation-infra.md) do Service Fabric
* Modificando a configuração do [Diagnóstico do Azure](service-fabric-diagnostics-event-aggregation-wad.md) para coletar mais logs
* [Configurando o Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) para ver os logs do Canal operacional
