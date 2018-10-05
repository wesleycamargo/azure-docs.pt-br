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
ms.openlocfilehash: da4a779bca806fe6aa392db96eafc6c20f8ddcf6
ms.sourcegitcommit: 5b8d9dc7c50a26d8f085a10c7281683ea2da9c10
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/26/2018
ms.locfileid: "47182154"
---
# <a name="list-of-service-fabric-events"></a>Lista de eventos do Service Fabric 

O Service Fabric expõe um conjunto primário de eventos de cluster para informar a você sobre o status do seu cluster como [Eventos do Service Fabric](service-fabric-diagnostics-events.md). Eles são baseados em ações realizadas pelo Service Fabric nos seus nós e no seu cluster ou decisões de gerenciamento tomadas por um proprietário/operador do cluster. Estes eventos podem ser acessados consultando a [EventStore](service-fabric-diagnostics-eventstore.md) no seu cluster ou através do canal operacional. Nos computadores Windows, o canal operacional também está ligado ao EventLog - então você pode ver os Eventos do Service Fabric no Visualizador de Eventos. 

>[!NOTE]
>Para ver uma lista dos eventos do Service Fabric para clusters nas versões anteriores à versão 6.2, consulte a seção a seguir. 

Aqui está uma lista de todos os eventos disponíveis na plataforma, classificados pela entidade para a qual eles fazem o mapeamento.

## <a name="cluster-events"></a>Eventos do cluster

**Eventos de atualização do cluster**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- | --- |
| 29627 | ClusterUpgradeStarted | Uma atualização de cluster foi iniciada | CM | Informativo | 1 |
| 29628 | ClusterUpgradeCompleted | Uma atualização de cluster foi concluída| CM | Informativo | 1 |
| 29629 | ClusterUpgradeRollbackStarted | A reversão de uma atualização de cluster foi iniciada | CM | Informativo | 1 |
| 29630 | ClusterUpgradeRollbackCompleted | A reversão de uma atualização de cluster foi concluída | CM | Informativo | 1 |
| 29631 | ClusterUpgradeDomainCompleted | Uma atualização de domínio foi concluída durante uma atualização de cluster | CM | Informativo | 1 |

## <a name="node-events"></a>Eventos de nó

**Eventos de ciclo de vida do nó** 

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 18602 | NodeDeactivateCompleted | A desativação de um nó foi concluída | FM | Informativo | 1 |
| 18603 | NodeUp | O cluster detectou que um nó foi iniciado | FM | Informativo | 1 |
| 18604 | NodeDown | O cluster detectou que um nó foi encerrado |  FM | Informativo | 1 |
| 18605 | NodeAddedToCluster | Um novo nó foi adicionado ao cluster | FM | Informativo | 1 |
| 18606 | NodeRemovedFromCluster | Um nó foi removido do cluster | FM | Informativo | 1 |
| 18607 | NodeDeactivateStarted | A desativação de um nó foi iniciada | FM | Informativo | 1 |
| 25620 | NodeOpening | Um nó está sendo iniciado. Primeira etapa do ciclo de vida do nó | FabricNode | Informativo | 1 |
| 25621 | NodeOpenSucceeded | O nó foi iniciado com êxito | FabricNode | Informativo | 1 |
| 25622 | NodeOpenFailed | Falha ao iniciar um nó | FabricNode | Informativo | 1 |
| 25623 | NodeClosing | Um nó está sendo desligado. Início da etapa final do ciclo de vida do nó | FabricNode | Informativo | 1 |
| 25624 | NodeClosed | Um nó foi desligado com êxito | FabricNode | Informativo | 1 |
| 25625 | NodeAborting | Um nó está começando a ser desligado de maneira brusca | FabricNode | Informativo | 1 |
| 25626 | NodeAborted | Um nó foi desligado de maneira brusca | FabricNode | Informativo | 1 |

## <a name="application-events"></a>Eventos de aplicativo

**Eventos do ciclo de vida do aplicativo**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 29620 | ApplicationCreated | Um novo aplicativo foi criado | CM | Informativo | 1 |
| 29625 | ApplicationDeleted | Um aplicativo existente foi excluído | CM | Informativo | 1 |
| 23083 | ApplicationProcessExited | Um processo dentro de um aplicativo foi encerrado | Hosting | Informativo | 1 |

**Eventos de atualização do aplicativo**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 29621 | ApplicationUpgradeStarted | A atualização de um aplicativo foi iniciada | CM | Informativo | 1 |
| 29622 | ApplicationUpgradeCompleted | A atualização de um aplicativo foi concluída | CM | Informativo | 1 |
| 29623 | ApplicationUpgradeRollbackStarted | A reversão de uma atualização de aplicativo foi iniciada |CM | Informativo | 1 |
| 29624 | ApplicationUpgradeRollbackCompleted | A reversão de uma atualização de aplicativo foi concluída | CM | Informativo | 1 |
| 29626 | ApplicationUpgradeDomainCompleted | Uma atualização de domínio foi concluída durante uma atualização de aplicativo | CM | Informativo | 1 |

## <a name="service-events"></a>Eventos de serviço

**Eventos de ciclo de vida do serviço**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 18657 | ServiceCreated | Um novo serviço foi criado | FM | Informativo | 1 |
| 18658 | ServiceDeleted | Um serviço existente foi excluído | FM | Informativo | 1 |

## <a name="partition-events"></a>Eventos de partição

**Eventos de movimentação de partição**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 18940 | PartitionReconfigured | Uma reconfiguração de partição foi concluída | RA | Informativo | 1 |

## <a name="container-events"></a>Eventos de contêiner

**Eventos de ciclo de vida do contêiner** 

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 23074 | ContainerActivated | Um contêiner for iniciado | Hosting | Informativo | 1 |
| 23075 | ContainerDeactivated | Um contêiner for encerrado | Hosting | Informativo | 1 |
| 23082 | ContainerExited | Um contêiner foi fechado – verifique o sinalizador UnexpectedTermination | Hosting | Informativo | 1 |

## <a name="health-reports"></a>Relatórios de integridade

**Eventos de relatório de integridade do cluster**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | --- | --- | --- | --- |
| 54428 | ClusterNewHealthReport | Um novo relatório de integridade do cluster está disponível | HM | Informativo | 1 |
| 54437 | ClusterHealthReportExpired | Um relatório de integridade de cluster existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade do nó**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54423 | NodeNewHealthReport | Um novo relatório de integridade do nó está disponível | HM | Informativo | 1 |
| 54432 | NodeHealthReportExpired | Um relatório de integridade do nó existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade do aplicativo**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54425 | ApplicationNewHealthReport | Um novo relatório de integridade do aplicativo foi criado. É referente a aplicativos não implantados. | HM | Informativo | 1 |
| 54426 | DeployedApplicationNewHealthReport | Um novo relatório de integridade do aplicativo implantado foi criado | HM | Informativo | 1 |
| 54427 | DeployedServicePackageNewHealthReport | Um novo relatório de integridade do serviço implantado foi criado | HM | Informativo | 1 |
| 54434 | ApplicationHealthReportExpired | Um relatório de integridade do aplicativo existente expirou | HM | Informativo | 1 |
| 54435 | DeployedApplicationHealthReportExpired | Um relatório de integridade do aplicativo implantado existente expirou | HM | Informativo | 1 |
| 54436 | DeployedServicePackageHealthReportExpired | Um relatório de integridade do serviço implantado existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade do serviço**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54424 | ServiceNewHealthReport | Um novo relatório de integridade do serviço foi criado | HM | Informativo | 1 |
| 54433 | ServiceHealthReportExpired | Um relatório de integridade do serviço existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade da partição**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54422 | PartitionNewHealthReport | Um novo relatório de integridade da partição foi criado | HM | Informativo | 1 |
| 54431 | PartitionHealthReportExpired | Um relatório de integridade da partição existente expirou | HM | Informativo | 1 |

**Eventos de relatório de integridade da réplica**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 54429 | StatefulReplicaNewHealthReport | Um relatório de integridade da réplica com estado foi criado | HM | Informativo | 1 |
| 54430 | StatelessInstanceNewHealthReport | Um novo relatório de integridade da réplica sem estado foi criado | HM | Informativo | 1 |
| 54438 | StatefulReplicaHealthReportExpired | Um relatório de integridade de réplica com estado existente expirou | HM | Informativo | 1 |
| 54439 | StatelessInstanceHealthReportExpired | Um relatório de integridade de instância sem estado existente expirou | HM | Informativo | 1 |

## <a name="chaos-testing-events"></a>Eventos de teste do caos 

**Eventos de sessão de caos**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50021 | ChaosStarted | Uma sessão de teste do caos foi iniciada | Capacidade de teste | Informativo | 1 |
| 50023 | ChaosStopped | Uma sessão de teste do caos foi interrompida | Capacidade de teste | Informativo | 1 |

**Eventos de nó de caos**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50033 | ChaosNodeRestartScheduled | Um nó foi agendado para ser reiniciado como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50087 | ChaosNodeRestartCompleted | A reinicialização de um nó foi concluída como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |

**Eventos de aplicativo de caos**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50053 | ChaosCodePackageRestartScheduled | Uma reinicialização do pacote de códigos foi agendada durante um sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50101 | ChaosCodePackageRestartCompleted | Uma reinicialização do pacote de códigos foi concluída durante um sessão de teste do caos | Capacidade de teste | Informativo | 1 |

**Eventos de partição de caos**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50069 | ChaosPartitionPrimaryMoveScheduled | Uma partição primária foi agendada para ser movida como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50077 | ChaosPartitionSecondaryMoveScheduled | Uma partição secundária foi agendada para ser movida como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 65003 | PartitionPrimaryMoveAnalysis | Uma análise mais profunda do movimenta da partição primária está disponível | Capacidade de teste | Informativo | 1 |

**Eventos de réplica de caos**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 50047 | ChaosReplicaRestartScheduled | Uma reinicialização de réplica foi agendada como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50051 | ChaosReplicaRemovalScheduled | Uma remoção de réplica foi agendada como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |
| 50093 | ChaosReplicaRemovalCompleted | Uma remoção de réplica foi concluída como parte de uma sessão de teste do caos | Capacidade de teste | Informativo | 1 |

## <a name="other-events"></a>Outros eventos

**Eventos de correlação**

| EventId | NOME | DESCRIÇÃO |Origem (Tarefa) | Nível | Versão |
| --- | --- | ---| --- | --- | --- |
| 65011 | CorrelationOperational | Uma correlação foi detectada | Capacidade de teste | Informativo | 1 |

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
