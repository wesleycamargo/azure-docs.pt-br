---
title: "Reimplantar as máquinas virtuais Linux | Microsoft Docs"
description: "Descreve como reimplantar máquinas virtuais Linux para atenuar problemas de conexão SSH."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: iainfoulds
manager: timlt
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: support-article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/19/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 63cf1a5476a205da2f804fb2f408f4d35860835f
ms.openlocfilehash: 1cd81944ff1e8b6048315946220adee4bf68e576


---
# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Reimplantar a máquina virtual em um novo nó do Azure
Se você enfrentou dificuldades para solucionar problemas de SSH ou de acesso ao aplicativo em uma VM (máquina virtual) do Azure, reimplantar a VM pode ajudar. Quando você reimplanta uma VM, ela é movida para um novo nó dentro da infraestrutura do Azure e, depois, é ligada novamente, mantendo todas as suas opções de configuração e recursos associados. Este artigo mostra como reimplantar uma VM usando a CLI do Azure ou o Portal do Azure.

> [!NOTE]
> Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual serão atualizados. 
> 
> 

## <a name="using-azure-cli"></a>Usando a CLI do Azure
Verifique se você tem a [CLI do Azure mais recente instalada](../xplat-cli-install.md) em seu computador e se você está no modo do Resource Manager (`azure config mode arm`).

Use o seguinte comando da CLI do Azure para reimplantar sua máquina virtual:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Você pode ver o status da VM mudar enquanto percorre o processo de reimplantação. O `PowerState` da VM muda de 'Em execução' para 'Atualizando', depois para 'Iniciando' e finalmente para 'Em execução' enquanto percorre o processo de reimplantação para um novo host. Verifique o status das VMs dentro de um grupo de recursos com:

```bash
azure vm list -g <resourcegroup>
```


[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Próximas etapas
Se você estiver enfrentando problemas para se conectar à sua VM., encontre ajuda específica em [Solução de problemas de conexões SSH](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Etapas detalhadas de solução de problemas de SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você também pode ler [problemas com a solução de problemas de aplicativo](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se não conseguir acessar um aplicativo em execução em sua VM.




<!--HONumber=Nov16_HO3-->


