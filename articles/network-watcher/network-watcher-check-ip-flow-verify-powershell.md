---
title: "Verifique se o tráfego com a Verificação de Fluxo de IP do Observador de Rede do Azure - PowerShell | Microsoft Docs"
description: "Este artigo descreve como verificar se o tráfego de ou para uma máquina virtual é permitido ou negado usando o PowerShell"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: e1dad757-8c5d-467f-812e-7cc751143207
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: adc7cbb938d396979a87d22ec86c82a801f4be5b
ms.lasthandoff: 02/23/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-to-or-from-a-vm-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Verifique se o tráfego é permitido ou negado com uma Verificação de Fluxo de IP, um componente do Observador de Rede do Azure

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [CLI](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST do Azure](network-watcher-check-ip-flow-verify-rest.md)

A Verificação de Fluxo de IP é um recurso do Observador de Rede que permite verificar se o tráfego é permitido para ou de uma máquina virtual. Esse cenário é útil para obter o estado atual de se uma máquina virtual pode se comunicar com um recurso externo ou um back-end. A Verificação de Fluxo de IP pode ser usada para verificar se as regras do Grupo de Segurança de Rede (NSG) estão configuradas corretamente e solucionar problemas de fluxos que estão sendo bloqueados por regras do NSG. Outro motivo para usar a Verificação de Fluxo de IP é para garantir que o tráfego, que você deseja bloquear, está sendo bloqueado corretamente pelo NSG.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você já tenha seguido as etapas em [criar um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede ou ter uma instância existente do Gerenciador da rede. O cenário também pressupõe que exista um grupo de recursos com uma máquina virtual válida a ser usada.

## <a name="scenario"></a>Cenário

Esse cenário usa o IP fluxo verificar para verificar se uma máquina virtual pode se comunicar com um endereço IP do Bing conhecido. Se o tráfego é negado, ele retorna a regra de segurança que está negando esse tráfego. Para saber mais sobre a Verificação de Fluxo de IP, consulte [Visão Geral da Verificação de Fluxo de IP](network-watcher-ip-flow-verify-overview.md)

## <a name="retrieve-network-watcher"></a>Recuperar o Observador de Rede

A primeira etapa é recuperar a instância do Observador de Rede. A variável `$networkWatcher` é passada para o cmdlet verificação de fluxo de IP.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Obter uma VM

A Verificação de Fluxo de IP testa o tráfego para ou de um endereço IP em uma máquina virtual ou de um destino remoto. Uma Id de uma máquina virtual é necessária para o cmdlet. Se você já souber a ID da máquina virtual para usar, você pode ignorar esta etapa.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="get-the-nics"></a>Obter as NICS

O endereço IP de uma NIC na máquina virtual é necessária neste exemplo, recuperamos as NICs em uma máquina virtual. Se você já souber o endereço IP que você deseja testar na máquina virtual, você pode ignorar esta etapa.

```powershell
$Nics = Get-AzureRmNetworkInterface | Where {$_.Id -eq $vm.NetworkInterfaceIDs.ForEach({$_})}
```

## <a name="run-ip-flow-verify"></a>Executar a verificação de fluxo de IP

Agora que temos as informações necessárias para executar o cmdlet, executamos o `Test-AzureRmNetworkWatcherIPFlow` para testar o tráfego. Neste exemplo, estamos usando o primeiro endereço IP na primeira NIC.

```powershell
Test-AzureRmNetworkWatcherIPFlow -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id `
-Direction Outbound -Protocol TCP `
-LocalIPAddress $nics[0].IpConfigurations[0].PrivateIpAddress -LocalPort 6895 -RemoteIPAddress 204.79.197.200 -RemotePort 80
```

> [!NOTE]
> A Verificação de Fluxo de IP requer que o recurso de máquina virtual seja alocado para executar.

## <a name="review-results"></a>Analisar Resultados

Após a execução do `Test-AzureRmNetworkWatcherIPFlow` os resultados são retornados, o exemplo a seguir é os resultados retornados da etapa anterior.

```
Access RuleName                                  
------ --------                                  
Allow  defaultSecurityRules/AllowInternetOutBound
```

## <a name="next-steps"></a>Próximas etapas

Se o tráfego estiver sendo bloqueado, mas não devesse estar, consulte [Gerenciar Grupos de Segurança de Rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para rastrear a rede segurança e o grupo de regras de segurança definidos.

[1]: ./media/network-watcher-check-ip-flow-verify-portal/figure1.png
[2]: ./media/network-watcher-check-ip-flow-verify-portal/figure2.png














