---
title: "Gerenciar logs de fluxo de Grupo de Segurança de Rede com o Observador de Rede do Azure - PowerShell | Microsoft Docs"
description: "Esta página explica como gerenciar logs de fluxo de Grupo de Segurança de Rede no Observador de Rede do Azure com o PowerShell"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: 7a8f685c34709f6b2a2c7627f1a66659720100c0
ms.contentlocale: pt-br
ms.lasthandoff: 05/09/2017


---


# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Configurar logs de fluxo de Grupo de Segurança de Rede com o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Logs de fluxo do Grupo de Segurança de Rede são um recurso do Observador de Rede permite que você exiba informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede. Esses logs de fluxo são escritos no formato json e mostram os fluxos de entrada e de saída por regra, a NIC à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

## <a name="register-insights-provider"></a>Provedor de informações de registro

Para o registro de fluxo em log funcionar, o provedor **Microsoft.Insights** deve ser registrado. Se você não tiver certeza se o provedor **Microsoft.Insights** está registrado, execute o script a seguir.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Habilitar os logs do Fluxo de Grupo de Segurança de Rede

O comando para habilitar os logs de fluxo é mostrado no exemplo a seguir:

```powershell
$NW = Get-AzurermNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName nsgRG-Name nsgName
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzureRmNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true
```

## <a name="disable-network-security-group-flow-logs"></a>Desabilitar os logs do Fluxo de Grupo de Segurança de Rede

Use o exemplo a seguir para desabilitar logs de fluxo:

```powershell
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Baixar um log de fluxo

O local de armazenamento de um log de fluxo é definido no momento da criação. Uma ferramenta conveniente para acessar esses logs de fluxo salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixado aqui: http://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId%3D/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/microsoft.network/networksecuritygroups/{nsgName}/{year}/{month}/{day}/PT1H.json
```

Para saber mais sobre a estrutura do log visite a [Visão geral do log do fluxo de Grupo de Segurança de Rede](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Próximas etapas

Saiba como [Visualizar seus logs de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualizar seus logs de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
