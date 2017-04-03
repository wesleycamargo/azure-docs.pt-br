---
title: "Solução de problemas de conexões e do gateway de rede virtual do Azure - PowerShell | Microsoft Docs"
description: "Esta página explica como usar o cmdlet do PowerShell para solucionar problemas do Observador de rede do Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: f6f0a813-38b6-4a1f-8cfc-1dfdf979f595
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 50d82847fb629880f298f79f9ab791a13836f01c
ms.openlocfilehash: 8b2df558c6793d5dab7dd7cb6d1f19d279cfa153
ms.lasthandoff: 03/31/2017


---

# <a name="troubleshoot-virtual-network-gateway-and-connections-using-azure-network-watcher-powershell"></a>Como solucionar problemas de conexões e gateway de rede virtual do usando o PowerShell do Observador de rede do Azure

> [!div class="op_single_selector"]
> - [PowerShell](network-watcher-troubleshoot-manage-powershell.md)
> - [CLI](network-watcher-troubleshoot-manage-cli.md)
> - [API REST](network-watcher-troubleshoot-manage-rest.md)

O Observador de rede oferece muitos recursos que dizem respeito às noções básicas sobre os recursos de rede no Azure. Um desses recursos é a solução de problemas de recursos. A solução de problemas de recursos pode ser chamada pelo PowerShell, pela CLI ou pela REST API. Quando chamado, o Observador de rede inspeciona a integridade de uma conexão ou um gateway de rede virtual e faz um relatório sobre suas descobertas.

## <a name="before-you-begin"></a>Antes de começar

Este cenário pressupõe que você seguiu as etapas em [Criação de um Observador de Rede](network-watcher-create.md) para criar um Observador de Rede.

## <a name="overview"></a>Visão geral

A solução de problemas de recursos fornece a capacidade de solucionar problemas que podem surgir com as conexões e o gateway de rede virtual. Quando uma solução de problemas de recursos recebe uma solicitação, os logs são consultados e inspecionadas. Quando a inspeção estiver concluída, você receberá um relatório com os resultados. As solicitações da solução de problemas de recursos são solicitações de execução longa e podem demorar para gerar um relatório. Os logs de solução de problemas são armazenados em um contêiner em uma conta de armazenamento especificada.

## <a name="retrieve-network-watcher"></a>Recuperar o Observador de Rede

A primeira etapa é recuperar a instância do Observador de Rede. A variável `$networkWatcher` é passada para o cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting` na etapa 4.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="retrieve-a-virtual-network-gateway-connection"></a>Como recuperar uma conexão de gateway de rede virtual

Neste exemplo, a solução de problemas de recursos está sendo executada em uma conexão. Você também pode passá-lo por um gateway de rede virtual.

```powershell
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name "2to3" -ResourceGroupName "testrg"
```

## <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

A solução de problemas de recursos produz relatório de dados sobre a integridade do recurso, ela também salva os logs em uma conta de armazenamento a ser revisada. Nesta etapa, criaremos uma conta de armazenamento. Você pode usar uma conta de armazenamento existente se já tiver uma.

```powershell
$sa = New-AzureRmStorageAccount -Name "contosoexamplesa" -SKU "Standard_LRS" -ResourceGroupName "testrg" -Location "WestCentralUS"
```

## <a name="run-network-watcher-resource-troubleshooting"></a>Como executar a solução de problemas de recursos do Observador de rede

Você usa o cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting` para solucionar problemas de recursos. Passamos o cmdlet do objeto do Observador de Rede, a Id da Conexão ou Gateway de Rede Virtual, a id da conta de armazenamento e o caminho para armazenar os resultados.

> [!NOTE]
> O cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting` é demorado e pode levar alguns minutos para ser concluído.

```powershell
Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath "$($sa.PrimaryEndpoints.Blob)logs"
```

Depois que você executar o cmdlet, o Observador de rede revisará o recurso para verificar a integridade. Ele envia um relatório com os resultados para o shell e armazena os logs dos resultados na conta de armazenamento especificada.

## <a name="understanding-the-results"></a>Como entender os resultados

O texto de ação fornece orientação geral sobre como resolver o problema. Se for possível executar uma ação para solucionar o problema, você receberá um link com orientações adicionais. Nos casos em que não há orientações adicionais, a resposta fornecerá a url para abrir um caso de suporte.  Para obter mais informações sobre as propriedades da resposta e o do que está incluído, acesse [Visão geral da solução de problemas do Observador de rede](network-watcher-troubleshoot-overview.md)

Para obter instruções sobre como baixar os arquivos de contas de armazenamento do Azure, veja [Introdução ao armazenamento de Blobs do Azure usando o .NET](../storage/storage-dotnet-how-to-use-blobs.md). Outra ferramenta que pode ser usada é o Gerenciador de armazenamento. Para obter mais informações sobre o Gerenciador de armazenamento, acesse o link: [Gerenciador de armazenamento](http://storageexplorer.com/)

## <a name="next-steps"></a>Próximas etapas

Se as configurações foram alteradas parar a conectividade VPN, confira [Gerenciar grupos de segurança de rede](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para rastrear as regras de segurança e o grupo de segurança de rede que podem estar em questão.

