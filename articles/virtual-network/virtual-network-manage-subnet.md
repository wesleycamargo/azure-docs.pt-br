---
title: Criar, alterar ou excluir sub-redes da rede virtual do Azure | Microsoft Docs
description: Saiba como criar, alterar ou excluir sub-redes da rede virtual.
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
ms.date: 05/10/2017
ms.author: jdial
ms.translationtype: Human Translation
ms.sourcegitcommit: 17c4dc6a72328b613f31407aff8b6c9eacd70d9a
ms.openlocfilehash: 38dcdf2f313c236a507e6a418c39812da16c6345
ms.contentlocale: pt-br
ms.lasthandoff: 05/16/2017


---
# <a name="create-change-or-delete-virtual-network-subnets"></a>Criar, alterar ou excluir sub-redes da rede virtual

Saiba como criar, alterar ou excluir sub-redes da VNet (rede virtual). Caso você não esteja familiarizado com VNets, é recomendável ler os artigos [Visão geral da rede virtual](virtual-networks-overview.md) e [Criar, alterar ou excluir redes virtuais](virtual-network-manage-network.md) antes de criar, alterar ou excluir sub-redes. Recursos do Azure que podem conectar-se a uma VNet são conectados a uma sub-rede em uma VNet. Várias sub-redes normalmente são criadas em uma VNet para:
- **Filtrar o tráfego entre sub-redes:** NSGs (grupos de segurança de rede) podem ser aplicados a sub-redes para filtrar o tráfego de rede de entrada e saída para todos os recursos (como máquinas virtuais) conectados à VNet. Para saber mais sobre como criar NSGs, leia o artigo [Criar grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
- **Controlar o roteamento entre sub-redes:** o Azure cria rotas padrão para que o tráfego seja automaticamente roteado entre sub-redes. Você pode substituir as rotas padrão do Azure criando UDRs (rotas definidas pelo usuário). Para saber mais sobre UDRs, leia o artigo [Rotas definidas pelo usuário](virtual-network-create-udr-arm-ps.md). 

Esse artigo explica como criar, alterar e excluir sub-redes de VNets criadas por meio do modelo de implantação do Azure Resource Manager.
 
## <a name="before"></a>Antes de começar

Conclua as seguintes tarefas antes de concluir as etapas em qualquer seção deste artigo:

- Caso você não esteja familiarizado com VNets e sub-redes no Azure, recomendamos que você conclua o exercício [Criação da sua primeira Rede Virtual do Azure](virtual-network-get-started-vnet-subnet.md) antes de ler este artigo. O exercício ajuda você a familiarizar-se melhor com VNets e sub-redes.
- Examine o artigo [Limites do Azure](../azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits) para saber mais sobre os limites de sub-redes e VNets.
- Faça logon no portal do Azure, na CLI (interface de linha de comando) do Azure ou no Azure PowerShell com uma conta do Azure. Caso ainda não tenha uma conta do Azure, inscreva-se para obter uma [conta de avaliação gratuita](https://azure.microsoft.com/free).
- Se você usar comandos do Azure PowerShell para concluir as tarefas neste artigo, primeiro [instale e configure o Azure PowerShell](/powershell/azureps-cmdlets-docs?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente dos cmdlets do Azure PowerShell instalada. Para obter ajuda com os comandos do PowerShell, com exemplos, digite `get-help <command> -full`.
- Se você usar comandos da CLI (interface de linha de comando) do Azure para concluir as tarefas neste artigo, primeiro [instale e configure a CLI do Azure](/cli/azure/install-azure-cli?toc=%2fazure%2fvirtual-network%2ftoc.json). Verifique se você tem a versão mais recente da CLI do Azure instalada. Para obter ajuda sobre os comandos da CLI, digite `az <command> --help`.


## <a name="create-subnet"></a>Criar uma sub-rede

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *redes virtuais*. Quando os resultados da pesquisa exibirem **Redes virtuais**, clique nessa opção.
3. Na folha **Redes virtuais** que aparece, clique na rede virtual à qual você deseja adicionar uma sub-rede.
4. No painel que aparece para a rede virtual selecionada, clique em **Sub-redes**.
5. Clique em **+ Subnet (+ Sub-rede)**.
6. Na folha **Adicionar sub-rede**, insira valores para os seguintes parâmetros:
    - **Nome:** o nome deve ser exclusivo na rede virtual.
    - **Intervalo de endereços:** o intervalo deve ser exclusivo dentro do espaço de endereço para a VNet e não pode sobrepor-se a outros intervalos de endereço de sub-rede dentro da VNet. O espaço de endereço deve ser especificado usando a notação CIDR. Por exemplo, em uma VNet com espaço de endereço 10.0.0.0/16, você pode definir um espaço de endereço de sub-rede de 10.0.0.0/24. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, definir uma sub-rede com um intervalo de endereços /29 resulta em três endereços IP utilizáveis na sub-rede. Se você planeja algum dia conectar uma VNet a um Gateway de VPN, uma sub-rede de gateway deve ser criada. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é criada. Para saber como alterar um intervalo de endereços de sub-rede, leia a seção [Alterar sub-rede](#change-subnet) deste artigo.
    - **NSG (Grupo de segurança de rede):** opcionalmente, você pode associar um NSG existente à sub-rede para controlar a filtragem de tráfego de rede de entrada e saída para a sub-rede. O NSG deve existir na mesma assinatura e local em que a VNet está, e deve ser criado por meio do modelo de implantação do Resource Manager. Para saber mais sobre como criar NSGs, leia o artigo [Grupos de segurança de rede](virtual-networks-create-nsg-arm-pportal.md).
    - **Tabela de rotas:** opcionalmente, você pode associar uma tabela de rotas existente à sub-rede a controlar o roteamento de tráfego de rede para outras redes. A tabela de rotas deve existir na mesma assinatura e local em que a VNet está, e deve ser criada por meio do modelo de implantação do Resource Manager. Para saber mais sobre como criar tabelas de rotas, leia o artigo [Rotas definidas pelo usuário](virtual-network-create-udr-arm-ps.md).
    - **Usuários**: você pode controlar o acesso à sub-rede usando funções internas ou suas próprias funções personalizadas. Para saber mais sobre como atribuir funções e usuários para que acessem a sub-rede, leia o artigo [Utilizar atribuição de função para gerenciar o acesso aos recursos do Azure](../active-directory/role-based-access-control-configure.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-access).
7. Clique no botão **OK** para adicionar a sub-rede à VNet selecionada.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network vnet subnet create](/cli/azure/network/vnet/subnet?toc=%2fazure%2fvirtual-network%2ftoc.json#create)|
|PowerShell|[New-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/new-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json), [Add-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/add-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="change-subnet"></a>Alterar as configurações de sub-rede

Você pode alterar o NSG, as tabelas de rotas e o acesso do usuário à sub-rede com recursos conectados a uma sub-rede. Para saber mais sobre essas configurações, leia a etapa 6 da seção [Criar uma sub-rede](#create-subnet) deste artigo. Para alterar o espaço de endereço de uma sub-rede, nenhum recurso pode ser conectado à sub-rede. Se houver recursos conectados à sub-rede, primeiro exclua os recursos conectados à sub-rede antes de alterar o intervalo de endereços. As instruções sobre como excluir um recurso variam de acordo com o recurso. Para saber como excluir recursos conectados a sub-redes, leia a documentação para cada tipo de recurso que você deseja excluir. Conclua as etapas a seguir para alterar o intervalo de endereços de uma sub-rede:

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal, digite *redes virtuais*. Quando os resultados da pesquisa exibirem **Redes virtuais**, clique nessa opção.
3. Na folha **Redes virtuais** que aparece, clique na VNet para a qual você deseja alterar um intervalo de endereços de sub-rede.
4. Clique na sub-rede para a qual você deseja alterar o intervalo de endereços.
5. Na folha que aparece para a sub-rede selecionada, insira o novo intervalo de endereços na caixa **Intervalo de endereços**. O intervalo deve ser exclusivo dentro do espaço de endereço para a VNet e não pode sobrepor-se a outros intervalos de endereço de sub-rede dentro da VNet. O espaço de endereço deve ser especificado usando a notação CIDR. Por exemplo, em uma VNet com espaço de endereço 10.0.0.0/16, você pode definir um espaço de endereço de sub-rede de 10.0.0.0/24. O menor intervalo que você pode especificar é de /29, que fornece oito endereços IP para a sub-rede. O Azure reserva o primeiro e o último endereço em cada sub-rede para conformidade de protocolo. Três endereços adicionais são reservados para uso pelo serviço do Azure. Como resultado, uma sub-rede com um intervalo de endereços /29 tem três endereços IP utilizáveis. Se você planeja algum dia conectar uma VNet a um Gateway de VPN, uma sub-rede de gateway deve ser criada. Saiba mais sobre [considerações de intervalo de endereços específico para sub-redes de gateway](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#a-namegwsubagateway-subnet). Sob condições específicas, você pode alterar o intervalo de endereços depois que a sub-rede é criada. Para saber como alterar um intervalo de endereços de sub-rede, leia a seção [Alterar sub-rede](#change-subnet) deste artigo.
6. Clique em **Salvar**.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network vnet subnet update](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#update)|
|PowerShell|[Set-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/set-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|


## <a name="delete-subnet"></a>Excluir uma sub-rede

Você poderá excluir uma sub-rede apenas se não houver recursos conectados a ela. Se houver recursos conectados à sub-rede, primeiro exclua esses recursos. As instruções sobre como excluir um recurso variam de acordo com o recurso. Para saber como excluir recursos conectados a sub-redes, leia a documentação para cada tipo de recurso que você deseja excluir.

1. Faça logon no [portal](https://portal.azure.com) com uma conta que tenha, no mínimo, permissões para a função de Colaborador de rede para a sua assinatura. Leia o artigo [Funções internas para o controle de acesso baseado em função do Azure](../active-directory/role-based-access-built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) para saber mais sobre como atribuir funções e permissões às contas.
2. Na caixa que contém o texto *Pesquisar recursos* na parte superior do portal do Azure, digite *redes virtuais*. Quando os resultados da pesquisa exibirem **Redes virtuais**, clique nessa opção.
3. Na folha **Redes virtuais** que aparece, clique na VNet da qual deseja excluir uma sub-rede.
4. Na folha que aparece para a VNet selecionada, clique em **Sub-redes** em **Configurações**.
5. Na lista de sub-redes que aparece na folha de sub-redes, clique com o botão direito do mouse na sub-rede que deseja excluir, clique em **Excluir** e, em seguida, clique em **Sim** para excluir a sub-rede.

**Comandos**

|Ferramenta|Command|
|---|---|
|CLI|[az network vnet delete](/cli/azure/network/vnet?toc=%2fazure%2fvirtual-network%2ftoc.json#delete)|
|PowerShell|[Remove-AzureRmVirtualNetworkSubnetConfig](/powershell/module/azurerm.network/remove-azurermvirtualnetworksubnetconfig?view=azurermps-3.8.0?toc=%2fazure%2fvirtual-network%2ftoc.json)|

## <a name="next-steps"></a>Próximas etapas

Para criar uma VM e conectá-la a uma sub-rede, leia o artigo [Criar uma VNet e conectar VMs](virtual-network-get-started-vnet-subnet.md#a-namecreate-vmsacreate-virtual-machines).
