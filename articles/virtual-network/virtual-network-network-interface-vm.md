---
title: "Adicionar ou remover interfaces de rede de máquinas virtuais do Azure | Microsoft Docs"
description: "Saiba como adicionar ou remover NICs (interfaces de rede) de máquinas virtuais."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/04/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: afa23b1395b8275e72048bd47fffcf38f9dcd334
ms.openlocfilehash: 0d609b20040572e3fb371a277603109d64a0ba37
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---

# <a name="add-network-interfaces-to-or-remove-from-virtual-machines"></a>Adicionar ou remover interfaces de rede de máquinas virtuais

Saiba como adicionar uma NIC (interface de rede) existente ao criar uma VM ou adicionar ou remover as NICs de uma VM existente no estado parado (desalocado). Uma NIC permite que uma VM (máquina virtual) do Azure comunique-se com a Internet, com o Azure e com recursos locais. Uma VM pode ter uma ou mais NICs. 

Se você precisa adicionar, alterar ou remover endereços IP para uma NIC, leia o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md). Se você precisa criar, alterar ou excluir NICs, leia o artigo [Configurações e tarefas da NIC](virtual-network-network-interface.md).

## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Saiba mais sobre quantas NICs o tamanho de VM Linux e Windows aceita examinando os artigos de tamanho de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Faça logon no portal do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando comandos do PowerShell para concluir tarefas neste artigo, instale e configure o Azure PowerShell executando as etapas no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, instale e configure a CLI do Azure executando as etapas no artigo [Como instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Você deve ter a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre comandos da CLI, digite `az <command> --help`.

## <a name="about"></a>Sobre NICs e VMs

Você pode adicionar (anexar) uma NIC existente a uma VM ao criar a VM, desde que a NIC não esteja conectada a outra VM no momento. Você pode adicionar uma NIC ou remover (desanexar) uma NIC de uma VM existente, desde que a VM esteja no estado parado (desalocado). Se você criar uma VM usando o portal do Azure, o portal criará uma NIC para você com as configurações padrão. O portal não permite que você:

- Especifique uma NIC existente a adicionar ao criar a VM
- Criar uma VM com diversos NICs
- Especifique um nome para a NIC (o portal cria a NIC com um nome padrão)

Você pode usar o Azure PowerShell ou a CLI para criar uma VM ou uma NIC com todos os atributos anteriores para os quais você não pode usar o portal. Antes de concluir as tarefas nas seções a seguir, considere as seguintes restrições e comportamentos:

- Todos os tamanhos de VM dão suporte para pelo menos duas NICs, mas alguns tamanhos de VM dão suporte a mais de duas NICs. No passado, alguns tamanhos de VM aceitavam apenas uma NIC. Para saber a quantas NICs cada tamanho de VM dá suporte, leia os artigos sobre tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 
- No passado, as NICs só podiam ser adicionadas às VMs que ofereciam suporte a várias NICs e foram criadas com pelo menos duas NICs. Não era possível adicionar uma NIC a uma VM criada com uma NIC, mesmo que o tamanho da VM oferecesse suporte a várias NICs. De modo inverso, só era possível remover NICs de uma VM com pelo menos três NICs, porque VMs criadas com pelo menos duas NICs sempre precisavam ter um mínimo de duas NICs. Nenhuma dessas restrições se aplicam mais. Agora você pode criar uma VM com qualquer número de NICs (até o número aceito pelo tamanho da VM) e adicionar ou remover qualquer número de NICs (de VMs no estado parado [desalocado]), desde que a VM sempre tenha pelo menos uma NIC.
- Por padrão, a primeira NIC em uma VM é definida como a NIC *primária*. Todas as outras NICs na VM são NICs *secundárias*.
- Os servidores DHCP do Azure atribuem um gateway padrão às NICs primárias, mas não às NICs secundárias. Uma vez que não é atribuído um gateway padrão a NICs secundárias, elas não conseguem se comunicar com recursos fora de sua sub-rede, por padrão. Para habilitar NICs secundárias em uma VM do Windows a comunicar-se com recursos fora da sub-rede, adicione rotas ao sistema operacional usando o comando `route add` de uma linha de comando do Windows. Para VMs do Linux, uma vez que o comportamento padrão usa roteamento de host fraco, é recomendável restringir o tráfego para NICs secundárias a uma única sub-rede. Se você precisar de conectividade fora da sub-rede para NICs secundárias, habilite o roteamento baseado em política para garantir que os tráfegos de entrada e de saída usem a mesma NIC.
- Por padrão, todo o tráfego de saída da VM é enviado pelo endereço IP atribuído à configuração de IP primária da NIC primária. Você controla qual endereço IP é usado para tráfego de saída no sistema operacional da VM, mas, por padrão, a NIC primária é utilizada.
- No passado, todas as VMs dentro do mesmo conjunto de disponibilidade deviam ter uma única ou diversas NICs. VMs com qualquer número de NICs agora podem existir no mesmo conjunto de disponibilidade até o número aceito pelo tamanho da VM. Porém, você pode adicionar uma VM a um conjunto de disponibilidade apenas quando ela é criada. Para saber mais sobre os conjuntos de disponibilidade, leia o artigo [Gerenciamento da disponibilidade de VMs no Azure](../virtual-machines/windows/manage-availability.md?toc=%2fazure%2fvirtual-network%2ftoc.json#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy).
- Embora NICs na mesma VM possam ser conectadas a diferentes sub-redes em uma VNet, todas as NICs devem ser conectadas à mesma VNet.
- Você pode adicionar qualquer endereço IP para qualquer configuração de IP de qualquer NIC primário ou secundário para um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre endereços IP e configurações, consulte o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).
- Excluir uma VM não exclui as NICs conectadas a ela. Quando uma VM é excluída, as NICs são desconectadas da VM. Você pode adicionar as NICs a diferentes VMs ou excluí-las.

## <a name="vm-create"></a>Adicionar NICs existentes a uma nova VM
Ao criar uma VM por meio do portal, o portal cria uma NIC com configurações padrão e anexa-a à VM para você. Não é possível adicionar NICs existentes a uma VM nova nem criar uma VM com várias NICs usando o portal do Azure. Você pode fazer essas duas ações usando a CLI ou o PowerShell. Você pode adicionar tantas NICs a uma VM quantas o tamanho da VM que você estiver criando aceitar. Para saber mais sobre a quantas NICs cada tamanho de VM dá suporte, leia os artigos sobre tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). As NICs adicionadas a uma VM no momento não podem ser anexadas a outra VM. Para saber mais sobre a criação de NICs, leia o artigo [Configurações e tarefas da NIC](virtual-network-network-interface.md#create-nic).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az vm create](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v2.5.0/new-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-add-nic"></a>Adicionar NICs existentes a uma VM existente

Você pode adicionar tantas NICs a uma VM quantas o tamanho da VM à qual você estiver adicionado as NICs aceitar. Para saber a quantas NICs cada tamanho de VM dá suporte, leia os artigos sobre tamanhos de VM [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) ou [Windows](../virtual-machines/virtual-machines-windows-sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A VM à qual você deseja adicionar uma NIC deve dar suporte a várias NICs e estar com o estado parado (desalocado). As NICs que você deseja adicionar não podem estar anexadas a outra VM no momento. Você não pode adicionar NICs a uma VM existente usando o portal do Azure. Você deve usar a CLI ou o PowerShell para adicionar NICs a uma VM existente.

|Ferramenta|Command|
|---|---|
|CLI|[az vm nic add](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#add)|
|PowerShell|[Add-AzureRmVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/add-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-view-nic"></a> Exibir NICs para uma VM

Você pode exibir as NICs anexadas a uma VM para saber mais sobre a configuração de cada NIC e os endereços IP atribuídos a cada NIC. 

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta atribuída à função de Proprietário, Colaborador ou Colaborador de rede para sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *máquinas virtuais*. Quando **máquinas virtuais** aparecerem nos resultados da pesquisa, clique nelas.
3. Na folha **Máquinas virtuais** que é exibida, clique no nome da VM para a qual você exibir NICs.
4. Na seção **CONFIGURAÇÕES** da folha da máquina virtual que é exibida para a VM selecionada, clique em **Interfaces de rede**. Para saber mais sobre as configurações da NIC e como alterá-las, leia o artigo [Configurações e tarefas da NIC](virtual-network-network-interface.md). Saiba mais sobre como adicionar, alterar ou remover endereços IP atribuídos a uma NIC, leia o artigo [Adicionar, alterar ou remover endereços IP](virtual-network-network-interface-addresses.md).

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az vm show](/cli/azure/vm?toc=%2fazure%2fvirtual-network%2ftoc.json#show)|
|PowerShell|[Get-AzureRmVM](/powershell/resourcemanager/azurerm.compute/v1.3.4/get-azurermvm?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="vm-remove-nic"></a> Remover NICs de uma VM

A VM da qual você deseja remover uma NIC deve estar no estado parado (desalocado) e ter pelo menos duas NICs anexadas a ela no momento. Você pode remover qualquer NIC, mas a VM sempre deve manter ao menos uma NIC anexada. Se você remover uma NIC primária, o Azure designará o atributo primário à NIC que está anexada à VM há mais tempo. Você pode designar qualquer NIC como primária por conta própria. Não é possível remover NICs de uma VM nem definir o atributo principal para uma NIC usando o portal do Azure, embora ambas as operações possam ser feitas usando a CLI ou o PowerShell. 

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az vm nic remove](/cli/azure/vm/nic?toc=%2fazure%2fvirtual-network%2ftoc.json#remove)|
|PowerShell|[Remove-AzureRMVMNetworkInterface](/powershell/resourcemanager/azurerm.compute/v2.5.0/remove-azurermvmnetworkinterface?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Próximas etapas
Para criar uma VM com várias NICs ou endereços IP, leia os seguintes artigos:

**Comandos**

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma única VM da NIC com vários endereços IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

