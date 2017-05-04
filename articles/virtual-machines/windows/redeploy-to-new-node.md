---
title: "Reimplantar máquinas virtuais Windows no Azure | Microsoft Docs"
description: "Como reimplantar máquinas virtuais Windows no Azure para atenuar problemas de conexão RDP."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: 0ee456ee-4595-4a14-8916-72c9110fc8bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 8682c7707f489aafd55a6673420bafa2ba5f4ade
ms.lasthandoff: 04/27/2017


---
# <a name="redeploy-windows-virtual-machine-to-new-azure-node"></a>Reimplantar uma máquina virtual Windows em um novo nó do Azure
Se você enfrentou dificuldades para solucionar problemas de conexão RDP (Área de Trabalho Remota) ou de acesso ao aplicativo em uma VM (máquina virtual) do Azure baseada em Windows, reimplantar a VM pode ajudar. Quando você reimplanta uma VM, ela é movida para um novo nó dentro da infraestrutura do Azure e, depois, é ligada novamente, mantendo todas as suas opções de configuração e recursos associados. Este artigo mostra como reimplantar uma VM usando o Azure PowerShell ou o Portal do Azure.

> [!NOTE]
> Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual serão atualizados. 


## <a name="using-azure-powershell"></a>Usando o PowerShell do Azure
Verifique se você tem o Azure PowerShell 1.x mais recente instalado em seu computador. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview):

O seguinte exemplo implanta a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```powershell
Set-AzureRmVM -Redeploy -ResourceGroupName "myResourceGroup" -Name "myVM"
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Próximas etapas
Você pode encontrar ajuda específica em [Solução de problemas de conexões RDP](troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) ou [Etapas detalhadas de solução de problemas de RDP](detailed-troubleshoot-rdp.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se você estiver enfrentando problemas para se conectar à sua VM. Você também pode ler [problemas com a solução de problemas de aplicativo](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) se não conseguir acessar um aplicativo em execução em sua VM.


