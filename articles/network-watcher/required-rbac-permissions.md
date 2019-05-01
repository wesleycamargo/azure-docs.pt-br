---
title: Permissões necessárias para usar os recursos do Observador de Rede do Azure | Microsoft Docs
description: Saiba quais permissões de controle de acesso baseadas em função do Azure são necessárias para trabalhar com os recursos do Observador de Rede.
services: network-watcher
documentationcenter: ''
author: KumudD
manager: twooley
editor: ''
ms.assetid: ''
ms.service: network-watcher
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2018
ms.author: kumud
ms.openlocfilehash: 8c8fe6125d9c638fedadc3d299ff0ac0d601fd61
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/28/2019
ms.locfileid: "64685690"
---
# <a name="role-based-access-control-permissions-required-to-use-network-watcher-capabilities"></a>As permissões de controle de acesso baseadas em função são necessárias para usar os recursos do Observador de Rede.

O controle de acesso baseado em função (RBAC) do Azure permite que você atribua apenas as ações específicas aos membros de sua organização que eles precisam para concluir suas responsabilidades atribuídas. Para usar recursos do Observador de Rede, a conta com a qual você faz logon no Azure deve ser atribuída às funções internas de [Proprietário](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#owner), [Colaborador](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#contributor) ou [Colaborador de rede](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#network-contributor) ou atribuída a uma [função personalizada](../role-based-access-control/custom-roles.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) que recebe as ações listadas para cada recurso do Observador de Rede nas seções a seguir. Para saber mais sobre os recursos do Observador de Rede, consulte [O que é o Observador de Rede?](network-watcher-monitoring-overview.md).

## <a name="network-watcher"></a>Observador de Rede

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/read                              | Obter um observador de rede                                          |
| Microsoft.Network/networkWatchers/write                             | Criar ou atualizar um observador de rede                             |
| Microsoft.Network/networkWatchers/delete                            | Excluir um observador de rede                                       |

## <a name="nsg-flow-logs"></a>Logs de fluxo NSG

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/configureFlowLog/action           | Configurar um log de fluxo                                           |
| Microsoft.Network/networkWatchers/queryFlowLogStatus/action         | Consultar o status de um log de fluxo                                    |

## <a name="connection-troubleshoot"></a>Solução de problemas na conexão

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectivityCheck/action          | Iniciar um teste de solução de problemas de conexão
| Microsoft.Network/networkWatchers/queryTroubleshootResult/action    | Consultar resultados de um teste de solução de problemas de conexão                |
| Microsoft.Network/networkWatchers/troubleshoot/action               | Executar um teste de solução de problemas de conexão                             |

## <a name="connection-monitor"></a>Monitor de conexão

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/connectionMonitors/start/action   | Iniciar um monitor de conexão                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/stop/action    | Parar um monitor de conexão                                      |
| Microsoft.Network/networkWatchers/connectionMonitors/query/action   | Consultar um monitor de conexão                                     |
| Microsoft.Network/networkWatchers/connectionMonitors/read           | Obter um monitor de conexão                                       |
| Microsoft.Network/networkWatchers/connectionMonitors/write          | Criar um monitor de conexão                                    |
| Microsoft.Network/networkWatchers/connectionMonitors/delete         | Excluir um monitor de conexão                                    |

## <a name="packet-capture"></a>Captura de pacotes

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/packetCaptures/queryStatus/action | Consultar o status de uma captura de pacotes                           |
| Microsoft.Network/networkWatchers/packetCaptures/stop/action        | Parar uma captura de pacotes                                          |
| Microsoft.Network/networkWatchers/packetCaptures/read               | Obter uma captura de pacote                                           |
| Microsoft.Network/networkWatchers/packetCaptures/write              | Criar uma captura de pacotes                                        |
| Microsoft.Network/networkWatchers/packetCaptures/delete             | Excluir uma captura de pacotes                                        |

## <a name="ip-flow-verify"></a>Verificação de fluxo de IP

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/ipFlowVerify/action               | Verificar um fluxo IP                                              |

## <a name="next-hop"></a>Próximo salto

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/nextHop/action                    | Obter o próximo salto de uma VM                                     |

## <a name="network-security-group-view"></a>Exibição de grupo de segurança de rede

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/securityGroupView/action          | Exibir grupos de segurança                                           |

## <a name="topology"></a>Topologia

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/topology/action                   | Obter topologia                                                   |

## <a name="reachability-report"></a>Relatório de acessibilidade

| Ação                                                              | NOME                                                           |
| ---------                                                           | -------------                                                  |
| Microsoft.Network/networkWatchers/azureReachabilityReport/action    | Obter um relatório de acessibilidade do Azure                               |

## <a name="additional-actions"></a>Ações adicionais

Os recursos do Observador de Rede também requerem as seguintes ações:

- Microsoft.Authorization/\*/Read
- Microsoft.Resources/subscriptions/resourceGroups/Read
- Microsoft.Storage/storageAccounts/Read
- Microsoft.Storage/storageAccounts/listServiceSas/Action
- Microsoft.Storage/storageAccounts/listAccountSas/Action
- Microsoft.Storage/storageAccounts/listKeys/Action
- Microsoft.Compute/virtualMachines/Read
- Microsoft.Compute/virtualMachines/Write
- Microsoft.Compute/virtualMachines/extensions/Read
- Microsoft.Compute/virtualMachines/extensions/Write
- Microsoft.Compute/virtualMachineScaleSets/Read
- Microsoft.Compute/virtualMachineScaleSets/Write
- Microsoft.Compute/virtualMachineScaleSets/extensions/Read
- Microsoft.Compute/virtualMachineScaleSets/extensions/Write
- Microsoft.Insights/alertRules/*
- Microsoft.Support/*
