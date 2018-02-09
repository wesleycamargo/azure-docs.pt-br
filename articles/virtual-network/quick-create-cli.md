---
title: Criar uma rede virtual - CLI do Azure | Microsoft Docs
description: Aprenda rapidamente a criar uma rede virtual usando a CLI do Azure. Uma rede virtual permite que muitos tipos de recursos do Azure comuniquem-se em modo privado.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: 
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/25/2018
ms.author: jdial
ms.custom: 
ms.openlocfilehash: b2fc8a622549a9858c6c769a7e648fe07a3d01c1
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 02/01/2018
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Criar uma rede virtual usando a CLI do Azure

Neste artigo, você aprenderá como criar uma rede virtual. Após criar uma rede virtual, você implantará duas máquinas virtuais na rede virtual e se comunicarão em modo privado.

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este guia de início rápido exigirá a execução da CLI do Azure versão 2.0.4 ou posterior. Para localizar a versão instalada, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group#az_group_create). Um grupo de recursos é um contêiner lógico no qual os recursos do Azure são implantados e gerenciados. 

O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* no local *eastus*. Todos os recursos do Azure são criados dentro de uma localização (ou região) do Azure.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie uma rede virtual com o comando [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create). O exemplo a seguir cria uma rede virtual padrão nomeada *myVirtualNetwork* com uma sub-rede nomeada *padrão*. Se a localização não for especificada, o Azure criará a rede virtual no mesmo local do grupo de recursos.

```azurecli-interactive 
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

Após a criação da rede virtual, uma parte das informações retornadas é a seguinte.

```azurecli
"newVNet": {
    "addressSpace": {
      "addressPrefixes": [
        "10.0.0.0/16"
```

Todas as redes virtuais têm um ou mais prefixos de endereço atribuídos a elas. Um vez que um prefixo de endereço não foi especificado durante a criação da rede virtual, o Azure definiu o espaço de endereço 10.0.0.0/16, por padrão. O espaço de endereço é especificado na notação CIDR. O espaço de endereço 10.0.0.0/16 abrange 10.0.0.0-10.0.255.254.

Outra parte das informações retornadas é o **addressPrefix** do *10.0.0.0/24* para a sub-rede *padrão* especificada no comando. Uma rede virtual contém zero ou mais sub-redes. O comando criou uma única sub-rede nomeada *padrão*, mas nenhum prefixo de endereço foi especificado para a sub-rede. Quando um prefixo de endereço não é especificado para uma rede ou sub-rede virtual, o Azure define 10.0.0.0/24 como prefixo de endereço para a primeira sub-rede, por padrão. Como resultado, a sub-rede abrange 10.0.0.0-10.0.0.254, mas apenas 10.0.0.4-10.0.0.254 estão disponíveis, porque o Azure reserva os quatro primeiros endereços (0-3) e o último endereço em cada sub-rede.

## <a name="create-virtual-machines"></a>Criar máquinas virtuais

Uma rede virtual permite que vários tipos de recursos do Azure comuniquem-se em modo privado. Um tipo de recurso que pode ser implantado em uma rede virtual é uma máquina virtual. Crie duas máquinas virtuais na rede virtual para que você possa validar e entender como a comunicação entre máquinas virtuais em uma rede virtual funciona em uma etapa posterior.

Crie uma máquina virtual com o comando [az vm create](/cli/azure/vm#az_vm_create). O exemplo a seguir cria uma máquina virtual nomeada *myVm1*. Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`. A opção `--no-wait` cria a máquina virtual em segundo plano para que você continue na próxima etapa.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

O Azure cria automaticamente a máquina virtual na sub-rede *padrão* da rede virtual *myVirtualNetwork* porque a rede virtual existe no grupo de recursos e nenhuma rede ou sub-rede virtual está especificada no comando. O DHCP do Azure é atribuído automaticamente 10.0.0.4 à máquina virtual durante a criação, porque é o primeiro endereço disponível na sub-rede *padrão*. A localização em que uma máquina virtual é criada deve ser a mesma localização na qual a rede virtual existe. Não é necessário que a máquina virtual esteja no mesmo grupo de recursos que a máquina virtual, embora isso seja abordado neste artigo.

Crie uma segunda máquina virtual. Por padrão, o Azure também cria essa máquina virtual na sub-rede *padrão*.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

A criação da máquina virtual demora alguns minutos. Depois que a máquina virtual é criada, a CLI do Azure retorna saída semelhante ao exemplo a seguir: 

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

No exemplo, é possível ver que o **privateIpAddress** é *10.0.0.5*. O DHCP do Azure atribuiu automaticamente *10.0.0.5* à máquina virtual porque era o próximo endereço disponível na sub-rede *padrão*. Anote o **publicIpAddress**. Esse endereço será usado para acessar a máquina virtual da Internet em uma etapa posterior. O endereço IP público não é atribuído dentro da rede virtual ou prefixos de endereço de sub-rede. Os endereços IP públicos são atribuídos a partir de um [pool de endereços atribuídos a cada região do Azure](https://www.microsoft.com/download/details.aspx?id=41653). Enquanto o Azure sabe qual endereço IP público é atribuído a uma máquina virtual, o sistema operacional executado em uma máquina virtual não reconhece nenhum endereço IP público atribuído a ele.

## <a name="connect-to-a-virtual-machine"></a>Conectar-se a uma máquina virtual

Use o comando a seguir para criar uma sessão SSH com a máquina virtual *myVm2*. Substitua `<publicIpAddress>` pelo endereço IP público de sua máquina virtual. No exemplo acima, o endereço IP é *40.68.254.142*.

```bash 
ssh <publicIpAddress>
```

## <a name="validate-communication"></a>Validar a comunicação

Use o comando a seguir para confirmar a comunicação com *myVm1* a partir de *myVm2*:

```bash
ping myVm1 -c 4
```

Quatro respostas são recebidas de *10.0.0.4*. É possível comunicar-se com *myVm1* a partir de *myVm2* porque ambas as máquinas virtuais possuem endereços IP privados atribuídos da sub-rede *padrão*. É possível executar ping pelo nome do host porque o Azure fornece automaticamente a resolução de nomes DNS para todos os hosts dentro de uma rede virtual.

Use o comando a seguir para confirmar a comunicação de saída para a Internet:

```bash
ping bing.com -c 4
```

Você recebe quatro respostas do bing.com. Por padrão, qualquer máquina virtual em uma rede virtual pode comunicar-se com a Internet.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, você poderá usar o comando [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém. Saia da sessão SSH para sua VM e exclua os recursos.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você implantou uma rede virtual padrão com uma sub-rede e duas máquinas virtuais. Para saber como criar uma rede virtual personalizada com várias sub-redes e executar tarefas básicas de gerenciamento, continue no tutorial para criar uma rede virtual personalizada e gerenciá-la.


> [!div class="nextstepaction"]
> [Criar uma rede virtual personalizada e gerenciá-la](virtual-networks-create-vnet-arm-pportal.md#azure-cli)
