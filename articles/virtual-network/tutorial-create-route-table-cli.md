---
title: Rotear tráfego - CLI do Azure | Microsoft Docs
description: Saiba como rotear tráfego com uma tabela de rotas utilizando a CLI do Azure.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/02/2018
ms.author: jdial
ms.custom: ''
ms.openlocfilehash: 3c16c774fa1c8a5c8bf50b4f4f9d0bfb283318e3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 03/08/2018
---
# <a name="route-network-traffic-with-a-route-table-using-the-azure-cli"></a>Rotear tráfego com uma tabela de rotas utilizando a CLI do Azure

Por padrão, o Azure roteia automaticamente o tráfego entre todas as sub-redes dentro de uma rede virtual. É possível criar as próprias rotas para substituir o roteamento padrão do Azure. A capacidade de criar rotas personalizadas será útil, por exemplo, se você quiser rotear o tráfego entre sub-redes por meio de um firewall. Neste artigo, você aprenderá a:

> [!div class="checklist"]
> * Criar uma tabela de rotas
> * Criar uma rota
> * Associar uma tabela de rotas a uma sub-rede de rede virtual
> * Testar o roteamento
> * Solucionar problemas de roteamento

Se você não tiver uma assinatura do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se você optar por instalar e usar a CLI localmente, este início rápido exigirá a execução da CLI do Azure versão 2.0.28 ou posterior. Para saber qual é a versão, execute `az --version`. Se você precisa instalar ou atualizar, consulte [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli). 

## <a name="create-a-route-table"></a>Criar uma tabela de rotas

Por padrão, o Azure roteia o tráfego entre todas as sub-redes em uma rede virtual. Para saber mais sobre as rotas padrão do Azure, consulte [Rotas do sistema](virtual-networks-udr-overview.md). Para substituir o roteamento padrão do Azure, você cria uma tabela de rotas que contém rotas e associa a tabela de rotas a uma sub-rede de rede virtual.

Antes que você possa criar uma tabela de rotas, crie um grupo de recursos com [az group create](/cli/azure/group#az_group_create) para todos os recursos criados neste artigo. 

```azurecli-interactive
# Create a resource group.
az group create \
  --name myResourceGroup \
  --location eastus
``` 

Crie uma tabela de rotas com [az network route-table create](/cli/azure/network/route#az_network_route_table_create). O exemplo a seguir cria uma tabela de rotas nomeada *myRouteTablePublic*. 

```azurecli-interactive 
# Create a route table
az network route-table create \
  --resource-group myResourceGroup \
  --name myRouteTablePublic
```

## <a name="create-a-route"></a>Criar uma rota

Uma tabela de rotas contém zero ou mais rotas. Crie uma rota na tabela de rotas com [az network route-table route create](/cli/azure/network/route-table/route#az_network_route_table_route_create). 

```azurecli-interactive
az network route-table route create \
  --name ToPrivateSubnet \
  --resource-group myResourceGroup \
  --route-table-name myRouteTablePublic \
  --address-prefix 10.0.1.0/24 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address 10.0.2.4
``` 

A rota direcionará todo o tráfego destinado ao prefixo de endereço 10.0.1.0/24 por meio de uma solução de virtualização de rede com o endereço IP 10.0.2.4. A solução de virtualização de rede e sub-rede com o prefixo de endereço especificado serão criadas em etapas posteriores. A rota substitui o roteamento padrão do Azure, que roteia o tráfego entre sub-redes diretamente. Cada rota especifica um tipo do próximo salto. O tipo do próximo salto instrui o Azure como rotear o tráfego. Nesse exemplo, o tipo do próximo salto é *VirtualAppliance*. Para saber mais sobre todos os tipos disponíveis do próximo salto no Azure, e quando utilizá-los, consulte [tipos do próximo salto](virtual-networks-udr-overview.md#custom-routes).

## <a name="associate-a-route-table-to-a-subnet"></a>Associar uma tabela de rotas a uma sub-rede

Antes de poder associar uma tabela de rotas a uma sub-rede, será necessário criar uma rede virtual e uma sub-rede. Crie uma rede virtual com uma sub-rede com [az network vnet create](/cli/azure/network/vnet#az_network_vnet_create).

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --address-prefix 10.0.0.0/16 \
  --subnet-name Public \
  --subnet-prefix 10.0.0.0/24
```

Crie duas sub-redes adicionais com [az network vnet subnet create](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_create).

```azurecli-interactive
# Create a private subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name Private \
  --address-prefix 10.0.1.0/24

# Create a DMZ subnet.
az network vnet subnet create \
  --vnet-name myVirtualNetwork \
  --resource-group myResourceGroup \
  --name DMZ \
  --address-prefix 10.0.2.0/24
```

É possível associar uma tabela de rotas para zero ou mais sub-redes. Uma sub-rede pode ter zero ou uma tabela de rotas associada a ela. O tráfego de saída de uma sub-rede é roteado com base nas rotas padrão do Azure e quaisquer rotas personalizadas adicionadas a uma tabela de rotas associadas a uma sub-rede. Associe a tabela de rotas *myRouteTablePublic* à sub-rede *Pública* com [az network vnet subnet update](/cli/azure/network/vnet/subnet#az_network_vnet_subnet_update).

```azurecli-interactive
az network vnet subnet update \
  --vnet-name myVirtualNetwork \
  --name Public \
  --resource-group myResourceGroup \
  --route-table myRouteTablePublic
```

Antes de implantar as tabelas de rotas para uso em produção, é recomendável que você cuidadosamente se familiarizar com o [roteamento no Azure](virtual-networks-udr-overview.md) e os [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

## <a name="test-routing"></a>Testar o roteamento

Para testar o roteamento, será necessário criar uma máquina virtual que serve como a solução de virtualização de rede em que a rota criada em uma etapa anterior passa. Após criar a solução de virtualização de rede, será necessário implantar uma máquina virtual nas sub-redes *Pública* e *Privada*. Em seguida, você roteará o tráfego da sub-rede *Pública* para a sub-rede *Privada* por meio da solução de virtualização de rede.

### <a name="create-a-network-virtual-appliance"></a>Criar uma solução de virtualização de rede

Em uma etapa anterior, você criou uma rota que especificou uma solução de virtualização como o tipo do próximo salto. Uma máquina virtual executando um aplicativo de rede é frequentemente referida como uma solução de virtualização de rede. Em ambientes de produção, a solução de virtualização de rede que você implanta é frequentemente uma máquina virtual pré-configurada. Várias soluções de virtualização de rede estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1). Neste artigo, é criada uma máquina virtual básica. 

Criar um dispositivo de rede virtual no *DMZ* sub-rede com [criar vm az](/cli/azure/vm#az_vm_create). Quando você cria uma máquina virtual, o Azure cria e atribui um endereço IP público para a máquina virtual, por padrão. O `--public-ip-address ""` parâmetro instrui o Azure não para criar e atribuir um endereço IP público para a máquina virtual, desde que a máquina virtual não precisa estar conectado a partir da Internet. Se as chaves SSH ainda não existirem em uma localização de chave padrão, o comando criará. Para usar um conjunto específico de chaves, use a opção `--ssh-key-value`.

```azure-cli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmNva \
  --image UbuntuLTS \
  --public-ip-address "" \
  --subnet DMZ \
  --vnet-name myVirtualNetwork \
  --generate-ssh-keys
```

A criação da máquina virtual demora alguns minutos. Não prossiga para a próxima etapa até que o Azure termine de criar a máquina virtual e retorne a saída sobre a máquina virtual. Em ambientes de produção, a solução de virtualização de rede que você implanta é frequentemente uma máquina virtual pré-configurada. Várias soluções de virtualização de rede estão disponíveis no [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?search=network%20virtual%20appliance&page=1).

Você deve habilitar o encaminhamento de IP para cada [interface de rede](virtual-network-network-interface.md) do Azure anexada a uma máquina virtual que encaminha o tráfego destinado a qualquer endereço IP que não esteja atribuído à interface de rede. Quando você criou a solução de virtualização de rede na sub-rede *DMZ*, o Azure criou automaticamente uma interface de rede nomeada *myVmNvaVMNic*, anexou a interface de rede à máquina virtual e atribuiu o endereço IP privado *10.0.2.4* à interface de rede. Ative o encaminhamento de IP para a interface de rede com [az network nic update](/cli/azure/network/nic#az_network_nic_update).

```azurecli-interactive
az network nic update \
  --name myVmNvaVMNic \
  --resource-group myResourceGroup \
  --ip-forwarding true
```

Dentro da máquina virtual, o sistema operacional, ou um aplicativo em execução na máquina virtual, também pode ser capaz de encaminhar o tráfego. Quando você implanta uma solução de virtualização de rede em um ambiente de produção, a solução geralmente filtra, registra ou executa alguma outra função antes de encaminhar o tráfego. No entanto, neste artigo, o sistema operacional simplesmente encaminha todo o tráfego que recebe. Habilite o encaminhamento de IP no sistema operacional da máquina virtual com [az vm extension set](/cli/azure/vm/extension#az_vm_extension_set), que executa um comando que habilita o encaminhamento de IP no sistema operacional.

```azurecli-interactive
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVmNva \
  --name customScript \
  --publisher Microsoft.Azure.Extensions \
  --settings '{"commandToExecute":"sudo sysctl -w net.ipv4.ip_forward=1"}'
```
O comando pode demorar até um minuto para executar.

### <a name="create-virtual-machines"></a>Criar máquinas virtuais

Crie duas máquinas virtuais na rede virtual para que seja possível validar esse tráfego da sub-rede *Pública* roteada para a sub-rede *Privada* por meio da solução de virtualização de rede em uma etapa posterior. 

Crie uma máquina virtual na sub-rede *Pública* com [az vm create](/cli/azure/vm#az_vm_create). O parâmetro `--no-wait` permite que o Azure execute o comando em segundo plano para que você possa continuar com o próximo comando. Para simplificar este artigo, uma senha é utilizada. Normalmente, chaves são usadas em implantações de produção. Se você utilizar chaves, também deverá configurar o encaminhamento de agente SSH. Para obter mais informações, consulte a documentação do cliente SSH. Substitua `<replace-with-your-password>` no comando a seguir com uma senha de sua escolha.

```azurecli-interactive
adminPassword="<replace-with-your-password>"

az vm create \
  --resource-group myResourceGroup \
  --name myVmWeb \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Public \
  --admin-username azureuser \
  --admin-password $adminPassword \
  --no-wait
```

Crie uma máquina virtual na sub-rede *Privada*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVmMgmt \
  --image UbuntuLTS \
  --vnet-name myVirtualNetwork \
  --subnet Private \
  --admin-username azureuser \
  --admin-password $adminPassword
```

A criação da máquina virtual demora alguns minutos. Depois que a máquina virtual é criada, a CLI do Azure mostra informações semelhantes ao seguinte exemplo: 

```azurecli 
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVmMgmt",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.1.4",
  "publicIpAddress": "13.90.242.231",
  "resourceGroup": "myResourceGroup"
}
```
Anote o **publicIpAddress**. Esse endereço será usado para acessar a máquina virtual da Internet em uma etapa posterior.

### <a name="route-traffic-through-a-network-virtual-appliance"></a>Rotear o tráfego por meio de uma solução de virtualização de rede

Use o comando a seguir para criar uma sessão SSH com a máquina virtual *myVmMgmt*. Substitua *<publicIpAddress>* pelo endereço IP público de sua máquina virtual. No exemplo acima, o endereço IP é *13.90.242.231*.

```bash 
ssh azureuser@<publicIpAddress>
```

Quando uma senha for solicitada, insira a senha que você selecionou em [Criar máquinas virtuais](#create-virtual-machines).

Utilize o comando a seguir para instalar o rastreamento de rotas na máquina virtual *myVmMgmt*:

```bash 
sudo apt-get install traceroute
```

Utilize o comando a seguir para testar o roteamento do tráfego para a máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*.

```bash
traceroute myvmweb
```

A resposta é semelhante ao seguinte exemplo:

```bash
traceroute to myvmweb (10.0.0.4), 30 hops max, 60 byte packets
1  10.0.0.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```

Você pode ver que o tráfego é roteado diretamente da máquina virtual *myVmMgmt* para a máquina virtual *myVmWeb*. As rotas padrão do Azure roteiam o tráfego diretamente entre sub-redes. 

Use o comando a seguir para SSH para a máquina virtual *myVmWeb* da máquina virtual *myVmMgmt*:

```bash 
ssh azureuser@myVmWeb
```

Utilize o comando a seguir para instalar o rastreamento de rotas na máquina virtual *myVmWeb*:

```bash 
sudo apt-get install traceroute
```

Utilize o comando a seguir para testar o roteamento do tráfego para a máquina virtual *myVmMgmt* da máquina virtual *myVmWeb*.

```bash
traceroute myvmmgmt
```

A resposta é semelhante ao seguinte exemplo:

```bash
traceroute to myvmmgmt (10.0.1.4), 30 hops max, 60 byte packets
1  10.0.2.4 (10.0.2.4)  0.781 ms  0.780 ms  0.775 ms
2  10.0.1.4 (10.0.0.4)  1.404 ms  1.403 ms  1.398 ms
```
Você pode ver que o primeiro salto é 10.0.2.4, que é o endereço IP privado da solução de virtualização de rede. O segundo salto é 10.0.1.4, o endereço IP privado da máquina virtual *myVmMgmt*. A rota adicionada à tabela de rotas *myRouteTablePublic* e associada à sub-rede *Pública* fez o Azure encaminhar o tráfego através de NVA, em vez de diretamente para a sub-rede *Privada*.

Feche as sessões SSH para ambas máquinas virtuais *myVmWeb* e *myVmMgmt*.

## <a name="troubleshoot-routing"></a>Solucionar problemas de roteamento

Como você aprendeu nas etapas anteriores, o Azure aplica rotas padrão que opcionalmente podem ser substituídas pelas suas próprias rotas. Às vezes, o tráfego pode não ser roteado como você espera que seja. Use [az network watcher show-next-hop](/cli/azure/network/watcher#az_network_watcher_show_next_hop) para determinar como o tráfego é roteado entre duas máquinas virtuais. Por exemplo, o comando a seguir testa o roteamento de tráfego da máquina virtual *myVmWeb* (10.0.0.4) para a máquina virtual *myVmMgmt* (10.0.1.4):

```azurecli-interactive
# Enable network watcher for east region, if you don't already have a network watcher enabled for the region.
az network watcher configure --locations eastus --resource-group myResourceGroup --enabled true

```azurecli-interactive
az network watcher show-next-hop \
  --dest-ip 10.0.1.4 \
  --resource-group myResourceGroup \
  --source-ip 10.0.0.4 \
  --vm myVmWeb \
  --out table
```
A seguinte saída será retornada após um curto período:

```azurecli
NextHopIpAddress    NextHopType       RouteTableId
------------------  ---------------- ---------------------------------------------------------------------------------------------------------------------------
10.0.2.4            VirtualAppliance  /subscriptions/<Subscription-Id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/routeTables/myRouteTablePublic
```

A saída informa que o endereço IP do próximo salto para o tráfego de *myVmWeb* para *myVmMgmt* é 10.0.2.4 (a máquina virtual *myVmNva*), que o tipo do próximo salto é *VirtualAppliance* e que a tabela de rotas que causa o roteamento é *myRouteTablePublic*.

As rotas efetivas para cada interface de rede são uma combinação das rotas padrão do Azure e quaisquer rotas que você definir. Consulte todas as rotas efetivas para uma interface de rede em uma máquina virtual com [az network nic show-effective-route-table](/cli/azure/network/nic#az_network_nic_show_effective_route_table). Por exemplo, para mostrar as rotas efetivas para a interface de rede *MyVmWebVMNic* na máquina virtual *myVmWeb*, insira o seguinte comando:

```azurecli-interactive
az network nic show-effective-route-table \
  --name MyVmWebVMNic \
  --resource-group myResourceGroup
```

Todas as rotas padrão e a rota que você adicionou na etapa anterior são retornadas.

## <a name="clean-up-resources"></a>Limpar recursos

Quando não for mais necessário, use [az group delete](/cli/azure/group#az_group_delete) para remover o grupo de recursos e todos os recursos que ele contém.

```azurecli-interactive 
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Próximas etapas

Neste artigo, você criou uma tabela de rotas e associou-a a uma sub-rede. Você criou uma solução de virtualização de rede que roteou o tráfego de uma sub-rede pública para uma sub-rede privada. Embora seja possível implantar muitos recursos do Azure dentro de uma rede virtual, os recursos para alguns serviços PaaS do Azure não podem ser implantados em uma rede virtual. Ainda assim, é possível restringir acesso aos recursos de alguns serviços PaaS do Azure somente para tráfego de uma sub-rede de rede virtual. Avance para o próximo tutorial para aprender como restringir o acesso à rede para recursos PaaS do Azure.

> [!div class="nextstepaction"]
> [Restringir o acesso à rede para recursos PaaS](virtual-network-service-endpoints-configure.md#azure-cli)
