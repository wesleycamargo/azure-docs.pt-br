---
title: "Adicionar ou remover interfaces de rede de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como adicionar adaptadores de rede ou remover adaptadores de rede de máquinas virtuais."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: jdial
ms.openlocfilehash: abe6abb942d206330e809f3aef388b846d7d7c7f
ms.sourcegitcommit: 3f33787645e890ff3b73c4b3a28d90d5f814e46c
ms.translationtype: HT
ms.contentlocale: pt-BR
ms.lasthandoff: 01/03/2018
---
# <a name="add-network-interfaces-to-or-remove-network-interfaces-from-virtual-machines"></a>Adicionar adaptadores de rede ou remover adaptadores de rede de máquinas virtuais

Saiba como adicionar um adaptador de rede existente ao criar uma VM ou adicionar ou remover adaptadores de rede de uma VM existente no estado parado (desalocado). Um adaptador de rede permite que uma VM (máquina virtual) do Azure comunique-se com a Internet, com o Azure e com recursos locais. Uma VM pode ter um ou mais adaptadores de rede. 

Se você precisar adicionar, alterar ou remover endereços IP de um adaptador de rede, leia o artigo [Gerenciar endereços IP de adaptador de rede](virtual-network-network-interface-addresses.md). Se você precisar criar, alterar ou excluir adaptadores de rede, leia o artigo [Gerenciar adaptadores de rede](virtual-network-network-interface.md).

## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Faça logon no [Portal](https://portal.azure.com) do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se usar comandos do PowerShell para concluir as tarefas neste artigo, [instale e configure o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`. Em vez de instalar o Azure PowerShell, você pode usar o Azure Cloud Shell. O Azure Cloud Shell é um PowerShell gratuito que pode ser executado diretamente no portal do Azure. Ele tem o Azure PowerShell pré-instalado e configurado para usar com sua conta. Para usar o Cloud Shell, clique no botão Cloud Shell **> _** na parte superior do [portal](https://portal.azure.com) e selecione o PowerShell no canto superior esquerdo, quando aparecer a janela do shell.
- Se você for usar os comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, [instale e configure a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`. Em vez de instalar a CLI e seus pré-requisitos, use o Azure Cloud Shell. O Azure Cloud Shell é um shell Bash gratuito que podem ser executado diretamente no portal do Azure. Ele tem a CLI do Azure instalada e configurada para usar com sua conta. Para usar o Cloud Shell, clique no botão Cloud Shell **> _** na parte superior do [portal](https://portal.azure.com) e selecione o Bash no canto superior esquerdo, quando aparecer a janela do shell.

## <a name="vm-create"></a>Adicionar adaptadores de rede existentes a uma nova VM

Ao criar uma VM por meio do portal, o portal cria um adaptador de rede com configurações padrão e anexa-o à VM para você. Não é possível adicionar adaptadores de rede existentes a uma nova VM nem criar uma VM com vários adaptadores de rede usando o Portal do Azure. Você pode fazer essas duas ações usando a CLI ou o PowerShell. Antes de usar o PowerShell ou a CLI para criar uma VM com um adaptador de rede já existente, no entanto, familiarize-se com as [restrições](#constraints). Se você criar uma máquina virtual com vários adaptadores de rede, também deverá configurar o sistema operacional para usá-las corretamente depois que a VM é criada. Para obter detalhes, consulte Configurar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para vários adaptadores de rede.

**Comandos** Antes de criar a VM, crie um adaptador de rede usando as etapas em [Criar um adaptador de rede](virtual-network-network-interface.md#create-a-network-interface).

|Ferramenta|Get-Help|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/module/azurerm.compute/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Adicionar um adaptador de rede a uma VM existente

1. Faça logon no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, procure o nome da VM a qual você deseja adicionar o adaptador de rede ou procure a máquina virtual clicando em **Todos os serviços** e, em seguida, **Máquinas virtuais**. Depois de localizar a máquina virtual, clique nela. A VM à qual você deseja adicionar um adaptador de rede deve dar suporte ao número de adaptadores de rede que você deseja adicionar. Para saber a quantos adaptadores de rede cada tamanho de VM dá suporte, leia os artigos sobre tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).  
3. Clique em **Visão geral**, em **CONFIGURAÇÕES**. Clique em **Parar** e aguarde até que o **Status** da VM seja alterado para *Parado (desalocado)*. 
4. Clique em **Rede** em **CONFIGURAÇÕES**.
5. Clique em **Anexar o adaptador de rede**. Na lista de adaptadores de rede existentes que não estão conectados a outra VM, clique no adaptador de rede que você deseja anexar. O adaptador de rede que você selecionar não pode ter rede acelerada habilitada, não pode ter endereço de IPv6 atribuído a ela e deve existir na mesma rede virtual em que está o adaptador de rede conectado à VM no momento. Se você ainda não tem um adaptador de rede, deve primeiro criar um. Para criar um adaptador de rede, clique em **Criar adaptador de rede**. Para saber mais sobre como criar um adaptador de rede, consulte [Criar um adaptador de rede](virtual-network-network-interface.md#create-a-network-interface). Para saber mais sobre as restrições adicionais durante a adição de adaptadores de rede a máquinas virtuais, consulte [Restrições](#constraints).
6. Clique em **OK**.
7. Clique em **Visão geral**, em **CONFIGURAÇÕES**. Clique em **Iniciar** para iniciar a máquina virtual.
8. Configure o sistema operacional da VM para usar adequadamente vários adaptadores de rede. Para obter detalhes, consulte Configurar [Linux](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) ou [Windows](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-guest-os-for-multiple-nics) para vários adaptadores de rede.

|Ferramenta|Get-Help|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add) (referência) ou [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-a-vm)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/module/azurerm.compute/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-a-nic-to-an-existing-vm)|

## <a name="vm-view-nic"></a> Exibir adaptadores de rede para uma VM

Você pode exibir os adaptadores de rede atualmente anexados a uma VM para saber mais sobre a configuração de cada adaptador de rede e os endereços IP atribuídos a cada adaptador de rede. 

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função de Proprietário, Colaborador ou Colaborador de rede para sua assinatura. Para saber mais sobre como atribuir funções às contas, veja [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor).
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *máquinas virtuais*. Quando **máquinas virtuais** aparecerem nos resultados da pesquisa, clique nelas.
3. Clique no nome da VM para a qual você deseja exibir os adaptadores de rede.
4. Na seção **CONFIGURAÇÕES** da máquina virtual selecionada, clique em **Rede**. Para saber mais sobre as configurações de adaptador de rede e como alterá-las, consulte [Gerenciar adaptadores de rede](virtual-network-network-interface.md). Para saber mais sobre como adicionar, alterar ou remover endereços IP atribuídos a um adaptador de rede, veja [Gerenciar endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Get-Help|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Remover um adaptador de rede de uma VM

1. Faça logon no Portal do Azure.
2. Na caixa de pesquisa na parte superior do portal, procure o nome da VM da qual você deseja remover (desanexar) o adaptador de rede ou procure a máquina virtual clicando em **Todos os serviços** e, em seguida, **Máquinas virtuais**. Depois de localizar a máquina virtual, clique nela.
3. Clique em **Visão geral**, em **CONFIGURAÇÕES**. Clique em **Parar** e aguarde até que o **Status** da VM seja alterado para *Parado (desalocado)*. 
4. Clique em **Rede** em **CONFIGURAÇÕES**.
5. Clique em **Desanexar adaptador de rede**. Na lista de adaptadores de rede atualmente conectados à máquina virtual, clique no adaptador de rede que você deseja desanexar. Se apenas um adaptador de rede estiver listado, você não poderá desanexá-lo, pois a máquina virtual sempre deve ter pelo menos um adaptador de rede anexado a ela.
6. Clique em **OK**.

**Comandos**

|Ferramenta|Get-Help|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove) (referência) ou [etapas detalhadas](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-a-vm)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/module/azurerm.compute/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json) (referência) ou [etapas detalhadas](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#remove-a-nic-from-an-existing-vm)|

## <a name="next-steps"></a>Próximas etapas
Para criar uma VM com vários adaptadores de rede ou endereços IP, leia os seguintes artigos:

**Comandos**

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma VM com um NIC com vários endereços IPv4|[CLI](virtual-network-multiple-ip-addresses-cli.md), [PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|
|Criar uma VM com um NIC com um endereço IPv6 privado (atrás de um Azure Load Balancer)|[CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json), [modelo do Azure Resource Manager](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="constraints"></a>Restrições

- Uma VM deve ter pelo menos um adaptador de rede anexado.
- Uma VM só pode ter tantos adaptadores de rede anexados quantos o tamanho da VM der suporte. Para saber mais sobre a quantos adaptadores de rede cada tamanho de VM dá suporte, consulte os tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Todos os tamanhos aceitam pelo menos dois adaptadores de rede.
- Os adaptadores de rede que você adicionar a uma VM no momento não podem ser anexados a outra VM. Para saber mais sobre como criar adaptadores de rede, consulte [Criar um adaptador de rede](virtual-network-network-interface.md#create-a-network-interface).
- No passado, adaptadores de rede podiam apenas ser adicionados a VMs que tinham suporte a vários adaptadores de rede e eram criadas com pelo menos dois adaptadores de rede. Você não podia adicionar um adaptador de rede a uma VM criada com um adaptador de rede, mesmo que o tamanho da VM desse suporte a vários adaptadores de rede. Por outro lado, você só pode remover interfaces de rede de uma VM com pelo menos três adaptadores de rede, pois VMs criadas com a rede pelo menos dois adaptadores sempre precisam ter pelo menos dois adaptadores de rede. Nenhuma dessas restrições se aplicam mais. Agora você pode criar uma VM com qualquer número de adaptadores de rede (até o número com suporte pelo tamanho da VM).
- Por padrão, o primeiro adaptador de rede anexado a uma VM é definido como o adaptador de rede *primário*. Todos os outros adaptadores de rede na VM são adaptadores de rede *secundários*.
- Embora você possa controlar a qual adaptador de rede é enviado tráfego de saída, por padrão, todo o tráfego de saída da VM é enviado pelo endereço IP atribuído à configuração de IP primária do adaptador de rede primário.
- No passado, todas as VMs dentro do mesmo conjunto de disponibilidade precisavam ter um único adaptador de rede ou vários adaptadores de rede. VMs com qualquer número de adaptadores de rede agora podem existir no mesmo conjunto de disponibilidade até o número com suporte pelo tamanho da VM. Porém, você pode adicionar uma VM a um conjunto de disponibilidade apenas quando ela é criada. Para saber mais sobre os conjuntos de disponibilidade, leia o artigo [Gerenciamento da disponibilidade de VMs no Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Enquanto os adaptadores de rede na mesma VM podem ser conectados a diferentes sub-redes em uma rede VNet, os adaptadores de rede devem todos estar conectados à mesma VNet.
- Você pode adicionar qualquer endereço IP para qualquer configuração de IP de qualquer adaptador de rede primário ou secundário para um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para o adaptador de rede primário podia ser adicionado a um pool de back-end. Para saber mais sobre endereços IP e configurações, consulte o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).
- Excluir uma VM não exclui os adaptadores de rede anexados a ele. Quando uma VM é excluída, os adaptadores de rede são desanexados da VM. Você pode adicionar os adaptadores de rede a diferentes VMs ou excluí-los.
- Se uma adaptador de rede tiver um endereço IPv6 privado atribuído a ele, você deverá adicioná-lo (anexá-lo) a uma máquina virtual ao criá-la. Não é possível adicionar um adaptador de rede com um endereço IPv6 atribuído a uma VM depois que a VM é criada. Se você adicionar um adaptador de rede com um endereço IPv6 privado atribuído durante a criação de uma máquina virtual, você somente poderá adicionar esse adaptador de rede à máquina virtual, independentemente de quantos adaptadores de rede o tamanho da VM suporta. Confira [Endereços IP do adaptador de rede](virtual-network-network-interface-addresses.md) para saber mais sobre como atribuir endereços IP a adaptadores de rede.
- Similar ao IPv6, você não pode anexar um adaptador de rede com rede acelerada habilitada a uma VM após a criação da VM. Além disso, para tirar proveito da rede rápida, você também deve concluir as etapas dentro do sistema operacional da VM. Para saber mais sobre a rede acelerada e outras restrições ao usá-la, consulte Rede acelerada para máquinas virtuais do [Windows](create-vm-accelerated-networking-powershell.md) ou do [Linux](create-vm-accelerated-networking-cli.md).
