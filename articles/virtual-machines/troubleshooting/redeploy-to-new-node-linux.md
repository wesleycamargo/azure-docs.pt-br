---
title: Reimplantar Máquinas Virtuais Linux no Azure | Microsoft Docs
description: Como reimplantar máquinas virtuais Linux no Azure para atenuar problemas de conexão SSH.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: genlin
manager: jeconnoc
tags: azure-resource-manager,top-support-issue
ms.assetid: e9530dd6-f5b0-4160-b36b-d75151d99eb7
ms.service: virtual-machines-linux
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/14/2017
ms.author: genli
ms.openlocfilehash: 8f8cab985ebc20290a94df34a9268ad2c1f169d3
ms.sourcegitcommit: b7e5bbbabc21df9fe93b4c18cc825920a0ab6fab
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 09/27/2018
ms.locfileid: "47410928"
---
# <a name="redeploy-linux-virtual-machine-to-new-azure-node"></a>Reimplantar uma máquina virtual Linux em um novo nó do Azure
Se você tiver dificuldades ao solucionar problemas de SSH ou de acesso do aplicativo a uma VM (máquina virtual) Linux no Azure, reimplantar a VM poderá ajudar. Quando você reimplanta uma VM, ela é movida para um novo nó dentro da infraestrutura do Azure e, depois, é ligada novamente. Todos os recursos associados e opções de configuração são mantidos. Este artigo mostra como reimplantar uma VM usando a CLI do Azure ou o Portal do Azure.

> [!NOTE]
> Depois que você reimplanta uma VM, o disco temporário será perdido e os endereços IP dinâmicos associados ao adaptador de rede virtual serão atualizados. 


## <a name="use-the-azure-cli"></a>Usar a CLI do Azure
Instale a última [CLI do Azure](/cli/azure/install-az-cli2) e faça logon em sua conta do Azure usando [az login](/cli/azure/reference-index#az_login).

Reimplante a VM com [az vm redeploy](/cli/azure/vm#az_vm_redeploy). O exemplo a seguir reimplanta a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
az vm redeploy --resource-group myResourceGroup --name myVM 
```

## <a name="use-the-azure-classic-cli"></a>Usar a CLI do Azure clássica
Instale a [última CLI clássica do Azure](../../cli-install-nodejs.md) e faça logon em sua conta do Azure. Certifique-se de estar no modo Resource Manager (`azure config mode arm`).

O exemplo a seguir reimplanta a VM chamada *myVM* no grupo de recursos chamado *myResourceGroup*:

```azurecli
azure vm redeploy --resource-group myResourceGroup --vm-name myVM 
```

[!INCLUDE [virtual-machines-common-redeploy-to-new-node](../../../includes/virtual-machines-common-redeploy-to-new-node.md)]

## <a name="next-steps"></a>Próximas etapas
Se você estiver enfrentando problemas para se conectar à sua VM., encontre ajuda específica em [Solução de problemas de conexões SSH](troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) ou [Etapas detalhadas de solução de problemas de SSH](detailed-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Você também pode ler [problemas com a solução de problemas de aplicativo](troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) se não conseguir acessar um aplicativo em execução em sua VM.


