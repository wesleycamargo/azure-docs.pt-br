---
title: Gerenciar logs de fluxo de Grupo de Segurança de Rede com o Observador de Rede do Azure - PowerShell | Microsoft Docs
description: Esta página explica como gerenciar logs de fluxo de Grupo de Segurança de Rede no Observador de Rede do Azure com o PowerShell
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 2dfc3112-8294-4357-b2f8-f81840da67d3
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 752370564c52513d59e99b18d5343b0575900463
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 11/16/2018
ms.locfileid: "51819348"
---
# <a name="configuring-network-security-group-flow-logs-with-powershell"></a>Configurar logs de fluxo de Grupo de Segurança de Rede com o PowerShell

> [!div class="op_single_selector"]
> - [Portal do Azure](network-watcher-nsg-flow-logging-portal.md)
> - [PowerShell](network-watcher-nsg-flow-logging-powershell.md)
> - [CLI do Azure](network-watcher-nsg-flow-logging-cli.md)
> - [API REST](network-watcher-nsg-flow-logging-rest.md)

Logs de fluxo do Grupo de Segurança de Rede são um recurso do Observador de Rede permite que você exiba informações sobre o tráfego IP de entrada e saída por meio de um Grupo de Segurança de Rede. Esses logs de fluxo são escritos no formato json e mostram os fluxos de entrada e de saída por regra, a NIC à qual o fluxo se aplica, as informações de cinco tuplas sobre o fluxo (IP de Origem/Destino, Porta de Origem/Destino, Protocolo) e se o tráfego foi permitido ou negado.

> [!NOTE] 
> Os Logs de Fluxo Versão 2 estão disponíveis apenas na região do Centro-Oeste dos EUA. A configuração está disponível por meio do Portal do Azure e da API REST. A ativação dos logs da Versão 2 em uma região não suportada resultará na saída dos registros da Versão 1 para sua conta de armazenamento.

## <a name="register-insights-provider"></a>Registrar o provedor Insights

Para o registro de fluxo em log funcionar, o provedor **Microsoft.Insights** deve ser registrado. Se você não tiver certeza se o provedor **Microsoft.Insights** está registrado, execute o script a seguir.

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Insights
```

## <a name="enable-network-security-group-flow-logs"></a>Habilitar os logs do Fluxo de Grupo de Segurança de Rede

O comando para habilitar os logs de fluxo é mostrado no exemplo a seguir:

```powershell
$NW = Get-AzurermNetworkWatcher -ResourceGroupName NetworkWatcherRg -Name NetworkWatcher_westcentralus
$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName nsgRG -Name nsgName
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName StorageRG -Name contosostorage123
Get-AzureRmNetworkWatcherFlowLogStatus -NetworkWatcher $NW -TargetResourceId $nsg.Id
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $true
```

A conta de armazenamento que você especificar não pode ter regras de rede configuradas para ela que restrinjam o acesso à rede somente a serviços da Microsoft ou redes virtuais específicas. A conta de armazenamento pode ser a mesma ou uma assinatura diferente do Azure do que o NSG que você habilitar o log de fluxo. Se você usar assinaturas diferentes, ambas devem estar associadas ao mesmo locatário do Microsoft Azure Active Directory. A conta que você usa para cada assinatura deve ter as [permissões necessárias](required-rbac-permissions.md).

## <a name="disable-network-security-group-flow-logs"></a>Desabilitar os logs do Fluxo de Grupo de Segurança de Rede

Use o exemplo a seguir para desabilitar logs de fluxo:

```powershell
Set-AzureRmNetworkWatcherConfigFlowLog -NetworkWatcher $NW -TargetResourceId $nsg.Id -StorageAccountId $storageAccount.Id -EnableFlowLog $false
```

## <a name="download-a-flow-log"></a>Baixar um log de fluxo

O local de armazenamento de um log de fluxo é definido no momento da criação. Uma ferramenta conveniente para acessar esses logs de fluxo salvos em uma conta de armazenamento é o Gerenciador de Armazenamento do Microsoft Azure, que pode ser baixado aqui: http://storageexplorer.com/

Se uma conta de armazenamento for especificada, os arquivos de captura de pacote serão salvos em uma conta de armazenamento no seguinte local:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Para saber mais sobre a estrutura do log visite a [Visão geral do log do fluxo de Grupo de Segurança de Rede](network-watcher-nsg-flow-logging-overview.md)

## <a name="next-steps"></a>Próximas etapas

Saiba como [Visualizar seus logs de fluxo NSG com o PowerBI](network-watcher-visualize-nsg-flow-logs-power-bi.md)

Saiba como [Visualizar seus logs de fluxo NSG com ferramentas de código aberto](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)
