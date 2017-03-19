---
title: "Criar uma instância do Observador de Rede do Azure | Microsoft Docs"
description: "Essa página oferece as etapas para criar uma instância do Observador de Rede usando o portal e a API REST do Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 064c8f7c312aaac94f20224e99b9e29c641e0349
ms.openlocfilehash: 7099081e7294c64c1b9076b58a2c87f4f7ca7e44
ms.lasthandoff: 03/01/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Criar uma instância do Observador de Rede do Azure

O Observador de Rede é um serviço regional que permite monitorar e diagnosticar as condições em um nível do cenário da rede em, para e a partir do Azure. O monitoramento de nível do cenário permite diagnosticar problemas em um modo de exibição de nível de rede de ponta a ponta. As ferramentas de diagnóstico e visualização da rede disponíveis com o Observador de Rede ajudam a entender, diagnosticar e ter informações para sua rede no Azure.

## <a name="register-the-preview-capability"></a>Registrar o recurso de visualização

O Observador de Rede está atualmente na visualização e para usar seus recursos, o recurso precisa ser registrado. Para fazer isso, execute o seguinte exemplo do PowerShell:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Network
Register-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace Microsoft.Network
```

Para verificar se o registro foi bem-sucedido, execute o seguinte exemplo do Powershell:

```powershell
Get-AzureRmProviderFeature -FeatureName AllowNetworkWatcher -ProviderNamespace  Microsoft.Network
```

Se o recurso foi registrado corretamente, a saída deve corresponder com o seguinte:

```
FeatureName         ProviderName      RegistrationState
-----------         ------------      -----------------
AllowNetworkWatcher Microsoft.Network Registered
```

####<a name="instructions-for-cli"></a>Instruções para CLI

Para registrar

```CLI
azure provider register Microsoft.Network
azure feature register  Microsoft.Network AllowNetworkWatcher
```
Para verificar se o registro foi bem-sucedido, execute o seguinte comando de CLI:

```CLI
azure feature show Microsoft.Network AllowNetworkWatcher
```

Se o recurso foi registrado corretamente, a saída deve corresponder com o seguinte:
```CLI
info:    Executing command feature show
data:    Feature Name:       AllowNetworkWatcher
data:    Provider Name:      Microsoft.Network
data:    Registration State: Registered
info:    feature show command OK
```

## <a name="create-a-network-watcher-in-the-portal"></a>Criar um Observador de Rede no portal

Navegue até **Mais Serviços** > **Rede** > **Observador de Rede**. Você pode selecionar todas as assinaturas para as quais deseja habilitar o Observador de Rede. Essa ação cria um Observador de Rede em todas as regiões que ele está disponível.

![criar um observador de rede][1]

## <a name="create-a-network-watcher-with-powershell"></a>Criar um Observador de Rede com o PowerShell

Para criar uma instância do Observador de Rede, execute o exemplo a seguir:

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Criar um Observador de Rede com a API REST

O ARMclient é usado para chamar a API REST usando o PowerShell. O ARMClient é encontrado no chocolatey em [ARMClient no Chocolatey](https://chocolatey.org/packages/ARMClient)

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

Quando uma instância do Observador de Rede tiver sido criada, a captura de pacotes pode ser configurada seguindo o artigo [Criar uma captura de pacote acionada por alerta](network-watcher-alert-triggered-packet-capture.md)

[1]: ./media/network-watcher-create/figure1.png












