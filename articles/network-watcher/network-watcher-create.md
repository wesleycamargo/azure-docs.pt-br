---
title: Criar uma instância do Observador de Rede do Azure | Microsoft Docs
description: Saiba como habilitar o Observador de Rede em uma região do Azure.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 9d3e579cd58bc6c7d67b29998ea5a48a65548b0a
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/06/2018
---
# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância do Observador de Rede do Azure

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

## <a name="create-a-network-watcher-in-the-portal"></a>Criar um Observador de Rede no portal

Navegue até **Todos os Serviços** > **Rede** > **Observador de Rede**. Você pode selecionar todas as assinaturas para as quais deseja habilitar o Observador de Rede. Essa ação cria um Observador de Rede em todas as regiões que ele está disponível.

![criar um observador de rede](./media/network-watcher-create/figure1.png)

Ao habilitar o Observador de Rede usando o portal, o nome da instância do Observador de Rede é definido automaticamente como *NetworkWatcher_region_name*, em que *region_name* corresponde à região do Azure onde a instância está habilitada. Por exemplo, um Observador de Rede habilitado na região Centro-oeste dos EUA é nomeado *NetworkWatcher_westcentralus*.

A instância do Observador de Rede é criada automaticamente em um grupo de recursos nomeado *NetworkWatcherRG*. O grupo de recursos será criado, se ainda não existir.

Se você deseja personalizar o nome de uma instância do Observador de Rede e do grupo de recursos em que ele é colocado, use os métodos do Powershell, CLI do Azure, API REST ou ARMClient descritos nas seções a seguir. Em cada opção, o grupo de recursos deverá existir antes de você criar um Observador de Rede.  

## <a name="create-a-network-watcher-with-powershell"></a>Criar um Observador de Rede com o PowerShell

Para criar uma instância do Observador de Rede, execute o exemplo a seguir:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-azure-cli"></a>Criar um Observador de Rede com a CLI do Azure

Para criar uma instância do Observador de Rede, execute o exemplo a seguir:

```azurecli
az network watcher configure --resource-group NetworkWatcherRG --locations westcentralus --enabled
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um Observador de Rede com a API REST

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient está localizado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

### <a name="log-in-with-armclient"></a>Fazer logon com o ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Criar o Observador de Rede

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Próximas etapas

Agora que você tem uma instância do Observador de Rede, saiba mais sobre os recursos disponíveis:

* [Topologia](network-watcher-topology-overview.md)
* [Captura de pacotes](network-watcher-packet-capture-overview.md)
* [Verificação de fluxo de IP](network-watcher-ip-flow-verify-overview.md)
* [Próximo salto](network-watcher-next-hop-overview.md)
* [Exibição de grupo de segurança](network-watcher-security-group-view-overview.md)
* [Registro do fluxo NSG](network-watcher-nsg-flow-logging-overview.md)
* [Solução de problemas do Gateway de Rede Virtual](network-watcher-troubleshoot-overview.md)

Após uma instância do Observador de Rede, será possível habilitar a captura de pacote em máquinas virtuais. Para saber mais, consulte [Criar uma captura de pacote disparada por alerta](network-watcher-alert-triggered-packet-capture.md)
