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
ms.devlang: azurecli
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: cb4d075d283059d613e3e9d8f0a6f9448310d96b
ms.openlocfilehash: 7a8653a82775e718c38f65f246d997ba61f99d58
ms.contentlocale: pt-br
ms.lasthandoff: 06/26/2017

---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Reimplantar uma máquina virtual Linux em um novo nó do Azure
Se você tiver dificuldades ao solucionar problemas de SSH ou de acesso do aplicativo a uma VM (máquina virtual) Linux no Azure, reimplantar a VM poderá ajudar. Quando você reimplanta uma VM, ela é movida para um novo nó dentro da infraestrutura do Azure e, depois, é ligada novamente. Todos os recursos associados e opções de configuração são mantidos. Este artigo mostra como reimplantar uma VM usando a CLI do Azure ou o Portal do Azure.

> [!NOTE]
> Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual serão atualizados. 

Reimplante uma VM usando uma das opções a seguir. Escolha uma opção para reimplantar a VM:

- [CLI 2.0 do Azure](#azure-cli-20)
- [CLI 1.0 do Azure](#azure-cli-10)
- [Portal do Azure](#using-azure-portal)

## <a name="use-the-azure-cli-20"></a>Usar a CLI 2.0 do Azure
Instale a [CLI 2.0 do Azure](/cli/azure/install-az-cli2) mais recente e faça logon em uma conta do Azure usando [az login](/cli/azure/#login).

Reimplante a VM com [az vm redeploy](/cli/azure/vm#redeploy). O exemplo a seguir reimplanta a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-cli-10"></a>Usar a CLI 1.0 do Azure
Instale a [CLI 1.0 do Azure mais recente](../../cli-install-nodejs.md), faça logon uma conta do Azure e verifique se você está no modo do Resource Manager (`azure config mode arm`).

O exemplo a seguir reimplanta a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Próximas etapas
Se você estiver enfrentando problemas para se conectar à sua VM., encontre ajuda específica em [Solução de problemas de conexões SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Etapas detalhadas de solução de problemas de SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você também pode ler [problemas com a solução de problemas de aplicativo](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se não conseguir acessar um aplicativo em execução em sua VM.


