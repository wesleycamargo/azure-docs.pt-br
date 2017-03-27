---
title: "Início Rápido do Azure – Criar CLI da VM | Microsoft Docs"
description: "Aprenda rapidamente criar máquinas virtuais com a CLI do Azure."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/10/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: c1cd1450d5921cf51f720017b746ff9498e85537
ms.openlocfilehash: 3ad45837a8c3f0e37e8571fb9b894df53ecb5f56
ms.lasthandoff: 03/14/2017

---

# <a name="create-a-linux-virtual-machine-with-the-azure-cli-20"></a>Criar uma máquina virtual Linux com a CLI do Azure 2.0

A CLI do Azure 2.0 é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso da CLI do Azure para implantar uma máquina virtual executando Ubuntu 14.04 LTS.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, consulte o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma VM com o comando [az vm create](/cli/azure/vm#create). 

O exemplo a seguir cria uma VM denominada `myVM` e cria chaves SSH, se eles ainda não existirem em um local de chave padrão. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.  

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Quando a VM tiver sido criada, a CLI do Azure mostra informações semelhantes ao exemplo a seguir. Anote o endereço IP público. Esse endereço é usado para acessar a VM.

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/d5b9d4b7-6fc1-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westeurope",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "52.174.34.95",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-virtual-machine"></a>Conectar-se à máquina virtual

Use o seguinte comando para criar uma sessão SSH com a máquina virtual. Substitua o endereço IP pelo endereço IP público de sua máquina virtual.

```bash 
ssh <Public IP Address>
```

## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando não é mais necessário, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial Criar máquinas virtuais altamente disponíveis](./virtual-machines-linux-create-cli-complete.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Explorar as amostras de CLI de implantação de VM](./virtual-machines-linux-cli-samples.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
