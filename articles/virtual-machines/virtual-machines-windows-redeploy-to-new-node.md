---
title: Reimplantar máquinas virtuais do Windows | Microsoft Docs
description: Descreve como reimplantar máquinas virtuais do Windows para atenuar problemas de conexão RDP.
services: virtual-machines-windows
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue

ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 09/19/2016
ms.author: iainfou

---
# Reimplantar a máquina virtual em um novo nó do Azure
Se você enfrentou dificuldades para solucionar problemas de conexão RDP (Área de Trabalho Remota) ou de acesso ao aplicativo em uma VM (máquina virtual) do Azure baseada em Windows, reimplantar a VM pode ajudar. Quando você reimplanta uma VM, ela é movida para um novo nó dentro da infraestrutura do Azure e, depois, é ligada novamente, mantendo todas as suas opções de configuração e recursos associados. Este artigo mostra como reimplantar uma VM usando o Azure PowerShell ou o Portal do Azure.

> [!NOTE]
> Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual serão atualizados.
> 
> 

## Usando o PowerShell do Azure
Verifique se você tem o Azure PowerShell 1.x mais recente instalado em seu computador. Para saber mais, consulte [Como instalar e configurar o Azure PowerShell](../powershell-install-configure.md):

Use este comando do Azure PowerShell para reimplantar a máquina virtual:

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## Próximas etapas
Você pode encontrar ajuda específica em [Solução de problemas de conexões RDP](virtual-machines-windows-troubleshoot-rdp-connection.md) ou [Etapas detalhadas de solução de problemas de RDP](virtual-machines-windows-detailed-troubleshoot-rdp.md) se você estiver enfrentando problemas para se conectar à sua VM. Você também pode ler [problemas com a solução de problemas de aplicativo](virtual-machines-windows-troubleshoot-app-connection.md) se não conseguir acessar um aplicativo em execução em sua VM.

<!---HONumber=AcomDC_0921_2016-->