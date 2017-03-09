---
title: "Reimplantar Máquinas Virtuais Linux no Azure | Microsoft Docs"
description: "Como reimplantar máquinas virtuais Linux no Azure para atenuar problemas de conexão SSH."
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
ms.date: 12/16/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 7f3abdd63e43713d9d1f7ff28e44efc08167fddb
ms.openlocfilehash: f421ebdc81ce413d69f62a3b86563ab4a09b747a
ms.lasthandoff: 02/27/2017


---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Reimplantar uma máquina virtual Linux em um novo nó do Azure
Se você tiver tendo dificuldades em solucionar problemas de SSH ou de acesso do aplicativo a uma VM (máquina virtual) Linux no Azure, reimplantar a VM poderá ajudar. Quando você reimplanta uma VM, ela é movida para um novo nó dentro da infraestrutura do Azure e, depois, é ligada novamente, mantendo todas as suas opções de configuração e recursos associados. Este artigo mostra como reimplantar uma VM usando a CLI do Azure ou o Portal do Azure.

> [!NOTE]
> Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual serão atualizados. 

Reimplante uma VM usando uma das opções a seguir. Escolha uma opção para reimplantar a VM:

- [CLI 2.0 do Azure](#azure-cli-20)
- [CLI 1.0 do Azure](#azure-cli-10)
- [Portal do Azure](#using-azure-portal)

## <a name="azure-cli-20"></a>CLI 2.0 do Azure
Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login).

Reimplante a VM com [az vm redeploy](/cli/azure/vm#redeploy). O seguinte exemplo reimplanta a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="azure-cli-10"></a>CLI 1.0 do Azure
Instale a [CLI 1.0 do Azure mais recente](../xplat-cli-install.md), faça logon uma conta do Azure e verifique se você está no modo do Resource Manager (`azure config mode arm`).

O seguinte exemplo reimplanta a VM chamada `myVM` no grupo de recursos chamado `myResourceGroup`:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Próximas etapas
Se você estiver enfrentando problemas para se conectar à sua VM., encontre ajuda específica em [Solução de problemas de conexões SSH](virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Etapas detalhadas de solução de problemas de SSH](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você também pode ler [problemas com a solução de problemas de aplicativo](virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se não conseguir acessar um aplicativo em execução em sua VM.


