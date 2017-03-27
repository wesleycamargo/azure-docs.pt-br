---
title: "Início Rápido do Azure – Criar CLI de VM do Windows | Microsoft Docs"
description: "Aprenda rapidamente criar máquinas virtuais Windows com a CLI do Azure."
services: virtual-machines-windows
documentationcenter: virtual-machines
author: neilpeterson
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 03/14/2017
ms.author: nepeters
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 3064da2a6afee007418ef6ba5ec4247e3e045c38
ms.lasthandoff: 03/15/2017

---

# <a name="create-a-windows-virtual-machine-with-the-azure-cli-20"></a>Criar uma máquina virtual do Windows com a CLI do Azure 2.0

A CLI do Azure 2.0 é usada para criar e gerenciar recursos do Azure da linha de comando ou em scripts. Este guia detalha o uso da CLI do Azure para implantar uma máquina virtual executando o Windows Server 2016.

Antes de começar, certifique-se de que a CLI do Azure foi instalada. Para obter mais informações, consulte o [Guia de instalação da CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 

## <a name="log-in-to-azure"></a>Fazer logon no Azure 

Faça logon na sua assinatura do Azure com o comando [az login](/cli/azure/#login) e siga as instruções na tela.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com [az group create](/cli/azure/group#create). Um grupo de recursos do Azure é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos denominado `myResourceGroup` no local `westeurope`.

```azurecli
az group create --name myResourceGroup --location westeurope
```

## <a name="create-virtual-machine"></a>Criar máquina virtual

Crie uma VM com [az vm create](/cli/azure/vm#create). 

O exemplo a seguir cria uma VM chamada `myVM`. Este exemplo usa `azureuser` para um nome de usuário administrativo e ` myPassword12` como a senha. Atualize esses valores para algo apropriado para seu ambiente. Esses valores são necessários durante a criação de uma conexão com a máquina virtual.

```azurecli
az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --admin-username azureuser --admin-password myPassword12
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

Use o seguinte comando para criar uma sessão de área de trabalho remota com a máquina virtual. Substitua o endereço IP pelo endereço IP público de sua máquina virtual. Quando solicitado, insira as credenciais usadas ao criar a máquina virtual.

```bash 
mstsc /v:<Public IP Address>
```

## <a name="delete-virtual-machine"></a>Excluir máquina virtual

Quando não é mais necessário, o comando a seguir pode ser usado para remover o Grupo de Recursos, a VM e todos os recursos relacionados.

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Próximas etapas

[Tutorial de instalação de uma função e configuração do firewall](./virtual-machines-windows-hero-role.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)

[Explorar as amostras de CLI de implantação de VM](./virtual-machines-windows-cli-samples.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
