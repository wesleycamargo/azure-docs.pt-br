---
title: "Configurar endereços IP para as interfaces de rede do Azure | Microsoft Docs"
description: "Saiba como adicionar, alterar e remover endereços públicos e privados de IP para NICs."
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
ms.openlocfilehash: cf7dbc648136897772bbd068687313eec16bed75
ms.contentlocale: pt-br
ms.lasthandoff: 05/12/2017


---

# <a name="add-change-or-remove-ip-addresses-for-azure-network-interfaces"></a>Adicionar, alterar ou remover endereços IP para as interfaces de rede do Azure

Saiba como adicionar, alterar e remover endereços IP públicos e privados para NICs (interfaces de rede). Endereços IP privados atribuídos a uma NIC permitem a uma VM comunicar-se com a Internet e com outros recursos conectados a VNet (rede virtual) do Azure. Endereços IP públicos atribuídos a uma NIC permitem a comunicação de entrada a uma VM da Internet. 

Se você precisa criar, alterar ou excluir NICs, leia o artigo [Configurações e tarefas da NIC](virtual-network-network-interface.md). Se você precisa adicionar ou remover NICs de máquinas virtuais, leia o artigo [Adicionar ou remover NICs](virtual-network-network-interface-vm.md). 


## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir quaisquer etapas em qualquer seção deste artigo:

- Examine o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para saber mais sobre os limites para endereços IP públicos e privados.
- Faça logon no portal do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se estiver usando comandos do PowerShell para concluir tarefas neste artigo, instale e configure o Azure PowerShell executando as etapas no artigo [Como instalar e configurar o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos commandlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se estiver usando comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, instale e configure a CLI do Azure executando as etapas no artigo [Como instalar e configurar a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`.

## <a name="about"></a>Sobre NICs e endereços IP

Cada NIC pode receber vários endereços IP públicos e privados dentro dos [limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits). Atribuir vários endereços IP a uma NIC é útil em cenários como:
- Hospede vários sites ou serviços com diferentes endereços IP e os certificados SSL em um único servidor.
- Uma VM usada como dispositivo de rede virtual, como um firewall ou balanceador de carga.
- A capacidade de adicionar qualquer um dos endereços IP privados para qualquer uma das NICs a um pool de back-end do Azure Load Balancer. No passado, somente o endereço IP primário para a NIC primária pode ser adicionado a um pool de back-end. Para saber mais sobre como balancear a carga de várias configurações de IP, leia o artigo [Balanceamento de carga de várias configurações de IP](../load-balancer/load-balancer-multiple-ip.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

Endereços IP são atribuídos a uma configuração de IP. Uma NIC sempre recebe uma configuração de IP **primária**, mas também pode receber várias configurações **secundárias**. Cada configuração de IP recebe um ou ambos os seguintes tipos de endereços:
- **Privado:** endereços IP privados habilitam uma VM a comunicar-se com outros recursos conectados à VNet em que a NIC está. Uma configuração de IP deve ter um endereço IP privado atribuído a ela. Os servidores DHCP do Azure atribuem o endereço IP privado para a configuração de IP primária da NIC para a NIC dentro sistema operacional da VM. Um endereço IP privado também permite que a VM faça comunicação de saída para a Internet. As conexões de saída são SNAT (convertidas para o endereço de rede de origem). Para saber mais sobre a conectividade de Internet de saída do Azure, leia o artigo [Conectividade de Internet de saída do Azure](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Você não pode realizar comunicação de entrada para o endereço IP privado de uma VM pela Internet.
- **Público:** endereços IP públicos habilitam a conectividade de entrada para uma VM da Internet. As conexões de entrada para a Internet não são SNAT. Você pode atribuir um endereço IP público a uma configuração de IP, mas isso não é necessário. Para saber mais sobre endereços IP públicos, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md).

Há limites ao número de endereços IP públicos e privados que você pode atribuir a uma NIC. Leia o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para obter detalhes.

Endereços IP públicos e privados podem ser atribuídos usando os seguintes métodos de alocação:
- **Dinâmico:** endereços IP públicos e privados dinâmicos são atribuídos por padrão. Os endereços dinâmicos poderão mudar se a VM for colocada em estado interrompido (desalocado) e então reiniciada. Se não quiser que o endereço IP mude durante a vida útil da VM, use um endereço estático.
- **Estático:** endereços estáticos não mudam até uma VM ser excluída. Você pode atribuir um endereço IP privado estático do espaço de endereço para a sub-rede à qual a NIC está conectada. Para saber mais sobre como o Azure atribui endereços IP públicos estáticos, leia o artigo [Endereço IP público](virtual-network-public-ip-address.md).

## <a name="create-ip-config"></a>Adicionar endereços IP

Você pode adicionar a uma NIC tantos endereços IP quantos forem necessários, desde que respeite os limites listados no artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits).

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Interfaces de rede** exibida, clique na NIC à qual deseja adicionar um endereço IP.
4. Clique em **Configurações de IP** na seção **CONFIGURAÇÕES** da folha para a NIC selecionada.
5. Clique em **+ Adicionar** na folha de configurações de IP que é exibida.
6. Especifique o seguinte e clique em **OK** para fechar a folha **Adicionar configuração de IP**:

    |Configuração|Obrigatório?|Detalhes|
    |---|---|---|
    |Nome|Sim|Deve ser exclusivo para a NIC|
    |Tipo|Sim|Como você está adicionando uma configuração de IP a uma NIC existente, e cada NIC deve ter uma configuração de IP primária, a sua única opção é **secundária**.|
    |Método de atribuição de endereço IP privado|Sim|Os endereços **dinâmicos** podem ser alterados se a VM for reiniciada depois de ter seu estado modificado para pausado (desalocada). O Azure atribui um endereço disponível do espaço de endereço da sub-rede à qual a NIC está conectada. Os endereços **estáticos** não serão liberados até que a NIC seja excluída. Especifique um endereço IP do intervalo de espaço de endereço da sub-rede que não esteja em uso por outra configuração de IP.|
    |Endereço IP público|Não|**Desabilitado:** no momento, nenhum recurso de endereço IP público está associado à configuração de IP. **Habilitado:** selecione um endereço IP público existente ou crie um novo. Para saber como criar um endereço IP público, leia o artigo [Endereços IP públicos](virtual-network-public-ip-address.md#create).|
7. Adicione manualmente endereços IP privados secundários ao sistema operacional da VM seguindo as instruções do artigo [Como atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Não adicione endereços IP públicos ao sistema operacional da VM.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config create](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[Add-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/add-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-ip-config"></a>Alterar configurações de endereço IP

Você pode precisar alterar o método de atribuição de endereço IP, alterar o endereço IP estático ou alterar o endereço IP público atribuído a uma NIC. Se você estiver alterando o endereço IP privado de uma configuração de IP secundária associada a uma NIC secundária em uma VM (saiba mais sobre [NICs primárias e secundárias](virtual-network-network-interface-vm.md#about)), coloque a VM no estado interrompido (desalocado) antes de concluir as etapas a seguir: 

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Interfaces de rede** exibida, clique na NIC que você deseja exibir cujas configurações de endereço IP você deseja alterar.
4. Clique em **Configurações de IP** na seção **CONFIGURAÇÕES** da folha para a NIC selecionada.
5. Clique na configuração de IP que você deseja modificar na lista da folha que será aberta para as configurações de IP.
6. Altere as configurações, conforme desejado, usando as informações sobre as configurações na etapa 6 da seção [Adicionar uma configuração de IP](#create-ip-config) deste artigo. Clique em **Salvar** para fechar a folha de configuração de IP alterada.

>[!NOTE]
>Se a NIC primária tiver várias configurações de IP e você alterar o endereço IP privado da configuração de IP primária, reatribua manualmente todos os endereços IP secundários para a NIC no Windows (não é necessário para Linux). Para atribuir manualmente endereços IP a uma NIC em um sistema operacional, leia o artigo [Como atribuir vários endereços IP a máquinas virtuais](virtual-network-multiple-ip-addresses-portal.md#os-config). Não adicione endereços IP públicos ao sistema operacional da VM.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config update](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRMNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/set-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="delete-ip-config"></a>Remover endereços IP

Você pode remover endereços IP públicos e privados de uma NIC, mas uma NIC deve sempre ter pelo menos um endereço IP privado atribuído a ela.

1. Faça logon no [portal do Azure](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos*, na parte superior do portal do Azure, digite *adaptadores de rede*. Quando o texto **adaptadores de rede** aparecer nos resultados da pesquisa, clique nele.
3. Na folha **Interfaces de rede** exibida, clique na NIC da qual deseja remover um endereço IP.
4. Clique em **Configurações de IP** na seção **CONFIGURAÇÕES** da folha para a NIC selecionada.
5. Clique com o botão direito do mouse em uma configuração de IP secundária (não é possível excluir a configuração primária) que você deseja excluir, clique em **Excluir** e, em seguida, clique em **Sim** para confirmar a exclusão. Se a configuração tem um recurso de endereço IP público associado a ela, o recurso é dissociado da configuração de IP, mas o recurso não é excluído.
6. Feche a folha das **configurações de IP**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network nic ip-config delete](/cli/azure/network/nic/ip-config?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmNetworkInterfaceIpConfig](/powershell/resourcemanager/azurerm.network/v3.4.0/remove-azurermnetworkinterfaceipconfig?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="next-steps"></a>Próximas etapas
Para criar uma VM com várias NICs ou endereços IP, leia os seguintes artigos:

**Comandos**

|Tarefa|Ferramenta|
|---|---|
|Criar uma VM com diversos NICs|[CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
||[PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
|Criar uma única VM da NIC com vários endereços IP|[CLI](virtual-network-multiple-ip-addresses-cli.md)|
||[PowerShell](virtual-network-multiple-ip-addresses-powershell.md)|

