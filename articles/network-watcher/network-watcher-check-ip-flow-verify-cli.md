---
title: "Verifique se o tráfego com a Verificação de Fluxo de IP do Observador de Rede do Azure - CLI do Azure | Microsoft Docs"
description: "Este artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado usando a CLI do Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 92b857ed-c834-4c1b-8ee9-538e7ae7391d
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 3c0287783e3f2b7acaeefd87acbda30885f93c22
ms.lasthandoff: 02/23/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado para ou de uma VM com IP fluxo verificar um componente do Observador de Rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)

Fluxo de IP Verifique se é um recurso do Observador de Rede que permite verificar se o tráfego é permitido para ou de uma máquina virtual. Esse cenário é útil para obter o estado atual de se uma máquina virtual pode se comunicar com um recurso externo ou um back-end. Fluxo IP, verifique se pode ser usado para verificar se as regras de grupo de segurança de rede (NSG) estão configuradas corretamente e solucionar problemas de fluxos que estão sendo bloqueados por regras NSG. Outro motivo para usar IP fluxo Verifique se é para garantir que deseja bloquear o tráfego está sendo bloqueado corretamente por NSG.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você já tiver seguido as etapas em [criar um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede ou ter uma instância existente do Gerenciador da rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

[!INCLUDE [network-watcher-preview](../../includes/network-watcher-public-preview-notice.md)]

## <a name="scenario"></a>Cenário

Esse cenário usa o IP fluxo verificar para verificar se uma máquina virtual pode se comunicar com um endereço IP do Bing conhecido. Se o tráfego é negado, ele retorna a regra de segurança que está negando esse tráfego. Para saber mais sobre IP fluxo verificar, visite [fluxo verificar visão geral de IP](network-watcher-ip-flow-verify-overview.md)


## <a name="get-a-vm"></a>Obter uma VM

Fluxo IP Verifique se o tráfego de testes para ou de um endereço IP em uma máquina virtual ou de um destino remoto. Uma Id de uma máquina virtual é necessária para o cmdlet. Se você já souber a ID da máquina virtual para usar, você pode ignorar esta etapa.

```
azure vm show -g resourceGroupName -n virtualMachineName
```

## <a name="get-the-nics"></a>Obter as NICS

O endereço IP de uma NIC na máquina virtual é necessária neste exemplo, recuperamos as NICs em uma máquina virtual. Se você já souber o endereço IP que você deseja testar na máquina virtual, você pode ignorar esta etapa.

```
azure network nic show -g resourceGroupName -n nicName
```

## <a name="run-ip-flow-verify"></a>Executar a verificação de fluxo de IP

Agora que temos as informações necessárias para executar o cmdlet, executamos o `network watcher ip-flow-verify` para testar o tráfego. Neste exemplo, estamos usando o primeiro endereço IP na primeira NIC.

```
azure network watcher ip-flow-verify -g resourceGroupName -n networkWatcherName -t targetResourceId -d directionInboundorOutbound -p protocolTCPorUDP -o localPort -m remotePort -l localIpAddr -r remoteIpAddr
```

> [!NOTE]
> Fluxo de IP verificar requer que o recurso de máquina virtual é alocado para executar.

## <a name="review-results"></a>Analisar resultados

Após a execução `network watcher ip-flow-verify` os resultados são retornados, o exemplo a seguir é os resultados retornados da etapa anterior.

```
data:    Access                          : Deny
data:    Rule Name                       : defaultSecurityRules/DefaultInboundDenyAll
info:    network watcher ip-flow-verify command OK
```

## <a name="next-steps"></a>Próximas etapas

Se o tráfego está sendo bloqueado e não deve ser, consulte [gerenciar grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para rastrear as rede segurança e grupo de regras de segurança que são definidas.

Aprenda a auditar as configurações de NSG visitando [auditoria de segurança grupos NSG (rede) com o Observador de Rede](network-watcher-nsg-auditing-powershell.md).

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png

