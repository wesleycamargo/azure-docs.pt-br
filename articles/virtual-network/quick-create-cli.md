---
title: Criar uma Rede Virtual do Azure - CLI do Azure | Microsoft Docs
description: Aprenda rapidamente a criar uma rede virtual usando a CLI do Azure. Uma rede virtual permite que recursos do Azure, como máquinas virtuais, comuniquem-se em modo privado e com a Internet.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: ''
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/09/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: d07f06a1a70c859544c3b1ceb6146dc11e4d10aa
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 04/03/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Criar uma rede virtual usando a CLI do Azure

Uma rede virtual permite que recursos do Azure, como VMs (máquinas virtuais), comuniquem-se em modo privado e com a Internet. Neste artigo, você aprenderá como criar uma rede virtual. Após criar uma rede virtual, você implantará duas VMs na rede virtual. Em seguida, você irá conectar uma VM a partir da Internet e comunicar em modo privado com a outra VM.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Caso opte por instalar e usar a CLI localmente, este artigo exigirá que seja executada a CLI do Azure versão 2.0.28 ou posterior. Para localizar a versão instalada, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 


## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Antes de poder criar uma rede virtual, você deverá criar um grupo de recursos para conter a rede virtual. Crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create). O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *eastus*:

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Crie a rede virtual com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). O exemplo a seguir cria uma rede virtual padrão nomeada *myVirtualNetwork* com uma sub-rede nomeada *padrão*:

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas VMs na rede virtual:

### <a name="create-the-first-vm"></a>Criar a primeira VM

Crie uma VM com [az vm create](/cli/azure/vm#az_vm_create). Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. A opção `--no-wait` cria a VM em segundo plano para que você possa prosseguir para a próxima etapa. O exemplo a seguir cria uma VM nomeada *myVm1*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Criar a segunda VM

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A VM demora alguns minutos para criar. Depois que a VM é criada, a CLI do Azure retorna uma saída semelhante ao exemplo a seguir: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm1",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
}
```

Anote o **publicIpAddress**. Esse endereço é usado para conectar a VM a partir da Internet na próxima etapa.

## <a name="connect-to-a-vm-from-the-internet"></a>Conecte uma VM a partir da Internet

Substitua `<publicIpAddress>` pelo endereço IP público da VM *myVm2* no seguinte comando e insira o comando a seguir:

```bash 
ssh <publicIpAddress>
```

## <a name="communicate-privately-between-vms"></a>Comunicação entre VMs em modo privado

Para confirmar a comunicação privada entre as VMs *myVm2* e *myVm1*, insira o seguinte comando:

```bash
ping myVm1 -c 4
```

Quatro respostas são recebidas de *10.0.0.4*.

Encerre a sessão SSH com a VM *myVm2*.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém:

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma rede virtual padrão e duas VMs. Em seguida, conectou uma VM a partir da Internet e executou comunicação entre uma VM e outra VM em modo privado. Para saber mais sobre configurações de rede virtual, consulte [Gerenciar uma rede virtual](manage-virtual-network.md). 

Por padrão, o Azure permite comunicação privada irrestrita entre máquinas virtuais, mas permite somente sessões SSH de entrada para VMs do Linux a partir da Internet. Para saber como permitir ou restringir diferentes tipos de comunicação de rede, de e para VMs, avance para o próximo tutorial.

> [!div class="nextstepaction"]
> [Filtrar tráfego](tutorial-filter-network-traffic-cli.md)
